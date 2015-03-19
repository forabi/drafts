ما الجديد في الإصدار القادم من JavaScript؟ (الجزء الثّاني)
==============================================

##التفكيك Destructuring
ذكرنا في الجزء السابق أن اهتمامًا كبيرًا أُوليَ لتسهيل كتابة الشفرة وقراءتها في ECMAScript 6، و**الإسناد بالتفكيك (Destructuring assignment)** لا يخرج عن هذا السياق، وهو ليس بالمفهوم الجديد في عالم البرمجة، فهو معروف في Python وفي Ruby. بعيدًا عن تعقيدات المصطلحات، إليك هذا المثال:

```javascript
var [a, b, c] = [1, 2, 3];
a == 1 // true
b == 2 // true
c == 3 // true
```

ما الذي يحدث هنا؟ بكل بساطة تسمح ECMAScript 6 بصياغة جديدة للتعريف عن المتغيرات أو إسناد قيم جديدة إليها جُملةً واحدة من خلال جمعها ضمن قوسي مصفوفة (Array) وسيقوم مُفسّر اللغة بإسناد قيمة مقابلة لكل متغيّر من المصفوفة الواقعة على يمين مُعامل الإسناد (`=`). الأمر لا يقتصر على إسناد المصفوفات، بل يمكن أيضًا إسناد خصائص العناصر:

```javascript
let person = { firstName: "John", lastName: "Smith", Age: 42, Country: "UK" };

let { firstName, lastName } = person;

console.log(`Hello ${ firstName } ${ lastName }!`); // Hello John Smith!
```

في هذا المثال لدينا متغيّرات تتبع للنطاق العامّ `firstName` و`lastName`، وقد أسندنا لها قيمًا من خصائص الكائن `person`، حيث يبحث مفسّر اللّغة عن خصائص في الكائن `person` يماثل اسمها اسم المتغيّر المفروض ويُسندها إلى المُتغيّرات. يمكن توضيح المقصود بصورة أفضل إذا أعدنا كتابة الشفرة لتتوافق مع الإصدار الحالي من JavaScript:

```javascript
var person = { firstName: "John", lastName: "Smith", Age: 42, Country: "UK" };

var firstName = person.firstName;
var lastName = person.lastName;

console.log("Hello " + firstName + " " + lastName + "!"); // Hello John Smith!
```

يشيع استخدام التفكيك في CoffeeScript (وهي لغة أقر Brendan Eich مُخترع JavaScript بأنّ الإصدار الأخير من JavaScript استوحى الكثير منها)، وخصوصًا عندما تُنظّم البرامج في وحدات كما في Node.js ويكون اهتمامًا مُقتصرًا على استيراد جزء مُحدّد من الوحدة المعنيّة:

```coffeescript
{ EventEmitter } = require 'events'
{ EditorView } = require 'atom'
{ compile } = require 'coffee-script'


compile('# coffeescript code here');
```

عند تحويل هذا النص إلى JavaScript الحالية، سنحصل على:

```javascript
var EventEmitter = require('events').EventEmitter;
var EditorView = require('atom').EditorView;
var compile = require('events').compile;

compile('# coffeescript code here');
```

من الاستخدامات المفيدة للإسناد بالتفكيك التبديل بين قيمتي متغيّرين بصورة سهلة، سنقتبس المثال من توثيق CoffeeScript ونُعيد كتابته بـJavaScript:

```javascript
var theBait   = 1000;
var theSwitch = 0;

[theBait, theSwitch] = [theSwitch, theBait];

```

قبل ES6 كنا لنحتاج لكتابة مُتغيّر مؤقّت نخزن فيه قيمة إحدى المتغيّرين للاحتفاظ بها قبل التبديل بين القيمتين، وهو ما يفعله محوّل CoffeeScript بالفعل ليعطينا شفرة JavaScript متوافقة مع الإصدار الحالي (مع أنه يقوم بتخزين كلا القيمتين في مصفوفة، إلا أنّ الفكرة تبقى ذاتها):


```javascript
var theBait, theSwitch, _ref;

theBait = 1000;

theSwitch = 0;

_ref = [theSwitch, theBait], theBait = _ref[0], theSwitch = _ref[1];
```


