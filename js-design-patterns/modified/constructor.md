أنماط التصميم في JavaScript: نمط المُشيِّد (Constructor)
=================================================
_في سلسلة من عدة أجزاء سنناقش موضوعًا نظريًّا يُعتبر من أساسيّات هندسة البرامج، وهو أنماط التصميم (Design Patterns)، وسنعتمد لغة JavaScript في نقاشنا لتصاعد شعبيّتها ومرونتها التي تسمح لنا ببناء مشاريعنا وفق أنماط متنوّعة مما سيُسهّل علينا شرح موضوع السّلسلة_

نمط المُشيِّد (Constructor)
-----------------------
يشيع استخدام المُشيّدات في اللغات الكائنيّة التّوجّه، حيث تُستخدم لإنشاء نُسخ (instances) من الأصناف (classes)، ومع أنّ JavaScript ليست لغةً كائنيّة التّوجّه بالمعنى التّقليديّ، إلّا أنّها تسمح بإنشاء نُسخ عن كائنات باستخدام بالمُشيّدات، ويمكن لأيّ دالّة أن تُستخدم كمُشيّد، وذلك بأن نُسبقها بالكلمة `new`، ولتوضيح هذا النّمط سنقوم بإنشاء مُنبّه (كالّذي تضبطه للاستيقاظ في هاتفك) يمكن ضبطه إلى تاريخ ووقت معيّنين ثمّ تفعيله أو تعطيله حسب الرّغبة:

```javascript
function Alarm(when) {
    this.setAt = when;
    this.enable = function() {
        var startAfter = new Date(this.setAt) - new Date;
        console.log("Alarm will wake you up after " + startAfter/1000 + " seconds");
        this.timeout = setTimeout(function() {
            console.log("Wake up!");
        }, startAfter);
    }
    this.disable = function() {
        if (this.timeout) {
            clearTimeout(this.timeout);
            delete this.timeout;
            console.log("Alarm diabled");
        }
    }
}

var a = new Alarm("2015-03-19 5:58 PM");
a.enable()
// Alarm will wake you up after 8.982 seconds

// After a few seconds:
// Wake up!
```

في المثال السّابق نُسمّي الدّالة `Alarm()‎` مُشيّدًا (constructor)، والكائن `a` نُسخة (instance).

لاحظ أنّ `Alarm` في المثال السّابق ليست سوى دالّة (function)، فهي ليست صنفًا كما في لغات أخرى مثل Java وC++، إذ تُعتبر الدّوال في JavaScript مكوّنًا من الدّرجة الأولى وتُعامل كما يُعامل أيّ كائن، وهكذا يمكن استخدامها كمشيّد لكائن آخر ممّا يسمح بمحاكاة مفهوم الأصناف الّذي لم يُضَف إلّا مؤخّرًا في JavaScript. من عيوب المِثال السّابق إسناد الدّوال الّتي ستعمل عمل الوظائف (methods) إلى النُسخة ذاتها عند إنشائها، وهذا يعني تكرار محتوى الدّوال في الذّاكرة مع كلّ نسخة جديدة من الكائن `Alarm`، بينما يمكننا توفير هذا الاستهلاك غير المُبرّر للذّاكرة بإسناد الدّوال إلى النّموذج البدئيّ للكائن (أي إلى `Alarm.prototype`) مما يسمح بمشاركتها بين كل نسخ الكائن، لتقوم الآلة الافتراضيّة بتنفيذ النّصّ البرمجيّ للدّالّة ذاتها بسياق النّسخة (instance context) الّتي استدعت الدّالة، أي إنّ `this` تُشير ضمن الدّالة عند تنفيذها إلى النُسخةَ المنشأة وليس الصّنف؛ بالطّبع ليس من المرغوب تطبيق الفكرة ذاتها على المُتغيّرات الأخرى مثل `setAt`، لأنّه من البديهيّ أن تختلف قيمتها بين نسخة وأخرى. لنُعد كتابة المثال السّابق بصورة أفضل:

```javascript
function Alarm(when) {
    this.setAt = when;
}

Alarm.prototype.enable = function() {
    var startAfter = new Date(this.setAt) - new Date;
    console.log("Alarm will wake you up after " + startAfter/1000 + " seconds");
    this.timeout = setTimeout(function() {
            console.log("Wake up!");
    }, startAfter);
}

Alarm.prototype.disable = function() {
    if (this.timeout) {
        clearTimeout(this.timeout);
        delete this.timeout;
        console.log("Alarm diabled");
    }
}

var a = new Alarm("2015-03-19 6:21 PM");
a.enable();
//  Alarm will wake you up after 30.243 seconds

// After 30 seconds...
// Wake up!
```

