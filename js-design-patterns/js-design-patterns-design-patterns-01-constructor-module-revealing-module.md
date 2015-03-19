أنماط التّصميم في JavaScript
=========================
سنستعرض في هذا القسم تطبيقًا لعدد من أنماط التّصميم القديمة والحديثة في JavaScript.

كثيرًا ما يتساءل المطوّرون إذا ما كان هناك نمط أو مجموعة من الأنماط _المثاليّة_ الّتي ينبغي استخدامها في سياق عملهم. لا إجابة منفردة بذاتها على هذا السؤال، فالغالب أنّ كلّ نصّ برمجيّ وكلّ تطبيق ويب نعمل عليه يتطلّب شروطًا خاصّة، وعلينا أن نختار نمط التّصميم الذي نعتقد أنّه سيقدّم قيمة حقيقة عند تبنّيه؛ فبعض المشاريع قد تستفيد مّما يقدّمه نمط المُراقِب (Observer) من خواصّ الفصل بين المكوّنات (إذ يقلّل هذا النّمط من درجة اعتماد أحد أجزاء التّطبيق على الأجزاء الأخرى)، وقد تكون مشاريع أخرى صغيرة جدًا لدرجة أنّ الفصل بين مكوّناتها ليس مهمًّا على الإطلاق.

يصبح إدخال أنماط التّصميم في تطبيقاتنا أسهل بكثير بعد أنّ نُحسن فهمها وفهم المُشكلات الّتي تحلّها.

**سنستعرض في هذا القسم الأنماط التّالية:**
* نمط مُشيّد الكائنات (Constructor Pattern)
* نمط الوحدة (Module Pattern)
* نمط الوحدة الكاشفة (Revealing Module Pattern)
* نمط الكائن المُتفرّد (Singelton Pattern)
* نمط المُراقِب (Observer Pattern)

نمط مُشيّد الكائنات (Constructor)
----------------------------
المُشيّد (constructor) في لغات البرمجة كائنيّة التّوجّه التّقليديّة هو وظيفة (method) خاصّة تُستخدم لتهيئة كائن جديد بعد أنّ خُصّصت له مساحة من الذّاكرة. في JavaScript يكاد يكون كلّ شيءٍ كائنًا، لذا فإنّ اهتمامنا سينصبّ على مُنشئات الكائنات.

تُستخدم المُشيّدات لإنشاء أنواع مُعيّنة من الكائنات، وهذا يتضمّن تهيئة الكائن للاستخدام وقبول المُعاملات الّتي سيتسخدمها هذا المُشيّد لتعيين قيمة الخواصّ والوظائف عندما يُنشأ هذا الكائن.

###إنشاء الكائنات
فيما يلي الطّرق الثلاث الأكثر شيوعًا في JavaScript لإنشاء كائنات جديدة:

```javascript
// كلّ ممّا يلي يُنشئ كائنًا جديدًا فارغًا:
 
var newObject = {};
 
// أو
var newObject = Object.create( Object.prototype );
 
// أو
var newObject = new Object();
```

في هذه الحالة، فإنّ مُنشئ الكائن (`Object`) يُنشئ كائنًا يُغلّف القيمة الُتي تلقّاها، أو يُنشئ كائنًا فارغًا ويُعيده ليُحفظ في المُتغيّر `newObject` إنّ لم تُمرّر له أيّة قيمة.

وأمّا إسناد الخصائص وقيمها إلى الكائن، فإنّه يُنفّذ بإحدى أربع طرق:

```javascript
// طرق إسناد متوافقة مع الإصدار 3 من ECMAScript:

// 1. بصياغة النّقطة (Dot syntax)

// تعيين الخصائص:
newObject.someKey = "Hello World";

// قراءة الخصائص:
var value = new Object.someKey;

// 2. بصياغة الأقواس المُربّعة (Square brackets synatx)

// تعيين الخصائص:

newObject["someKey"] = "Hello World";

// قراءة الخصائص:
var value = newObject["someKey"];

// طرق إسناد متوافقة فقط مع الإصدار 5 من ECMAScript وما يليه:
// اطّلع على http://kangax.github.com/es5-compat-table لمعلومات أكثر

// 3. باستخدام Object.defineProperty

// تعيين الخصائص:

Object.defineProperty( newObject, "someKey", {
    value: "for more control of the property's behavior",
    writable: true,
    enumerable: true,
    configurable: true
});


// يمكن كتابة دالّة تختصر ما سبق لسهولة القراءة:

var defineProp = function ( obj, key, value ){
  var config = {
    value: value,
    writable: true,
    enumerable: true,
    configurable: true
  };
  Object.defineProperty( obj, key, config );
};

// حيث نستخدمها بعد إنشاء كائن فارغ مثل person:
var person = Object.create( Object.prototype );

// نملؤه بالخصائص مستخدمين الدّالة الّتي كتبناها:
defineProp( person, "car",  "Delorean" );
defineProp( person, "dateOfBirth", "1981" );
defineProp( person, "hasBeard", false );

console.log(person);
// النّاتج: {car: "Delorean", dateOfBirth: "1981", hasBeard: false}


// 4. باستخدام Object.defineProperties

// تعيين الخصائص
Object.defineProperties( newObject, {
 
  "someKey": {
    value: "Hello World",
    writable: true
  },
 
  "anotherKey": {
    value: "Foo bar",
    writable: false
  }
 
});

// يمكن قراءة خصائص 3 و4 بأيّ من الوسيلتين في 1 و2
```