##المُكرِّرات (Iterators) وحلقة `for... of`
ما من لغة برمجة تخلو من وسيلة للمرور على عدد من القيم و__تكرار__ تنفيذ عمليّة معيّنة على هذه القيم، من أبسط هذه الوسائل حلقة `for` التقليديّة الشّهيرة، وفي JavaScript يشيع استخدام حلقة `for... in` إلى جانبها للمرور على أسماء خصائص العناصر، إذ يمكننا معرفة كل خصائص العنصر `document` بسطرين فقط:

```javascript
for (var propertyName in document) {
    console.log(propertyName);
}

// "body"
// "addEventListener"
// "getElementById"
// ...
```

لاحظ أن حلقة `for... in` تُعيد **أسماء** خصائص العنصر (كسلسة نصيّة String)، والأمر لا يستثني المصفوفات، فهي ليست سوى كائنات بأسماء خصائص توافق رقم الفهرس (Index):

```javascript
for (var i in [1, 2, 3]) {
  console.log(i);
}

// "0"
// "1"
// "2"
```

من عيوب حلقة `for... in` أن لا شيء في تعريف اللغة يُجبر مُفسّر اللّغة على إخراج العناصر بترتيب ثابت بالضّرورة، وهذا يعني أنها تصبح مباشرة غير صالحة للمرور على المصفوفات - التي تستخدم لحفظ عناصر _مُرتّبة_ - بطريقة بديهيّة، ويحلّ محلّها حلقة `for` التقليديّة عندئذٍ، وأمّا عند استخدامها للمرور على الكائنات، فإنّها لا تُعيد إلّا الخصائص الّتي تُعرّف على أنها قابلة للتعداد (enumerable)، وهو شيء يُحدّد عند تعريف الخاصّة، كما أنّها تُعيد الخصائص القابلة للتعداد التي ورثها الكائن عن "آباءه" ضمن سلسلة الوراثة، وهو تصرّف قد لا يكون مرغوبًا دومًا، وغالبًا سترى المطوّرين يُجرون فحصًا للخاصّة قبل متابعة تنفيذ الشفرة لمعرفة ما إذا كانت تخصّ العنصر ذاته أمّ أنّه ورثها:

```javascript
var obj = { a: 1, b: 2, c: 3 }; // كائن جديد لا يرث سوى النموذج Object
    
for (var prop in obj) {
  console.log("o." + prop + " = " + obj[prop]);
}

// "o.a = 1"
// "o.b = 2"
// "o.c = 3"
```

في هذا المثال (المنقول عن [شبكة مطوّري موزيلا](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...in)) فرضنا كائنًا جديدًا بثلاث خصائص، وعند المرور عليه بحلقة `for... in` فإنّنا حصلنا على النتيجة المتوقّعة، ولم نحصل على خصائص إضافيّة لأنّ الكائن الذي فرضناه لا يرث أي كائن آخر سوى `Object` (الذي ترثه كل الكائنات افتراضًا). أما في المثال التالي، فقد احتجنا لإجراء اختبار `hasOwnProperty` على العنصر الوارث لكي لا تظهر سوى الخاصة `color` التي يملكها بذاته ولم يرثها:

```javascript
var triangle = {a: 1, b: 2, c: 3};

function ColoredTriangle() {
  this.color = "red";
}

ColoredTriangle.prototype = triangle;

var obj = new ColoredTriangle();

for (var prop in obj) {
  if (obj.hasOwnProperty(prop)) {
    console.log("o." + prop + " = " + obj[prop]);
  } 
}

// Output:
// "o.color = red"
```


حسنًا، لقد أطلنا الحديث عن حلقة `for... in` وهي ليست بالجديدة؛ لكنّنا أصبحنا نرى الحاجة لشيء جديد أكثر بساطة ومرونة، فهذا ما تبتغيه ES6 في النهاية، ولهذا نشأت فكرة المُكرّرات؛ التي تسمح لأي عنصر بأن يختار لنفسه الطّريقة التي يتصرّف بها عند المرور به في حلقة، ومع المُكرّرات لا بدّ من نوع جديد من الحلقات لتلبية هذه الحاجة والمحافظة على حلقة `for... in` للتوافق مع الإصدارات القديمة. من هنا نشأت حلقة `for... of` الجديدة.

