نمط الكائن المُتفرِّد (Singleton)
---------------------------
يأتي اسم هذا النّمط من كونه يمنع إنشاء أكثر من نسخة عن صنف (class). الطّريقة التّقليديّة لتطبيق هذا النمط هي إنشاء صنف يحوي وظيفة تُنشئ نسخة جديدة عن الصّنف إن لم تكن واحدة قد أُنشئت من قبل، أو تُرجِع النُسخة المُنشأة إن وُجدت.

تختلف الكائنات المُتفرِّدة عن الأصناف الثّابتة (static) بإمكانيّة تأخير إنشاء نسخة عن الصّنف، وذلك بسبب الحاجة إلى معلومات قد لا تكون متوفّرة عند بدء التّطبيق مثلاً.

تعمل الكائنات المتفرّدة في JavaScript كفضاء أسماء يعزل تفاصيل الشّيفرة عن النّطاق العام، ويقدّم نقطة موحَّدة للوصول إلى الدّوال المحتواة ضمنه.

يمكننا إنشاء كائن متفرّد كما يلي:

```javascript
var mySingleton = (function () {
 
  // هذا المتغيّر يحفظ مؤشِّرًا إلى الكائن المُتفرّد
  var instance;
 
  function init() {
 
    // الكائن المتفرّد
 
    // المتغيّرات والوظائف السّرّيّة
    function privateMethod(){
        console.log( "I am private" );
    }
 
    var privateVariable = "Im also private";
 
    var privateRandomNumber = Math.random();
 
    return {
 
      // المتغيّرات والوظائف العامّة
      publicMethod: function () {
        console.log( "The public can see me!" );
      },
 
      publicProperty: "I am also public",
 
      getRandomNumber: function() {
        return privateRandomNumber;
      }
 
    };
 
  };
 
  return {
 
    // اجلب نسخة الكائن المتفرّد إن وُجدت
    // أو أنشئ واحدة إن لم تُوجد
    getInstance: function () {
 
      if ( !instance ) {
        instance = init();
      }
 
      return instance;
    }
 
  };
 
})();
 
var myBadSingleton = (function () {
 
  // هذا المتغيّر يحفظ مؤشِّرًا إلى الكائن المُتفرّد
  var instance;
 
  function init() {
 
    // الكائن المتفرّد
 
    var privateRandomNumber = Math.random();
 
    return {
 
      getRandomNumber: function() {
        return privateRandomNumber;
      }
 
    };
 
  };
 
  return {
 
    // أنشئ نسخة جديدة دومًا
    getInstance: function () {
 
      instance = init();
 
      return instance;
    }
 
  };
 
})();
 
 
// الاستخدام:
 
var singleA = mySingleton.getInstance();
var singleB = mySingleton.getInstance();
console.log( singleA.getRandomNumber() === singleB.getRandomNumber() ); // true
 
var badSingleA = myBadSingleton.getInstance();
var badSingleB = myBadSingleton.getInstance();
console.log( badSingleA.getRandomNumber() !== badSingleB.getRandomNumber() ); // true
 
// ملاحظة: بما أنّنا نتعامل مع أرقام عشوائيّة، فهناك احتمالٌ رياضيّ قائم أن يكون 
// الرّقمان متماثلين، وإن كان ذلك مستبعدًا. باستثناء هذه الحالة، فإنّ المثال أعلاه صحيح.
```

ما يجعل الكائن مُتفرِّدًا هو أنّنا نطلب النّسخة في النّطاق العام باستدعاء  `MySingleton.getInstance()‎` ولا نستدعي `new MySingleton()‎` (على الأقل في اللّغات الثّابتة الأنواع)، مع أنّ كلا الأسلوبين مُمكن في JavaScript.

تُعرّف إمكانية تطبيق الكائنات المُتفرّدة بحسب كتاب أنماط التّصميم لعُصبة الأربعة* كما يلي:

* يجب أن تُوجد نسخة واحدة فقط من الصّنف، وأن يمكن الوصول إليها من نقطة معروفة.
* عندما يمكن توسيع هذه النّسخة بإنشاء صنف فرعيّ، فإنّه يمكن استخدام هذه النّسخة الموسّعة بدون تعديل الشّيفرة.