هذا الأسلوب في إنشاء الأصناف شائع جدًّا، وهو يتطلّب فهمًا دقيقًا لآليّة الوراثة في JavaScript؛ إذ يُبنى كلّ كائنٍ فيها على كائن آخر يُسمّى النّموذج البدئيّ (prototype)، ويقوم هذا الكائن الأخير على كائن ثالث أعلى منه في السّلسلة هو نموذجه البدئيّ، وهكذا حتّى نصل إلى `null` الّذي ليس له نموذج بدئيّ بحسب تعريف اللّغة. في مثالنا السّابق الكائن `Alarm.prototype` هو النّموذج البدئيّ للكائن `a`، وهذا يعني أنّ كل الخواصّ المُسندة إلى `Alarm.prototype` وما فوقه ستكون مُتاحة للكائن `a`، ولو كتابنا برنامجًا مُشابهًا بـJava لقُلنا إنّ `Alarm` صنفٌ وإنّ `a` نُسخة عن هذا الصّنف (instance). عندما نحاول الوصول إلى الخاصّة `a.setAt`، فإنّ مُفسِّر JavaScript يبدأ بالبحث عن هذه الخاصّة من أدنى سلسلة الوراثة، أي من الكائن `a` ذاته، فإنّ وجدها قرأها وأعاد قيمتها، وإلّا تابع البحث صعودًا إلى النّموذج البدئيّ وهكذا... وطبيعة JavaScript هذه هي ما سمح لنا بإسناد الوظيفتين `enable` و`disable` إلى `Alarm.prototype` مطمئنّين إلى أنّها ستكون مُتاحة عند قراءة `a.enable()‎` و`a.disable()‎`. يمكن التأكّد من النّموذج البدئيّ للكائن `a` كما يلي:

```javascript
Object.getPrototypeOf(a) == Alarm.prototype;
// true
```

###الأصناف في ECMAScript 6
يُقدّم الإصدار الأحدث من JavaScript مفهوم الأصناف (classes) بصورته التّقليديّة المعروفة في اللّغات الأخرى، إلّا أنّه ليس سوى أسلوب آخر لصياغة النّماذج البدئيّة (أو ما يُسمّى syntactic sugar)، وهذا يعني أنّه نموذج الوراثة في JavaScript لم يتغيّر. يمكننا إعادة كتابة المثال السّابق بصياغة الأصناف في ES6 كما يلي:

```javascript
class Alarm {
    constructor(when) {
        this.startAt = when;
    }
    
    enable() {
        var startAfter = new Date(this.setAt) - new Date;
        console.log("Alarm will wake you up after " + startAfter/1000 + " seconds");
        this.timeout = setTimeout(function() {
            console.log("Wake up!");
        }, startAfter);
    }
    
    disable() {
        if (this.timeout) {
            clearTimeout(this.timeout);
            delete this.timeout;
            console.log("Alarm diabled");
        }
    }
}

```

وستُسند الدّوال `enable()‎` و`disable()‎` إلى `Alarm.prototype` تمامًا كما في المثال الذي سبقه.

يُذكر أنّ استخدام `new` ليست الطّريقة الوحيدة لتشييد الكائنات، إذ يمكن استخدام الوظيفة `Object.create()‎` لتُعطي النّتيجة ذاتها:

```javascript
var a = Object.create(Alarm.prototype);

Object.getPrototypeOf(a) == Alarm.prototype;
// true
```

###إسناد الخواصّ إلى الكائنات
‏JavaScript لغة ديناميكية، وهذا يعني أنّه يمكن إضافة وحذف الخواصّ من الكائنات وتعديل نماذجها البدئيّة أثناء التّنفيذ، وهذا ما يمنحها القسم الأكبر من مرونتها ويجعلها مناسبة للاستخدام في بيئة مُعقّدة مثل بيئة الويب، وليس من الغرابة أن توفّر اللّغة وسائل متعدّدة لإسناد الخصائص إلى الكائنات لتلبية الحاجات المتنوّعة لتطبيقات الويب.

ماذا لو أردنا تغيير قيمة المنبّه في مثالنا السّابق بعد تفعيله؟ لربّما ترادونا للوهلة الأولى إمكانيّة تغيير قيمة الخاصّة `setAt` بالطّريقة التّقليدية:


```javascript
a.setAt = "2016-03-03 03:03 PM";
// or
a["setAt"] = "2016-03-03 03:03 PM";
```

لكنّ نتيجة هذا الفعل لن تكون كما يُتوقّع، فلو عدنا للمثال السابق وتمعّنا في خواصّه، للاحظنا عيبًا في كيفيّة عمل المُنبّه، إذ إنّ الخاصّة `setAt` مكشوفة ويمكن تغيير قيمتها في أيّ وقت، حتى بعد تفعيل المُنبّه، إلّا أنّ تغييرها بعدئذٍ لن يغيّر اللّحظة الحقيقيّة الّتي سينطلق فيها المنبّه كما يتضّح لنا عند قراءة النّصّ البرمجيّ، ولذا فنحن هنا أمام حلّين: إمّا منع تغيير قيمة الخاصّة `setAt` وجعلها للقراءة فقط بعد إنشاء المُنبّه، أو إيقاف المنبّه وإعادة ضبطه في كلّ مرّة تُغيّر فيها قيمة الخاصّة `setAt`، وكلا الحلّين متاحان إذا كنّا على علم بأساليب إسناد الخصائص في JavaScript.