```javascript
for (var num of [1, 2, 3]) {
    console.log(num);
}

// 1
// 2
// 3


for (var node of document.querySelectorAll('a')) {
  console.log(node);
}

// <a class="title" href="/">
// <a class="contact" href="/contact/">

```

حصلنا في المثالين السابقين على __قيمة__ الخاصّة وليس اسم الخاصّة، لكنّ هذا لا يعني أنّ حلقة `for... of` تُعيد قيم الخصائص دومًا، بل إنّها تستدعي **مُكرّر** الكائن (`Iterator`) وتطلب منه في كلّ دورة للحلقة تزويدها بشيء ما، وتترك للمُكرّر الحُريّة بإعادة أي قيمة يرغب بها، ولكن ولأنّنا نستدعي في مثالنا مصفوفة أولاً، وعنصر من نوع `NodeList` ثانيًا، وكلا النّوعين يُعيد مُكرُّرهما قيمَ العناصر في المصفوفة، فإنّنا نحصل على تلك النتيجة البديهيّة. بإمكاننا إنشاء أصناف بمُكرّرات خاصّة نُنشئها بأنفسنا، ولنفترض أن لدينا نوعًا لصفّ ضمن مدرسة ابتدائية، ونريد أن نحصل على تفاصيل الطلّاب على هيئة نص مُنسّق عند المرور على الصّفّ في حلقة `for... of`:

```javascript

function SchoolClass(students) {
   this.students = students;
}

SchoolClass.prototype[Symbol.iterator] = function*() {
    for (let i = 0; i < this.students.length; i++) {
        let student = this.students[i];
        yield `#${i+1} ${student.name} (${student.age} years old)`;
    }
}

var ourClass = new SchoolClass([ { name: "Ahmed", age: 10 }, { name: "Alaa", age: 9 }/*, ...*/ ]);
for (student of ourClass) {
     console.log(student);
}

// "#1 Ahmed (10 years old)"
// "#2 Alaa (9 years old)"
// "#3 ..." ...

```

استخدمنا الرمز الخاص `Symbol.iterator` لإسناد دالّة مُولّد (Generator function) التي تُعطينا عند استدعائها نسخة من مُكرّر الصنف المُخصّص الذي أنشأناه. سنتعرف بعد قليل على المُولّدات (Generators) وكذلك على الرموز (Symbols) في وقت لاحق. لاحظ أنّنا استخدمنا حلقة `for... of` للمرور على محتويات `ourClass`.

تذكّر أنّنا استخدمنا هذه الحلقة في الجزء السابق مع Array Comprehension، كما في المثال:


```javascript
let people = ["Samer", "Ahmed", "Khalid"];
console.log([`Hello ${person}` for (person of people)]);
```

إن كانت الفقرة الأخيرة غامضة بعض الشيء فلا تقلق، سنتوسّع بشرح المولّدات بعد قليل.

لكن دعونا نتوقّف قليلاً ولننتقل إلى الجانب الفلسفي لهذه الإضافات في JavaScript، قد تبدو للوهلة الأولى تعقيدات بلا طائل، خصوصًا وأنّ كثيرًا منها لا يهدف سوى للتسهيل، ولا يقدّم شيئًا يستحيل إنجازه بالإصدارات السابقة من اللغة؛ هنا يمكن الرّدّ بأنّ تطوّر اللغة متعلّق بكيفيّة استخدامها والخبرة التي تُكتسب مع مرور السّنين، حيث تظهر للمطوّرين حاجات جديدة وأفكار تطبّق مرارًا لدرجة أنها ترتقي لتصبح ضمن أساسات اللغة. سهولة كتابة الشفرة لم تعد رفاهية، بل هي ضرورة لإنجاز المشاريع الكبيرة لأنّها تتيح اختصار الوقت الذي كان سيضيع في كتابة متكرّرة ومُملّة، كما أنّها تُلبّي ما يتوقّعه المطوّرون من لغة أصبحت تؤخذ على محمل الجدّ وتُستخدم في تطوير تطبيقات ضخمة ومُعقّدة بعد أن كان جُلّ استخدامها تنفيذ بعض المهام البسيطة.



##المُولِّدات (Generators)
المولدات (Generators) ببساطة هي دوال يمكن إيقافها والعودة إليها في وقت لاحق مع الاحتفاظ بسياقها دون تغيير، صياغة دوال المولدات لا تختلف كثيرًا عن صياغة الدوال التقليدية، كل ما عليك هو إضافة إشارة * بعد `function` واستخدام `yield` بدل `return`، المثال التالي سيوضح فكرة المولدات أكثر:


```javascript
function* getName() {
  let names = ['Muhammad', 'Salem', 'Abdullah'];
  for (name of names) {
    yield name;
  }
}