‏<sub>* عصبة الأربعة (Gang of Four أو اختصارًا GoF) مجموعة مكوّنة من أربعة علماء حاسوب هم Erich Gamma وRichard Helm وRalph Johnson وJohn Vlissides، لهم كتاب مشهور يتّحدث عن أنماط التّصميم في هندسة البرمجيّات عنوانه: [_Design Patterns: Elements of Reusable Object-Oriented Software_‏](https://en.wikipedia.org/wiki/Design_Patterns). (المترجم)</sub>

النّقطة الثّانية تُشير إلى حالة تُشبه المثال التّالي:

```javascript
mySingleton.getInstance = function(){
  if ( this._instance == null ) {
    if ( isFoo() ) {
       this._instance = new FooSingleton();
    } else {
       this._instance = new BasicSingleton();
    }
  }
  return this._instance;
};
```

في هذه الحالة، تعمل `getInstance` كمعمل (factory) ولا نحتاج إلى تعديل كل موضع في الشّيفرة يصل إلى هذه الدّالة. `FooSingleton` صنف فرعيّ من `BasicSingleton` ويُطبِّق الواجهة (interface) ذاتها.

ولكن لماذا تُعتبر إمكانيّة تأخير الإنشاء مهمّة في الكائنات المُتفرِّدة؟

>تفيد [إمكانيّة تأخير التّنفيذ] في C++‎ بحماية البرنامج من الاختلاف في ترتيب التّهيئة الدّيناميكيّة، وتعطي المٌبرمج إمكانيّة التّحكم بذلك.

من المُهمّ أن نميّز بين نسخة ثابتة من صنف (كائن) وكائن مُتفرّد، فمع أنّه من الممكن تطبيق الكائنات المتفرّدة بأسلوب الأصناف الثّابتة، إلا أنّه يمكن تطبيقها بالأسلوب الكسول (lazy) كذلك، أي تهيئتها فقط عند الحاجة إليها، دون حجز الذاكرة والموارد منذ البداية. ففي الكائنات الثّابتة الّتي يمكن تهيئتها مباشرة علينا أن نتأكّد من أنّ الشّيفرة تنفّذ دومًا بالتّرتيب ذاته (مثلاً إن كان `objCar` يحتاج `objWheel` عند تهيئته). هذا يجعل إدارة المشروع أكثر تعقيدًا عندما يصبح عدد ملفّاته ضخمًا.

الكائنات المتفرّدة والكائنات الثّابتة كلاهما نمطان مفيدان إن لم نبالغ في استخدامهما، والأمر ذاته ينطبق على كل أنماط التّصميم.

في التّطبيقات العمليّة، يكون نمط الكائن المتفرّد مُفيدًا عندما نحتاج كائنًا واحدًا فقط يُنسِّق بين مكوّنات المشروع. فيما يلي مثال عن استخدام هذا النّمط في سياق مشابه:

```javascript
var SingletonTester = (function () {
 
  // options: كائن يحوي إعدادات ضبط الكائن المُتفرّد
  // مثال:
  // var options = { name: "test", pointX: 5};
  function Singleton( options )  {
 
    // عيّن إعدادات الكائن إلى الكائن options
    // أو إلى كائن فارغ إن لم يُمرَّر شيء
    options = options || {};
 
    // عيّن بعض خصائص الكائن المتفرّد
    this.name = "SingletonTester";
 
    this.pointX = options.pointX || 6;
 
    this.pointY = options.pointY || 10;
 
  }
 
  // متغيّر يحفظ النسخة الوحيدة
  var instance;
 
  // أسلوب لمحاكاة للمتغيّرات والوظائف الثّابتة
  var _static  = {
 
    name:  "SingletonTester",
 
    // وظيفة لقراءة نسخة. تُرجِع نسخة وحيدة من كائن متفرّد.
    getInstance:  function( options ) {
      if( instance  ===  undefined )  {
        instance = new Singleton( options );
      }
 
      return  instance;
 
    }
  };
 
  return  _static;
 
})();
 
var singletonTest  =  SingletonTester.getInstance({
  pointX:  5
});
 
// سجّل ناتج pointX للتأكّد من سلامة الشّيفرة
// Outputs: 5
console.log( singletonTest.pointX );
```

على الرّغم من أنّ لهذا النّمط استخدامات مناسبة، إلا أنّ الحاجة لاستخدامه في JavaScript غالبًا ما تُشير إلى ضرورة إعادة تصميم المشروع. إذ يكون وجوده مؤشِّرًا إلى كون الوحدات في المشروع مرتبطة ببعضها بشدّة أو أنّ الشّيفرة موزّعة على أجزاء من المشروع أكثر من اللّازم. كذلك فقد يكون اختبار الكائنات المتفرّدة أكثر صعوبة لعدد من المُشكلات، تتراوح بين المُتطلبات الخفيّة، وصعوبة إنشاء عدّة نُسَخ، وصعوبة استبدال المُتطلبّات أثناء الاختبار... إلخ.

ينصح Miller Medeiros بالاطّلاع على [هذه](http://www.ibm.com/developerworks/webservices/library/co-single/index.html) المقالة الممتازة الّتي تتحدّث عن الكائنات المتفرّدة وعيوبها المتنوّعة، وكذلك بقراءة [هذه](http://misko.hevery.com/2008/10/21/dependency-injection-myth-reference-passing) المقالة الّتي تناقش كيف أنّ الكائنات المتفرّدة تزيد من احتمال ارتباط أجزاء المشروع ببعضها بشدّة. أنصح بقراءة كلتا المقالتين لأنّهما تطرحان عدّة نقاط مهمّة عن هذا النمط يجب الانتباه إليها.

_**ملاحظة**: هذا جزء مُترجم —بشيء من التصرف— عن كتاب [Learning JavaScript Design Patterns‏](http://addyosmani.com/resources/essentialjsdesignpatterns/book) لمؤلّفه Addy Osmani._