يمكن استخدام هذه الوسائل في الوراثة كما سيتبيّن لنا فيما بعد:
```javascript
// الاستخدام:
 
// أنشئ سائق سيّارات يرث الكائن "person":
var driver = Object.create( person );
 
// أسند بعض الخصائص للسائق:
defineProp(driver, "topSpeed", "100mph");
 
// اقرأ خاصّة موروثة مثل تاريخ الولادة
console.log( driver.dateOfBirth );
 
// اقرأ خاصّة أسندناها مباشرة على السّائق مثل السّرعة القُصوى:
console.log( driver.topSpeed );
```

###المُشيّدات البسيطة
كما تعلّمنا من قبل فإنّه لا وجود لمفهوم الأصناف (classes) التّقليديّ في JavaScript*، ولكنّها تسمح بإنشاء دوال مُشيّدة تعمل مع الكائنات، وذلك بأن نسبق استدعاء هذه الدّوال بالكلمة المفتاحية `new` الّتي تُخبر JavaScript بأنّنا نريد للدّالة المُستدعاة أن تتصرّف كمُشيّد يولّد كائنًا جديدًا عناصره هي العناصر الّتي عُرِّفت في تلك الدّالة.

تُشير الكلمة المفتاحيّة `this` ضمن دالّة التّشييد إلى العنصر الجديد الّذي سيتمّ إنشاؤه. بالعودة إلى موضوع إنشاء الكائنات، فإنّه يمكن كتابة مُشيّد بسيط كما يلي:

```javascript
function Car( model, year, miles ) {
 
  this.model = model;
  this.year = year;
  this.miles = miles;
 
  this.toString = function () {
    return this.model + " has done " + this.miles + " miles";
  };
}
 
// الاستخدام:
 
// نُنشئ نُسخًا جديدة من السّيّارة:
var civic = new Car( "Honda Civic", 2009, 20000 );
var mondeo = new Car( "Ford Mondeo", 2010, 5000 );
 
// ثمّ نفتح لوحة المراقبة في المتصفّح لنشاهد ناتج استدعاء
// الوظيفة toString على هذه الكائنات
// these objects
console.log( civic.toString() );
console.log( mondeo.toString() );
```

المثال السّابق نُسخة مُبسّطة من نمط المُشيّد تعاني من بعض المُشكلات؛ فهي أوّلًا تجعل الوراثة عمليّة صعبة، وثانيًا تُعيد تعريف الوظيفة `toString()` لكلّ عنصر يُنشئ باستخدام المُشيّد `Car`، وهذا ليس أفضل خيار، فالأفضل أن تكون هذه الدّالة مُشتركة بين كلّ نُسخ النّوع `Car`**.

لحسن الحظّ، تتوفّر بدائل لتشييد الكائنات في كلّ من ES3 وES5 تجعل تجاوز هاتين المُشكلتين أمرًا سهلًا للغاية.

‏<sub>* هذا الكتاب صدر قبل اعتماد الأصناف في JavaScript. (المترجم)</sub>

‏<sub>** السّبب في تفضيل إلحاق الوظيفة بنموذج الكائن بدل تعيينها ضمن مُشيّد الكائنات هو توفير استخدام الذّاكرة، لأنّه ما من سبب يجعلنا نستهلك الذّاكرة بإعادة تعيين الوظيفة لكلّ نسخة من الكائن طالما أنّها جميعًا تشترك بنفس القيمة وتتصرّف بالطّريقة ذاتها، على عكس أنواع الخواصّ الأخرى الّتي تكون مختلفة من نسخة لأخرى (مثلاً: السّرعة القصوى للسّيّارة، أو لونها). (المترجم)</sub>

###المُشيّدات مع النّماذج البدئيّة
الدّوال في JavaScript هي الأخرى كائنات، ولكلّ الكائنات نموذج بِدئيّ (protoype). عندما نستدعي مُشيّدًا لكائن فإنّ كلّ خصائص نموذج هذا المُشيّد ستكون متاحة للكائن الجديد. يمكننا بهذا الأسلوب إنشاء نسخ سيّارات يمكنها الوصول إلى النّموذج ذاته. سنقوم بتعديل المثال الأصلي كما يلي:

```javascript
function Car( model, year, miles ) {
 
  this.model = model;
  this.year = year;
  this.miles = miles;
 
}
 
 
// لاحظ أنّنا نستخدم Object.prototype.newMethod وليس Object.prototype
//  مباشرةً، وذلك لنتجنّب إعادة تعريف نموذج الكائن
Car.prototype.toString = function () {
  return this.model + " has done " + this.miles + " miles";
};
 
// الاستخدام:
 
var civic = new Car( "Honda Civic", 2009, 20000 );
var mondeo = new Car( "Ford Mondeo", 2010, 5000 );
 
console.log( civic.toString() );
console.log( mondeo.toString() );
```

في هذه الحالة، فإنّ كلّ السّيّارات ستتشارك نسخة واحدة من الدّالة `toString()‎`.


##نمط الوحدة (Module)
###الوحدات
الوحدات جزء لا يُستغنى عنه إذا أردنا بناء تطبيق على أُسِس متينة، لأنّها تُسهم في جعل مكوّنات المشروع منفصلة ومُرتبّة في وقت واحد.

تتوفّر في JavaScript عدّة خيارات لتصميم الوحدات، منها:

* نمط الوحدة
* نمط الكائنات الحرفيّة
* وحدات AMD
* وحدات CommonJS
* وحدات ECMAScript Harmony

لا بأس في أنّ نراجع مجدّدًا ما تعنيه الكائنات الحرفيّة، لأنّ نمط الوحدات يقوم في بعض أجزائه على هذا الأسلوب.

###الكائنات الحرفيّة
الكائنات الحرفيّة هي أسلوب للتّصريح عن الكائنات بكتابة مجموعة من الأزواج المؤلّفة من أسماء الخصائص وقيمها مفصولة بفاصلة (`,`) ومحاطةً جميعها بأقواس معكوفة (`{}`). يمكن كتابة أسماء الخصائص ضمن الكائن إمّا كسلسة نصّيّة (string) أو كمُعرّف. ينبغي ألّا يُلحق الزّوج الأخير من الخصائص بفاصلة لأنّ هذا قد يؤدّي إلى أخطاء.

```javascript
var myObjectLiteral = {
 
    variableKey: variableValue,
 
    functionKey: function () {
      // ...
    }
};
```

لا تتطلّب الكائنات المُنشأة بهذا الأسلوب استخدام الكلمة `new` عند إنشائها، ولكن يجب أّلا تُستخدم في بداية الجملة لأنّ القوس المعكوف `{` قد يُفسّر على أنّه بداية لقطعة برمجيّة (block). يمكن إضافة المزيد من الخصائص على هذا الكائن من خارجه وذلك باستخدام الإسناد كما يلي: ‎`myModule.property = "someValue";`‎

فيما يلي مثال أكثر تفصيلًا عن وحدةٍ تُعرَّف باستخدام الكائنات الحرفيّة:

```javascript
var myModule = {
 
  myProperty: "someValue",
 
  // يمكن للكائنات الحرفيّة أن تحوي خصائص ووظائف
  // مثلاً يمكننا تعريف كائن آخر ضمنها لضبط إعدادات الوحدة:
  myConfig: {
    useCaching: true,
    language: "en"
  },
 
  // وظيفة بسيطة جدًّأ:
  saySomething: function () {
    console.log( "Where in the world is Paul Irish today?" );
  },
 
  // إخراج قيمة بحسب الضبط الحاليّ:
  reportMyConfig: function () {
    console.log( "Caching is: " + ( this.myConfig.useCaching  ? "enabled" : "disabled") );
  },
 
  // تغيير الضّبط الحالي:
  updateMyConfig: function( newConfig ) {
 
    if ( typeof newConfig === "object" ) {
      this.myConfig = newConfig;
      console.log( this.myConfig.language );
    }
  }
};
 
// النتيجة: Where in the world is Paul Irish today?
myModule.saySomething();
 
// النتيجة: Caching is: enabled
myModule.reportMyConfig();
 
// النتيجة: fr
myModule.updateMyConfig({
  language: "fr",
  useCaching: false
});
 
// النتيجة: Caching is: disabled
myModule.reportMyConfig();
```