let nameGenerator = getName();
nameGenerator.next().value; // 'Muhammad'
nameGenerator.next().value; // 'Salem'
nameGenerator.next().value; // 'Abdullah'
nameGenerator.next().value; // undefined

}
```

ما الذي يحدث هنا؟ فرضنا دالّة مولّد (Generator function) (والتي تُميّز بإشارة النجمة `*`) سمّيناها `getName`، وفيها صرحنا عن مصفوفة فيها أسماء، وظيفة هذه الدالة أن تعطينا عند استدعائها نسخة من مُكرّر (Iterator) (الذي شرحناه لتوّنا)، يزوّدنا بالأسماء بالترتيب في كل مرة نستدعيه فيها ليعطينا النتيجة التالية (`next()`)، أولاً يجب حفظ نسخة المُكرّر ضمن متغير لكي نسمح له بحفظ حالته، ودون ذلك سيعطينا استدعاء دالّة المولد مباشرةً `getName().next()` دوماً النتيجة الأولى لأننا عملياً نُنشئ نسخة جديدة عنه في كل مرة نستدعيه، أما استدعاء نسخة عنه وحفظها في متغير مثل `myGenerator` فيسمح لنا باستدعاء `.next()` عليها كما هو متوقع. لا ترجع الدالة `.next()` القيمة التي نرسلها عبر `yield` فقط، بل ترجع كائناً يحوي القيمة المطلوبة ضمن الخاصة `value`، وخاصة أخرى `done` تسمح لنا بمعرفة ما إذا كان المولد قد أعطانا كل شيء.

لنُعِدْ ترتيب أفكارنا:

1. المولّدات تسمح **بتوقّف تنفيذها مع الاحتفاظ بحالة التنفيذ** (يحدث توقّف التنفيذ عند كلّ كلمة `yield`). فلو أنّنا كتبنا دالّة تقليديّة في المثال أعلاه مع `return` بدل `yield` لحصلنا في كلّ مرّة على الاسم الأول (Muhammad). وهذه الميزة في المولّدات يمكن *استغلالها لإنشاء حلقات لا نهائية* دون إعاقة متابعة البرنامج:

	```javascript
	function* numberGenerator() {
	  for (let i = 0; true; i++) {
	     yield i;
	  }
	}
	
	let numGen = numberGenerator();
	numGen.next(); // { value: 0, done: false }
	numGen.next(); // { value: 1, done: false }
	numGen.next(); // { value: 2, done: false }
	numGen.next(); // { value: 3, done: false }
	// ...
	
	```

2. دوالّ المولّدات تُعطي عند استدعاءها **مُكرّرات**، وهذا يعني إمكانيّة استخدامها في حلقة `for... of` (احذر من تطبيق مثال كهذا على مولّد غير منتهٍ كما في المثال السابق!):
	
	```javascript
	for (let name of getName()) {
	  console.log(name);
	}
	
	// "Muhammad"
	// "Salem"
	// "Abdullah"
	
	```

3. لكلّ مُكرّر وظيفة `.next()` مهمّتها بدء تنفيذ الدّالّة أو متابعة تنفيذها ثم إيقافها مؤقّتًا عند كلّ كلمة `yield`.
4. استدعاء `next()` على المُكرّر يعيد لنا في كلّ مرة كائنًا ذا خاصّتين: الأولى `value` وهي أيّ شيء نُعيده بكلمة `yield`، والثّانية `done` وهي قيمة منطقيّة (Boolean) تشير إلى حالة انتهاء تنفيذ الدّالة.

تقبل الوظيفة `.next()` للمُكرّرات مُعاملاً اختياريًّا تستقبله وتُرسله لدالّة المولّد بعد متابعة التنفيذ، ويمكن استخدامها لإرسال رسائل لدالّة المولّد بحيث نؤثّر في تنفيذه:

```javascript
function* numberGenerator() {
  for (let i = 0; true; i++) {
     var reset = yield i;
     if (reset) i = -1;
  }
}