توفّر اللّغة الوظيفة [`Object.defineProperty()‎`‏](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty) الّتي تسمح بتعريف خواصّ لكائن ما مع إمكانيّة التّحكم بتفاصيل هذه الخاصّة، ومن هذه التّفاصيل:

* هل الخاصّة قابلة للكتابة؟ (`writable`)
* هل يجب المرور على هذه الخاصّة عند سرد خواصّ الكائن؟ (`enumerable`)
* ما الذي يحدث عند إسناد قيمة للخاصّة؟ (`set`)
* ما الذي يحدث عند قراءة قيمة الخاصّة؟ (`get`)

وبهذا يمكننا بسهولة منع تغيير قيمة الخاصّة `setAt` بعد إسنادها:

```javascript
function Alarm(when) {
    Object.defineProperty(this, "setAt", { 
        value: when,
        writable: false
    })
}

Alarm.prototype.enable = function() {
    var startAfter = new Date(this.setAt) - new Date;
    console.log("Alarm will wake you up after " + startAfter/1000 + " seconds");
    this.timeout = setTimeout(function() {
            console.log("Wake up!");
    }, startAfter);
}

Alarm.prototype.disable = function() {
    if (this.timeout) {
        clearTimeout(this.timeout);
        delete this.timeout;
        console.log("Alarm diabled");
    }
}

var a = new Alarm("2015-03-19 7:51 PM");
a.setAt = new Date("2016-03-19");

console.log(a.setAt);

// "2015-03-19 7:51 PM"

```

لاحظ أنّ قيمة `setAt` لم تتغيّر. هذا حلّ جيّد، لكن سيكون من الأفضل السّماح للمُستخدم بتعديل قيمة المنبّه، وعندها سنلجأ لإيقاف المنّبه وإعادة ضبطه كما يلي:

```javascript
function Alarm(when) {
    var _hidden_value = new Date(when);
    Object.defineProperty(this, "setAt", { 
        set: function(newValue) {
            _hidden_value = new Date(newValue);
            if (this.timeout) {
                this.disable();
                console.log("Alarm changed to " + newValue);
                console.log("You need to re-enable the alarm for changes to take effect");
            }
        },
        get: function() {
            return _hidden_value;
        }
    })
}

Alarm.prototype.enable = function() {
    var startAfter = new Date(this.setAt) - new Date;
    console.log("Alarm will wake you up after " + startAfter/1000 + " seconds");
    this.timeout = setTimeout(function() {
            console.log("Wake up!");
    }, startAfter);
}

Alarm.prototype.disable = function() {
    if (this.timeout) {
        clearTimeout(this.timeout);
        delete this.timeout;
        console.log("Alarm diabled");
    }
}

var a = new Alarm("2016-03-03 03:03 PM")
a.enable();
// Alarm will wake you up after 30221933.66 seconds

a.setAt = "2015-03-19 8:05 PM";
// Alarm changed to 2015-03-19 8:05 PM
// You need to re-enable the alarm for changes to take effect

a.enable()
// Alarm will wake you up after 20.225 seconds

// After 20 seconds...
// Wake up!
```

لاحظ أنّنا سنحتاج إلى مُتغيّر سرِّيِّ (`‎_hidden_value`) نُخزّن فيه القيمة الفعليّة لوقت التّنبيه.

متى أستخدم هذا النّمط؟
-------------------
نمط المُشيّد لا يحتكر بنية مشروعك عند استخدامه؛ معنى هذا أنّه لا شيء يمنعك من استخدام نمط المُشيّد مع أي نمط آخر عند الحاجة لذلك، فيمكن (بل يشيع كثيرًا) استخدام المُشيّدات ضمن الوحدات (modules) ثمّ تصديرها لاستخدامها من موضع آخر في المشروع، ومثال ذلك أشياء مثل [`EventEmitter`‏](https://nodejs.org/api/events.html#events_class_events_eventemitter) و[Streams‏](https://nodejs.org/api/stream.html) في Node.js. كما يمكن بناء أنماط أخرى سنتعرّف عليها لاحقًا على أساس المُشيِّدات مثل نمط الكائن المُتفرّد (Singleton) ونمط المُراقِب (Observer pattern).
    
المصادر:
1. [شبكة مُطوّري موزيلّا: Inheritance and the prototype chain‏](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Inheritance_and_the_prototype_chain)
2. كتاب _[JavaScript Design Patterns‏](http://addyosmani.com/resources/essentialjsdesignpatterns/book/)_ لمؤلّفه Addy Osmani