يساعد استخدام الكائنات الحرفية في عزل الشيفرة وتنظيمها، وقد كتبت Rebecca Murphey من قبل عن هذا الموضوع [بالتّفصيل](http://rmurphey.com/blog/2009/10/15/using-objects-to-organize-your-code/) إن كنت ترغب في التّعمّق في موضوع الكائنات الحرفيّة.

قد نرغب مع اختيارنا لهذه التّقنيّة بالانتقال إلى نمط الوحدة، الذي يستخدم الكائنات الحرفيّة ولكن فقط كقيمة تُعيدها دالّة تُحيط بها.

###نمط الوحدة (Module)
صُمّمت الوحدات بالأصل لتقدّم للمطوّرين طريقة لعزل المكوّنات العلنيّة (public) والسّرِّيَّة (private) للأصناف (classes) في هندسة البرمجيّات التّقليديّة.

أمّا في JavaScript، فإنّ نمط الوحدة يُستخدم ليُحاكيَ مفهوم الأصناف بطريقة تسمح لنا بتضمين مُتغيّرات ووظائف علنيّة وسرّيَّة على حدّ سواء في كائن واحد، مانعين بذلك الوصول إليها من النّطاق العامّ (global scope). يقلّل هذا التّصميم بالنّتيجة من احتمال أن تتعارض أسماء الدّوال في هذا الكائن مع أسماء دوالّ أخرى عُرّفت في نصوص برمجيّة (scripts) أخرى في الصّفحة.

####الخصوصيّة
يستخدم نمط الوحدات الدّوال المُغلقة (closures)* لعزل المكوّنات الدّاخليّة وإعطائها الخصوصيّة المطلوبة، وذلك بإحاطة خليط من المُتغيّرات والوظائف السّريّة والعلنيّة وحماية أجزاء من الكائن من أن "تتسرّب" إلى النّطاق العامّ وتتعارض مع واجهة لمطوّر آخر من غير قصد. لا نُعيد في هذا النّمط إلا واجهة برمجيّة علنيّة، مُبقين على كلّ شيء آخر سرِّيًّا ضمن الدّالّة.

يعطينا هذا وسيلةً لإخفاء تفاصيل عمل الشّيفرة في حين يُبقي على واجهة يُسمح للأجزاء الأخرى من التّطبيق استخدامها.

يجب ألّا ننسى أنّه ما من مفهوم حقيقيّ لخصوصيّة المُتغيّرات والكائنات في JavaScript لأنّها —بعكس بعض اللّغات التّقليديّة— لا تتضمّن إمكانيّة تحديد مستويات للوصول إلى المُتغيّرات (access modifiers)؛ بمعنى أنّه لا يمكن التّصريح عن مُتغيّرات على أنّها سرّيّة أو علنيّة، وهذا ما يجعلنا نلجأ إلى نطاق الدّالة الفرعيّ لمحاكاة هذا المفهوم، إذ تكون المُتغيّرات والوظائف المُعرّفة في نمط الوحدة متاحةً ضمن الوحدة ذاتها فقط نظرًا لأنّ الوحدة هي دالّة مُغلقة (closure). وأمّا المُتغيّرات والوظائف العلنيّة فهي متاحة للعموم لأنّها ضمن الكائن الذي تُعيده الدّالّة.

‏<sub>* الدّوال المغلقة (closures): إذا كانت لدينا دالّة تُعيد عند استدعائها دالّة أخرى، فإنّنا ندعو الدّالة الأخيرة دالّة مُغلقة، ويتاح لهذه الدّالة الوصول إلى المتّغيّرات الّتي كانت مفروضة في الدّالة الأولى حتّى عندما تُستدعى من خارجها. (المترجم)</sub>

####نظرة تاريخيّة
لنلقِ نظرة على تاريخ هذا النّمط؛ إذ طوّره في عام 2003 عدّة أشخاص من بينهم [Richard Cornford‏](http://groups.google.com/group/comp.lang.javascript/msg/9f58bd11bd67d937) ثمّ أشاع استخدامه Douglas Crockford في محاضراته. إذا اطّلعت على النّص البرمجيّ لمكتبة YUI من Yahoo!‎ فستجد أنّ بعض ميّزاتها مألوفة، وسبب ذلك أنّه كان لهذا النّمط تأثير كبير عندما بدأ إنشاء مكوّنات هذه المكتبة.

####أمثلة
سنبدأ بتطبيق مثال لنمط الوحدة نُنشئ فيه وحدة محتواة بذاتها.

```javascript
var testModule = (function () {
 
  var counter = 0;
 
  return {
 
    incrementCounter: function () {
      return counter++;
    },
 
    resetCounter: function () {
      console.log( "counter value prior to reset: " + counter );
      counter = 0;
    }
  };
 
})();
 
// الاستخدام:
 
// زِد قيمة العدّاد
testModule.incrementCounter();
 
// افحص قيمة العدّاد ثمّ صفّره
// النّاتج: counter value prior to reset: 1
testModule.resetCounter();
```
لا تستطيع أجزاء البرنامج الأخرى الوصول مباشرةً إلى قيمة `incrementCounter()‎` أو `resetCounter()‎` في المثال السّابق، فقيمة العدّاد محميّة تمامًا من أن تصل إليها كائنات النّطاق العام، أي أنّها تتصرّف وكأنّها متغيّر سرِّيّ (private) وجوده محدود ضمن الدّالة المُغلقة (التي هي الوحدة) بحيث أنّه لا يمكن الوصول إلى نطاقها إلّا من الدّالتين السّابقتين. لاحظ كذلك أنّ هاتين الدّالتين معرّفتان ضمن فضاء أسماء (namespace) عمليًّا، ممّا يعني أنّه يجب إسباقهما باسم الوحدة (`testModule` مثلاً) قبل استدعائهما.

من المفيد استخدام قالب بسيط نبدأ به دومًا عندما نختار نمط الوحدة. في ما يلي قالب يشمل فضاء الأسماء والمُتغيّرات العامّة والخاصّة:

```javascript
var myNamespace = (function () {
 
  var myPrivateVar, myPrivateMethod;
 
  // متغيّر خاص لعدّاد
  myPrivateVar = 0;
 
  // دالّة خاصّة تُسجّل كلّ المُعامِلات
  myPrivateMethod = function( foo ) {
      console.log( foo );
  };
 
  return {
 
    // متغيّر عامّ
    myPublicVar: "foo",
 
    // دالّة عامّة تستفيد من كائنات خاصّة
    myPublicFunction: function( bar ) {
 
      // زِد قيمة متغيّر العدّاد الخاصّ
      myPrivateVar++;
 
      // استدعِ الدّالة الخاصّة بمعامل bar
      myPrivateMethod( bar );
 
    }
  };
 
})();
```

لنطّلع على مثال آخر كذلك: سلّة مشتريات تعتمد هذا النّمط. الوحدة محتواة ذاتيًّا ضمن متغيّر يتبع النّطاق العام ندعوه `basketModule`. نُبقي مصفوفة `basket` سرّيَّة بحيث لا يمكن لأجزاء أخرى من البرنامج قراءتها مباشرة، فلا وجود لها إلا ضمن الوحدة ولا يستطيع قرائتها إلّا ما هو ضمن نطاق داّلة الوحدة (أجزاء مثل `addItem()‎` و`getItemCount()‎`... إلخ.).

```javascript
var basketModule = (function () {
 
  // المتغيّرات السّرِّيّة
 
  var basket = [];
 
  function doSomethingPrivate() {
    //...
  }
 
  function doSomethingElsePrivate() {
    //...
  }
 
  // أرجع كائنًا مكشوفًا للعموم
  return {
 
    // أضف عناصر إلى السّلّة
    addItem: function( values ) {
      basket.push(values);
    },
 
    // أحصِ عناصر السّلّة
    getItemCount: function () {
      return basket.length;
    },
 
    // اسم بديل متاح للعموم للدّالة السّرّيّة
    doSomething: doSomethingPrivate,
 
    // أحصِ قيمة المشتريات في السّلّة
    getTotal: function () {
 
      var q = this.getItemCount(),
          p = 0;
 
      while (q--) {
        p += basket[q].price;
      }
 
      return p;
    }
  };
})();
```

لاحظ أنّنا أرجعنا كائنًا (`object`) ضمن الوحدة، وهذا الكائن سُيسنَد إلى المُتغّيّر الذي فرضناه `basketModule` لنستطيع استعماله بعد ذلك:

```javascript
// تُرجِع basketModule كائنًا بواجهة مُتاحة للعموم
 
basketModule.addItem({
  item: "bread",
  price: 0.5
});
 
basketModule.addItem({
  item: "butter",
  price: 0.3
});
 
// الناتج: 2
console.log( basketModule.getItemCount() );
 
// الناتج: 0.8
console.log( basketModule.getTotal() );
 
// لن تعمل الشّيفرة التّالية:
 
// النّاتج: undefined
// وسبب ذلك أنّ السّلة ذاتها (المتغيّر basket) ليس مكشوفًا ضمن الواجهة العّامّة
console.log( basketModule.basket );
 
// وهذا أيضًا لن يعمل، لأنّه لا وجود للسّلّة خارج نطاق الوحدة:
console.log( basket );
```

الوظائف أعلاه محتواة عمليًّا ضمن فضاء الأسماء `basketModule`.

لاحظ كيف تحيط الدّالّة بالوحدة ثمَّ تُستدعى مباشرةً لتُحفظ القيمة ضمن المُتغيّر. لهذا الأسلوب عدّة فوائد، منها:

* حرّيّة إنشاء دوالّ سرِّيَّة لا يمكن استخدامها إلّا ضمن الوحدة لأنّها لا تُكشف لبقيّة الصّفحة (لا تُكشف إلّا الواجهة الّتي قرّرنا إتاحتها للعموم).
* باعتبار أنّ هذه الدّوالّ معرّفة بشكل اعتيادي ومُسمَّاة، سيكون من السّهل تتبّع مسار الاستدعاء ضمن المُنقِّح (debugger) عندما نحاول معرفة الدّالّة أو الدّوال الّتي ألقت باسثتناء (exception).
* كما وضّح T.J Crowder من قبل، فهذا الأسلوب يتيح لنا إعادة دوالّ مختلفة بحسب البيئة. رأيت بعض المطوّرين في الماضي يستخدمونها لإجراء اختبار على عميل المستخدم (User Agent) في المتصفّح من أجل تقديم شفرة في الدّالّة خاصّة بـInternet Explorer، أمّا اليوم فيمكننا اعتماد أسلوب "اكتشاف المزايا" للوصول إلى نفس الهدف.
    
####التّعديلات على نمط الوحدة

#####الاستيراد
هذا التّعديل يوضّح كيف يمكن إمرار كائنات مفروضة في النّطاق العام (مثل jQuery وUnderscore) إلى الدّالّة المجهولة لوحدتنا. هذا يسمح لنا عمليًّا _باستيراد_ هذه الكائنات وتسميتها بأسماء بديلة محليًّا ضمن الوحدة نختارها كما نشاء.

```javascript
// وحدة متاحة في النّطاق العامّ
var myModule = (function ( jQ, _ ) {
 
    function privateMethod1(){
        jQ(".container").html("test");
    }
 
    function privateMethod2(){
      console.log( _.min([10, 5, 100, 2, 1000]) );
    }
 
    return{
        publicMethod: function(){
            privateMethod1();
        }
    };
 
// استورد jQuery وUnderscore
})( jQuery, _ );
 
myModule.publicMethod();
```

#####التّصدير
هذا التّعديل يُتيح التّصريح عن كائنات في النّطاق العامّ دون استخدامها، ويمكن أن يترافق مفهوم الاستيراد من النّطاق العامّ الّذي رأيناه في المثال السّابق.

```javascript
// وحدة متاحة في النّطاق العامّ
var myModule = (function () {
 
  // Module object
  var module = {},
    privateVariable = "Hello World";
 
  function privateMethod() {
    // ...
  }
 
  module.publicProperty = "Foobar";
  module.publicMethod = function () {
    console.log( privateVariable );
  };
 
  return module;
 
})();
```
####تطبيقات خاصّة لنمط الوحدة ضمن أُطر العمل ومكتبات الأدوات
#####Dojo
تُتيح مكتبة Dojo وظيفة للتّعامل مع الكائنات تُدعى `dojo.setObject()‎` تستقبل سلسلة نصّيّة مفصولة بنقاط مثل `"myObj.parent.child"`  الّتي تشير إلى خاصّة اسمها `child` ضمن كائن `parent` مُعرّف ضمن `myObj`. عند استخدام `setObject()‎` يمكن أن نُعيّن قيمة `child` وستقوم المكتبة بإنشاء كلّ الكائنات التي تعلوها في السّلسلة الّتي مرّرناها إن لم تكن موجودة من قبل.

إن أردنا مثلاً تعريف `basket.core` ككائن ضمن فضاء `store`، فيمكن تنفيذ ذلك بالطّريقة التّقليديّة:

```javascript
var store = window.store || {};
 
if ( !store["basket"] ) {
  store.basket = {};
}
 
if ( !store.basket["core"] ) {
  store.basket.core = {};
}
 
store.basket.core = {
  // ... بقية الشّيفرة
};
```
أو كما يلي مستخدمين Dojo 1.7 (الإصدارة المتوافقة مع نظام وحدات AMD):

```javascript
require(["dojo/_base/customStore"], function( store ){
 
  // باستخدام dojo.setObject()‎
  store.setObject( "basket.core", (function() {
 
      var basket = [];
 
      function privateMethod() {
          console.log(basket);
      }
 
      return {
          publicMethod: function(){
                  privateMethod();
          }
      };
 
  })());
 
});
```

اطّلع على [الوثائق الرّسميّة](http://dojotoolkit.org/reference-guide/1.7/dojo/setObject.html) لمعلومات أكثر عن `dojo.setObject()‎`.

#####ExtJS
لمن يستخدم ExtJS من Sencha، سنستعرض مثالًا عن استخدام نمط الوحدة مع إطار العمل هذا.

في هذا المثال نرى كيف نُعرِّف فضاء أسماء يمكن تعبئته بوحدة تحوي واجهتين علنيّة وسرِّيَّة. الأسلوب مشابه كثيرًا لـJavaScript الخام مع بعض الاختلافات البسيطة في التِّسميات:

```javascript
// أنشئ فضاء الأسماء
Ext.namespace("myNameSpace");
 
// أنشئ تطبيقًا
myNameSpace.app = function () {
 
  // لا تتعامل مع DOM هنا لأنّ محتوياته لمَّا تُوجد
  // متغيّرات سرِّيَّة
 
  var btn1,
      privVar1 = 11;
 
  // private functions
  var btn1Handler = function ( button, event ) {
      console.log( "privVar1=" + privVar1 );
      console.log( "this.btn1Text=" + this.btn1Text );
    };
 
  // واجهة عامّة
  return {
    // خواصّ عامّة، مثل جمل متاحة للترجمة
    btn1Text: "Button 1",
 
    // وظائف عامّة
    init: function () {
 
      if ( Ext.Ext2 ) {
 
        btn1 = new Ext.Button({
          renderTo: "btn1-ct",
          text: this.btn1Text,
          handler: btn1Handler
        });
 
      } else {
 
        btn1 = new Ext.Button( "btn1-ct", {
          text: this.btn1Text,
          handler: btn1Handler
        });
 
      }
    }
  };
}();
```

#####YUI
يمكننا بأسلوب مشابه تطبيق نمط الوحدة عندما نبني تطبيقات باستخدام YUI3. المثال التّالي مُستوحى من نمط الوحدة الّذي اعتمده Eric Miraglia في YUI، ولكنّه لا يختلف عن نسخة JavaScript الخام كثيرًا:

```javascript
Y.namespace( "store.basket" ) ;
Y.store.basket = (function () {
 
    var myPrivateVar, myPrivateMethod;
 
    // مُتغيّرات سرِّيَّة:
    myPrivateVar = "I can be accessed only within Y.store.basket.";
 
    // وظيفة سرِّيَّة:
    myPrivateMethod = function () {
        Y.log( "I can be accessed only from within YAHOO.store.basket" );
    }
 
    return {
        myPublicProperty: "I'm a public property.",
 
        myPublicMethod: function () {
            Y.log( "I'm a public method." );
 
            // يمكنني أن أصل إلى المُتغيّرات والوظائف "السّرِّيّة ضمن السّلة:
            Y.log( myPrivateVar );
            Y.log( myPrivateMethod() );
 
            // النّطاق الطّبيعيّ لـmyPublicMethod هو store لذا يمكننا
            // الوصول إلى الخواصّ العامّة باستخدام "this":
            Y.log( this.myPublicProperty );
        }
    };
 
})();
```

#####jQuery
تتوفّر عدّة طرق في jQuery لاستخدام نمط الوحدة في كتابة الشّيفرة خارج إضافات jQuery، اقترح Ben Cherry سابقًا طريقة لتطبيق هذا النمط باستخدام دالّة تختصر تعريف الوحدات في حال كانت هذه الوحدات متشابهة فيما بينها.

في المثال التّالي، لدينا دالّة `library` الّتي تُصرّح عن مكتبة جديدة وتربط الدّالّة `init` ضمن المكتبة بالحدث `document.ready` عندما تُنشأ المكتبة (أو الوحدة) الجديدة.

```javascript
function library( module ) {
 
  $( function() {
    if ( module.init ) {
      module.init();
    }
  });
 
  return module;
}
 
var myLibrary = library(function () {
 
  return {
    init: function () {
      // محتويات الوحدة
    }
  };
}());
```

####المحاسن
نعرف الآن فائدة نمط المُشيِّد، ولكن متى يكون اختيار نمط الوحدة مناسبًا؟ بالنّسبة للمبتدئين القادمين من لغة برمجة كائنيّة التّوجّه (object-oriented)، فإنّ نمط الوحدة يبدو أكثر نظافةً من فكرة العزل الكامل للشّيفرة، على الأقل من وجهة نظر JavaScript.

وثانيًا، فإنّها تتيح سرِّيَّة البيانات - وهكذا فإنّه يمكن للأجزاء العلنيّة من الوحدة أن تتواصل مع الأجزاء السّرِّيَّة فيها، ولكن لا يمكن لما خارج الوحدة أن يتواصل مع تلك الأجزاء.

####المُساوئ
من مساوئ هذا النّمط أنّه عندما نستخدم محتويات الوحدة السرِّيَّة والعلنيّة كليهما، فإنّنا نصل إلى كلّ منهما بطريقة مختلفة، وعندما نرغب بتغيير مستوى ظهور إحدى الخواصّ، فإنّ علينا تعديل الشّيفرة في كلّ موضع اُستخدمَت فيه هذه الخاصّة.

وكذلك فإنّه لا يمكننا الوصول إلى الخواصّ السّريَّة في الوحدة من وظائف أُضيفت إليها بعد إنشاءها. ومع ذلك، يبقى نمط الوحدة مُفيدًا عندما يُستخدم بشكل صحيح ويمكن أن يُحسَّن من هيكل تطبيقنا.

من المساوئ الأخرى: عدم القدرة على إنشاء اختبارات للوحدات (unit tests) للخواصّ السّرِّيّة وزيادة في تعقيد الشّيفرة عندما نحتاج لإصلاح العلل بسرعة؛ إذ لا يمكن ببساطة إصلاح الأجزاء السرِّيَّة، بل علينا تبديل كلّ الوظائف العلنيّة الّتي تقرأ أو تتعامل مع الخواصّ السّريَّة الّتي تسبّب المشكلة. وكذلك لا يمكن للمطوّرين توسيع الخواصّ السّرِّيَّة. إذن علينا أن نتنبَّه إلى أنّ الخواصّ السّريّة ليست بالمرونة الّتي تبدو بها للوهلة الأولى.

للتوسّع في نمط الوحدة، أنصح بقراءة [مقالة Ben Cherry‏](http://www.adequatelygood.com/2010/3/JavaScript-Module-Pattern-In-Depth) الممتازة.

نمط الوحدة الكاشفة (Revealing Module)
-----------------------------------
بعد أن ألِفنا نمط الوحدة قليلًا، لم لا نطّلع على نسخة مُحسّنة منها؟ إنّه نمط الوحدة الكاشفة الّذي ابتكره Christian Heilmann.

أتى Heilmann بهذا النّمط بعد أن ساءه اضطراره إلى تكرار اسم الكائن الرئيسيّ عند الحاجة لاستدعاء وظيفة علنيّة من وظيفة أخرى أو قراءة خاصّة علنيّة ضمن الوحدة، وساءه كذلك ما يشترطه نمط الوحدة من كتابة الخواصّ العلنيّة بصيغة كائن حرفيّ.

كانت نتيجةُ جهوده نمطًا مُحسَّنًا يستطيع به تعريف كلّ الدّوال والمتغيّرات ضمن نطاق الوحدة الخاصّ ثمّ إرجاع كائن جديد يحوي خواصّ تُشير إلى بعض المكوّنات السّرِّيّة الّتي يُراد إتاحتها بصورة علنيّة.

فيما يلي مثال عن استخدام هذا النّمط:

```javascript
var myRevealingModule = (function () {
 
        var privateVar = "Ben Cherry",
            publicVar  = "Hey there!";
 
        function privateFunction() {
            console.log( "Name:" + privateVar );
        }
 
        function publicSetName( strName ) {
            privateVar = strName;
        }
 
        function publicGetName() {
            privateFunction();
        }
 
 
        // اكشف كائنًا علنيًّا بخواصّ تُشير إلى
        // بعض الدّوال والمُتغيّرات السِّرِّيَّة
 
        return {
            setName: publicSetName,
            greeting: publicVar,
            getName: publicGetName
        };
 
    })();
 
myRevealingModule.setName( "Paul Kinlan" );
```

يمكن استخدام هذا النّمط لكشف دوالّ ومتغيّرات سريّة بأسلوب تسميّة مُختلف إن شئنا:

```javascript
var myRevealingModule = (function () {
 
        var privateCounter = 0;
 
        function privateFunction() {
            privateCounter++;
        }
 
        function publicFunction() {
            publicIncrement();
        }
 
        function publicIncrement() {
            privateFunction();
        }
 
        function publicGetCount(){
          return privateCounter;
        }
 
         // اكشف كائنًا عامًّا بخواصّ تُشير إلى
         // بعض الدّوال والمُتغيّرات السِّرِّيَّة
 
       return {
            start: publicFunction,
            increment: publicIncrement,
            count: publicGetCount
        };
 
    })();
 
myRevealingModule.start();
```

###المحاسن
يسمح هذا النّمط بتوحيد صياغة الشّيفرة بين الأجزاء العلنيّة والسّريّة. كما أنّه يجعل من السهل معرفة ما يُكشف من الوحدة للعموم في نهاية الشِّيفرة، مما يُسهِّل قراءتها.

###المساوئ
إحدى مساوئ هذا النّمط أنّه عندما تتعامل دالّة سرِّيَّة مع دالّة علنيّة فإنّه لا يمكن تبديل تلك الدّالّة العلنيّة عندما نحتاج لإصلاح مشكلة ما في الوحدة، وذلك لأنّ كلّ ما نستطيع فعله هو تبديل ما تُشير إليه الخاصّة العلنيّة وستبقى الدّالّة السّريَّة تتعامل مع الدّالّة الأصليّة ذاتها. كما أنّ هذا النّمط لا يمكن تطبيقه مع المتغيّرات* العلنيّة، بل مع الدّوالّ فقط.

تعاني الكائنات العلنيّة الّتي تُشير إلى مُتغيّرات خاصّة من نفس العيب السّابق.

كنتيجة لهذه العيوب، فإنّ الوحدات المُنشأة بهذا النّمط ستكون أكثر هشاشة من الوحدات المُنشأ بالنّمط التّقليديّ، فيجب الانتباه لهذا أثناء استخدامها.

‏<sub> * السّبب هو أنّ المُتغيّرات الّتي تُشير إلى قيم مثل الأرقام والنصوص تُمرَّر في JavaScript بقيمتها (by value) وليس بموضع الذّاكرة الّذي تُشير إليه (by reference). (المترجم)</sub>

_**ملاحظة**: هذا جزء مُترجم —بشيء من التصرف— عن كتاب [Learning JavaScript Design Patterns‏](http://addyosmani.com/resources/essentialjsdesignpatterns/book) لمؤلّفه Addy Osmani._