let numGen = numberGenerator();
numGen.next(); // { value: 0, done: false }
numGen.next(); // { value: 1, done: false }
numGen.next(); // { value: 2, done: false }
numGen.next(); // { value: 3, done: false }

numGen.next(true); // { value: 0, done: false }

```

في هذا المثال مرّرنا القيمة `true` إلى الوظيفة `.next()` على المُكرّر، والذي بدوره يُرسلها لدالّة المولّد كنتيجة `yeild i` في الدّورة الموافقة للحلقة، لنقومَ بحفظها في متغيّر `reset` ونُجريَ فحصًا عند متابعة التنفيذ لإعادة تعيين قيمة `i`، التي ستزداد بمقدار واحد مع بدء الدورة التالية لحلقة `for` جاعلةً قيمة `i` مساوية للصّفر.

خصائص المولّدات تجعلها مناسبة جدًا لكتابة شيفرة غير متزامنة بصورة أسهل تكاد تبدو فيها وكأنها شيفرة متزامنة خالية من الاستدعاءات الراجعة المتداخلة (Nested callbacks)؛ هذه الفكرة تحتاج إلى تركيز لأنها أساس لعدد من المكتبات مثل [co](https://github.com/tj/co) و[suspend](https://github.com/jmar777/suspend) التي ظهرت مؤخّرًا وتصاعدت شعبيّتها بسرعة لأنّها تحلّ مشكلة جوهرية في استخدام JavaScript، ألا وهي التعامل مع الدوال غير المتزامنة (asynchronous functions) وذلك بالاعتماد كُليًّا على المُولّدات.

لنفترض أنّ لدينا موقعًا لقراءة الكتب يعرض ملفّ المستخدم الشّخصيّ مع عدد الكتب التي قرأها وعنوان آخر كتاب مع تقييم المستخدم له:

```javascript

var list = document.querySelector("#book-list");

getJSON("http://reading-website.com/users/fwz.json", function(err, user) {
    if (err) return; // افعل شيئًا بما بخصوص الخطأ
    
    var num_books = user.books.length;
    var most_recent_book_id = user.books[num_books - 1];
    
    getJSON("http://reading-website.com/users/fwz/ratings/" + most_recent_book_id + ".json", function(err, user_rating) {
        getJSON("http://reading-website.com/books/" + most_recent_book_id + ".json", function(err, book) {
            var fragment = document.createDocumentFragment();
            
            var h2 = document.createElement("h2");
            h2.textContent = user.full_name;
            
            var h3 = document.createElement("h3");
            h3.textContent = "الكتب التي قرأها";

            for (let book of books) {
                let li = document.createElement("li");
                li.textContent = book.title + (book.id == most_recent_book_id ? " " + user_rating : "");
                fragment.appendChild(li);
            }
            
            list.appendChild(fragment);
        });
    });
    
})

```

في المثال السابق احتجنا إلى إرسال 3 طلبات AJAX يعتمد أحدها على الآخر، ولأنّنا لا نستطيع إرسال طلب بتقييم المستخدم للكتاب قبل معرفة مُعرّف الكتاب، فلا بدّ من أن يرسل الطلب الخاصّ بتقييم الكتاب ضمن الاستدعاء الرّاجع لطلب معلومات المستخدم، ثمّ يمكن جلب عنوان الكتاب ضمن الاستدعاء الرّاجع للطلب السّابق، وهذا يعني زيادة تعقيد الشفرة مع تداخل الاستدعاءات الرّاجعة لتبدو أشبه بسباغيتي لا تُعرف بدايته من نهايته.

تخيّلوا -لغرض التّخيّل- لو أمكننا كتابة هذه الشفرة (وهي غير متزامنة) لتبدو لقارئها وكأنها نص برمجي يسير بترتيب متزامن وبديهيّ... ألن يكون هذا أعظم شيء منذ اختراع JavaScript؟

```javascript
var list = document.querySelector("#book-list");

try {
   var user = getJSON("http://reading-website.com/users/fwz.json");
   
   var num_books = user.books.length;
   var most_recent_book_id = user.books[num_books - 1];
   
   var user_rating = getJSON("http://reading-website.com/users/fwz/ratings/" + most_recent_book_id + ".json");
   var book = getJSON("http://reading-website.com/books/" + most_recent_book_id + ".json");
   
   var fragment = document.createDocumentFragment();
            
   var h2 = document.createElement("h2");
   h2.innerText = user.full_name;
            
   var h3 = document.createElement("h3");
   h3.innerText = "الكتب التي قرأها";

   for (let book of books) {
       let li = document.createElement("li");
       li.innerText = book.title + (book.id == most_recent_book_id ? " " + user_rating : "");
       fragment.appendChild(li);
   }
            
   list.appendChild(fragment);
   
} catch (e) {
   // افعل شيئًا بما بخصوص الخطأ
   // Error
}


```

نحن نعلم أن الأمور لا يمكن أن تكون بهذه الروعة، وأنّ الشفرة أعلاه لن تعمل... نحن نعلم أن شيفرتنا تحتاج تفاصيل المستخدم للحصول على الكتب، وأننا نحتاج للكتاب لجلب عنوانه وتقييمه، وتنفيذ هذه المهمّات بشكل غير متزامن لا يعني أنّه ليس علينا **انتظار** المهمّة الأولى قبل إطلاق الثانية - بل يعني فقط أن المتصفح يمكنه تنفيذ رسم العناصر الأخرى وعرض الصفحات وإرسال طلبات أخرى في هذا الوقت.

حسنًا، لدي خبر جيّد وآخر سيئ: أمّا الجيّد فهو أنّنا كتابة شيفرة شبيه بهذه أصبحت قريبة المنال مع الدّوالّ غير المتزامنة (Async Functions)، وأمّا الخبر السيّئ فهو أنّ علينا الانتظار إلى الإصدار 7 من ECMAScript لنستطيع كتابتها! (مع العلم أن المتصفّحات لم تنتهِ من تطبيق ES6!).

لكن هذا لا يعني أن نقف مكتوفي الأيدي إلى أن تصدر ES7، بل بإمكاننا إيجاد حلّ وسط لهذه المشكلة؛ لماذا نضطّر إلى تعقيد الأمور بالاستدعاءات الرّاجعة المتداخلة؟ ألا يتوفّر في اللّغة بنية برمجيّة تسمح بإيقاف شيفرتنا ريثما يتمّ أمر ما غير متزامن (**الانتظار** لإكمال طلب AJAX) ثمّ **المتابعة** بعد انتهاءه؟ يبدو هذا الحديث مألوفًا!

نعلم حتى الآن أننا بحاجة لاستخدام مولّد، ولذلك سنحيط شيفرتنا بدالّة مولّد كخطوة أولى:

```javascript

var list = document.querySelector("#book-list");

function* displayUserProfile() {
    // شيفرتنا هنا
}

```

الآن نحتاج لتنفيذ طلب AJAX الأوّل والانتظار إلى انتهاءه قبل الانتقال إلى الطّلب الثّاني، نعلم أنّ `yield` توقف تنفيذ المولّد:

```javascript

var list = document.querySelector("#book-list");

function* displayUserProfile() {
    yield getJSON("http://reading-website.com/users/fwz.json");
    // ...
}

```

عظيم! لكن كيف نُخبر المولّد بأنّ عليه متابعة التنفيذ؟

```javascript
var list = document.querySelector("#book-list");

function* displayUserProfile() {
    yield getJSON("http://reading-website.com/users/fwz.json", resume);
    // ...
}

```

سنمرر دالة اسمها `resume` للدّالة `getJSON`، وهذه الدالة ستُستدعى عند انتهاء جلب جواب الطّلب الذي أرسلناه، وهي فرصتنا لإخبار المولّد بمتابعة التنفيذ... فكيف سيكون محتواها؟

```javascript
var list = document.querySelector("#book-list");

var resume = function(err, response) {
     displayIterator.next(response);
}

function* displayUserProfile() {
    var user = yield getJSON("http://reading-website.com/users/fwz.json", resume);
    var num_books = user.books.length;
    var most_recent_book_id = user.books[num_books - 1];
   
    var user_rating = yield getJSON("http://reading-website.com/users/fwz/ratings/" + most_recent_book_id + ".json", resume);
    var book = yield getJSON("http://reading-website.com/books/" + most_recent_book_id + ".json", resume);
   
    var fragment = document.createDocumentFragment();
            
    var h2 = document.createElement("h2");
    h2.innerText = user.full_name;
            
    var h3 = document.createElement("h3");
    h3.innerText = "الكتب التي قرأها";

    for (let book of books) {
        let li = document.createElement("li");
        li.innerText = book.title + (book.id == most_recent_book_id ? " " + user_rating : "");
        fragment.appendChild(li);
    }
            
    list.appendChild(fragment);
}

var displayIterator = displayUserProfile();
displayIterator.next();

```

حفظنا نسخة عن المكرّر في متغيّر ثم استدعينا وظيفته `next()` في دالّة المتابعة، ممرّرين لها جواب الطّلب ليمكننا تخزينه ضمن المتغيّر `user`. الدّالة resume تستطيع الوصول إلى `displayIterator` لأنّه يكون معرّفًا قبل استدعاءها حتمًا، ولا ننسَ أن تعريف المتغيّرات في JavaScript يخضع لعملية [الرّفع إلى أعلى النّطاق (variable hoisting)](http://www.w3schools.com/js/js_hoisting.asp) ممّا يجعل المتغيّر `displayIterator` موجودًا (وإن كان بلا قيمة) منذ بداية تنفيذ الشيفرة.

للتأكّد من فهم هذه الشيفرة، سنعيد تحليلها خطوة بخطوة: في طلب AJAX الأوّل تستدعى الدالة `resume` ويمرّر إليها جواب الطّلب (`response`)، الذي يمرّر بدوره إلى المُكرّر ليُحفظ في المتغيّر `user` الذي سيُستخدم في الخطوة التّالية للمولّد لإرسال الطّلب الثّاني. تُكرّر العمليّة ذاتها للطلبين الآخرين ثمّ تُعرض النتائج في الصّفحة. الفائدة التي جنيناها من استخدام المولّدات هي التخلّص من تعقيد الاستدعاءات الرّاجعة نهائيًّا وتحويل شيفرة غير متزامنة وجعلها تبدو وكأنّها متزامنة. ذكرنا القليل عن مكتبات مثل co وsuspend، لكنّها باختصار تعمل بطريقة مماثلة جدًا لمثالنا الأخير:

```
var suspend = require('suspend'),
    resume = suspend.resume;

suspend(function*() {
    var data = yield fs.readFile(__filename, 'utf8', resume());
    console.log(data);
})();

```

هذه المكتبات خطوة نحو مستقبل JavaScript، الذي بدأ يتشكل مع مشروع الدّوال غير المتزامنة باستخدام الكلمتين المفتاحيتين الجديدتين `async` و`await` اللّتان ستتوفّران في الإصدار السّابع وتستندان في عملهما إلى أرضيّة الوعود (Promises) الّتي تتوفّر اليوم في ES6. سيكون بإمكاننا كتابة هذه الشيفرة بدل الاعتماد على المولّدات:

```javascript
async function displayUserProfile() {
   var user = await getJSON("http://reading-website.com/users/fwz.json");
   
   var num_books = user.books.length;
   var most_recent_book_id = user.books[num_books - 1];
   
   var user_rating = await getJSON("http://reading-website.com/users/fwz/ratings/" + most_recent_book_id + ".json");
   var book = await getJSON("http://reading-website.com/books/" + most_recent_book_id + ".json");
   
   var fragment = document.createDocumentFragment();
            
   var h2 = document.createElement("h2");
   h2.innerText = user.full_name;
            
   var h3 = document.createElement("h2");
   h2.innerText = "الكتب التي قرأها";

   for (let book of books) {
       let li = document.createElement("li");
       li.innerText = book.title + (book.id == most_recent_book_id ? " " + user_rating : "");
       fragment.appendChild(li);
   }
            
   list.appendChild(fragment);
}

```
في هذا المثال يجب على `getJSON` أن تُعيد وعدًا `Promise` ليستطيع مُفسّر اللّغة انتظاره إلى أن يُحقّق (resolve) أو يُرفض (reject)، والقيمة الّتي تُحقّق تُحفظ ضمن المُتغيّر `user`، وأما عند رفض الوعد يُرمى خطأ (`throw`) يمكن تلقّيه (`catch`) كما في الشيفرة غير المتزامنة.

##مُعامِل البقيّة (Rest parameter) والناشرة (Spread)
بعد كلّ هذا الكلام المُعقّد عن الأشياء غير المتزامنة التي نريد جعلها تبدو متزامنة وما إلى ذلك، سنختم الجزء الثّاني بفكرتين بسيطتين أُضيفتا إلى ECMAScript في الإصدار السّادس وتحلّان مشكلتين شائعتين في كثير من اللّغات البرمجيّة:

أمّا الأولى فهي الحاجة إلى تنفيذ نصّ برمجيّ ضمن دالة على عدد غير معروف من المُعاملات، فلنفترض أنّ لدينا دالة تجمع عددين:

```javascript
function add(n1, n2) {
    return n1 + n2;
}

add(1)
// 1
add(1, 2)
// 3

```

ونظرًا لكوننا مبرمجين أذكياء فقد قرّرنا جعل الدّالة تقبل أي عددين أو ثلاثة أو أكثر... لنجعلها تقبل عددًا لا نهائيًّا من الأعداد؛ في الإصدار الحالي سنلجأ إلى استخدام الكائن الخاصّ `arguments` المتوفّر ضمن نطاق كلّ دالّة تلقائيًا:

```javascript
function add() {
   return [].reduce.call(arguments, function(memo, n) { return memo + n; });
}

add(1)
// 1
add(1, 2)
// 3
add(1, 2, 3)
// 6

```

حسنًا لقد اضطررنا إلى "استعارة" دالة الاختزال من مصفوفة فارغة لتطبيقها على الكائن الخاص `arguments` الذي يُعتبر "شبيه مصفوفة" ولا يملك ما تمتلكه المصفوفة من دوالّ، لماذا لا يمكننا كتابة هذا فحسب:

```javascript
function add(...numbers) {
    return numbers.reduce(function(memo, n) { return memo + n; });
}

add(1)
// 1
add(1, 2)
// 3
add(1, 2, 3)
// 6

```

وأمّا الفكرة الثّانية فهي تكاد تكون عكس السّابقة، فإذا كانت الأولى تجمع بقيّة المعاملات في كائن مُفرَد، فإنّ هذه "تَنشر" محتويات المصفوفة إلى عناصرها المكوّنة لها، ماذا لو لم نكن أذكياء وعجزنا عن الإتيان بدالة تجمع عددًا غير منتهٍ من الأرقام:

```javascript
function addThreeNumbers(n1, n2, n3) {
     return n1 + n2 + n3;
}

var myNumbers = [1, 2, 3];
addThreeNumbers(...myNumbers);

// 6

```

لاحظ أن صياغة النشر (Spread) تطابق تمامًا صياغة البقيّة (Rest)، والاختلاف في السّياق فقط. لاحظ أيضًا أنّ معامل البقيّة، وكما يوحي اسمه، يمكن استخدامه لتجميع _ما تبقى_ من مُعاملات الدّالة فقط:

```javascript
function addThreeOrMoreNumbers(n1, n2, ...numbers) {
    return n1 + n2 + numbers.reduce(function(memo, n) { return memo + n; });
}

addThreeOrMoreNumbers(1, 2, 3);
// 6

```

في الجزء القادم سنتعرف بمشيئة الله على الوحدات (Modules) التي تُعتبر طريقة جديدة لتنظيم الشفرة اُستلهمَت من عالم Node.js وrequire.js، وسُنلقي نظرة على الأصناف (Classes)، المكوّن البرمجيّ الذي وجد طريقه أخيرًا إلى JavaScriptّ!

###المصادر
1. [شبكة مطوّري موزيلّا](https://developer.mozilla.org)
2. [Going Async With ES6 Generators](http://davidwalsh.name/async-generators)
3. [Replacing callbacks with ES6 Generators ](http://modernweb.com/2014/02/10/replacing-callbacks-with-es6-generators)
4. [Iterators gonna iterate](http://jakearchibald.com/2014/iterators-gonna-iterate/)
