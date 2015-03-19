نمط المُراقب (Observer)
----------------------
يتألف هذا النّمط من كائن (ندعوه الفاعل) يحفظ قائمة بالكائنات الّتي تعتمد عليه (والّتي ندعو كلًّا منها "مراقِبًا") ليقوم بإعلامها تلقائيًّا عندما يطرأ عليه أي تغيير في حالته.

عندما يحتاج الفاعل إلى إعلام الكائنات المُراقِبة بوقوع تغيير مهمّ، فإنّه يبُثُّ إعلامًا إليها، وقد يحتوي هذا الإعلام على بعض البيانات الّتي تتعلّق بموضوع الحدث.

عندما نريد لإحدى الكائنات المُراقبة أن تتوقّف عن تلقِّي التنبيهات بوقوع التّغييرات في الفاعل الّتي سُجِّلت فيه، فإنّه يمكن لهذا الفاعل إزالتها من قائمة الكائنات المراقِبة المسجّلة لديه.

من المفيد العودة إلى التّعريفات المنشورة في كتب أنماط التّصميم، لأنّها تكون غير مرتبطة بلغة برمجة معيّنة، وهذا يزوّدنا بفكرة أوسع عن استخدامها ومحاسِنها مع ازدياد خبرتنا. فيما يلي تعريف نمط المُراقِب كما ورد في كتاب _Design Patterns: Elements of Reusable Object-Oriented Software_ لعصبة الأربعة:

> "يهتمّ مُراقِب واحدٌ أو أكثر بحالة الفاعل، لذا فإنّه يقوم بتسجيل رغبته هذه لدى الفاعل بإلحاق نفسه به. عندما يتغيّر شيء ما في الفاعل، ويكون هذا التّغيّر مُهمِّا بالنّسبة للمراقِب، تُرسَل رسالة إعلام تستدعي وظيفة التّحديث في كلّ مراقب. عندما لا يرغب المراقِب بمتابعة تغيّرات حالة الفاعل بعد ذلك، يمكنه ببساطة حلّ ارتباطه."

يمكننا الآن أن نبني على ما تعلّمناه بتصميم نمط المُراقِب من المكوّنات التّالية:

* **الفاعل**: يحتفظ بقائمة من المُراقبين، ويسمح بحذف أيٍّ منهم أو إضافة مُراقبٍ جديد.
* **المُراقب**: يوفّر واجهة لتحديث الكائنات الّتي يجب إعلامها بتغيّر طرأ على حالة الفاعل.
* **الفاعل المرئيّ (ConcreteSubject)**: يبثُّ إعلامًا للمُراقبين عند تغيّر حالته، ويحتفظ بحالة المراقبين المرئيّين.
* **المُراقب المرئيّ (ConcreteObserver)**: يحتفظ بمرجع يُشير إلى الفاعل المرئيّ، ويعتمد واجهة تحديث للمراقب للتأكّد من من أنّ حالته موافقة لحالة الفاعل.

لنُنشئ أوّلًا قائمة بالمراقبين الّذين يعتمدون على الفاعل:

```javascript
function ObserverList(){
  this.observerList = [];
}
 
ObserverList.prototype.add = function( obj ){
  return this.observerList.push( obj );
};
 
ObserverList.prototype.count = function(){
  return this.observerList.length;
};
 
ObserverList.prototype.get = function( index ){
  if( index > -1 && index < this.observerList.length ){
    return this.observerList[ index ];
  }
};
 
ObserverList.prototype.indexOf = function( obj, startIndex ){
  var i = startIndex;
 
  while( i < this.observerList.length ){
    if( this.observerList[i] === obj ){
      return i;
    }
    i++;
  }
 
  return -1;
};
 
ObserverList.prototype.removeAt = function( index ){
  this.observerList.splice( index, 1 );
};
```

لنُنشئ الآن نموذج الفاعل ونسمح بإضافة وحذف وتنبيه المراقبين المُسجّلين في القائمة:

```javascript
function Subject(){
  this.observers = new ObserverList();
}
 
Subject.prototype.addObserver = function( observer ){
  this.observers.add( observer );
};
 
Subject.prototype.removeObserver = function( observer ){
  this.observers.removeAt( this.observers.indexOf( observer, 0 ) );
};
 
Subject.prototype.notify = function( context ){
  var observerCount = this.observers.count();
  for(var i=0; i < observerCount; i++){
    this.observers.get(i).update( context );
  }
};
```

ثمّ نُعرّف هيكلاً لإنشاء مُراقبين جديد. وظيفة `update` هنا ستُبدّل فيما بعد بدالّة تختلف بحسب المُراقب:

```javascript
// المُراقب
function Observer(){
  this.update = function(){
    // ...
  };
}
```

الآن سُننشئ في تطبيقنا المكوّنات المرئيّة التّالية:
* زرّ يضيف مربّعات اختيار إلى الصّفحة يمكن مُراقبتها
* مربّع اختيار سيلعب دور الفاعل، حيث يقوم بتنبيه مربّعات الاختيار الأخرى لتقوم بجعل حالتها موافقة لحالته
* عنصر في الصّفحة يحيط بمربّعات الاختيار الّتي ستُضاف إلى الصّفحة

بعد ذلك نُعرّف الفاعل المرئيّ (ConcreteSubject) والمراقب المرئيّ  (ConcreteObserver) اللّذين يتولّيان إضافة مُراقبين جُديد إلى الصّفحة وتحديث الواجهة. اطّلع على التّعليقات ضمن الشّيفرة أدناه لفهم وظيفة هذه المكوّنات في سياق هذا المثال.

```html
<button id="addNewObserver">Add New Observer checkbox</button>
<input id="mainCheckbox" type="checkbox"/>
<div id="observersContainer"></div>
```

```javascript
// وسّع كائنًا بخصائص من كائن آخر
function extend( extension, obj ){
  for ( var key in extension ){
    obj[key] = extension[key];
  }
}
 
// عناصر الصّفحة المرئيّة
 
var controlCheckbox = document.getElementById( "mainCheckbox" ),
  addBtn = document.getElementById( "addNewObserver" ),
  container = document.getElementById( "observersContainer" );
 
 
// الفاعل الحقيقيّ
 
// وسّع مربّع الاختيار واجعله فاعلاً
extend( new Subject(), controlCheckbox );
 
// سيؤدي النّقر على مربّع الاختيار إلى إعلام مُراقبيه بتغيّر حالته
controlCheckbox.onclick = function(){
  controlCheckbox.notify( controlCheckbox.checked );
};
 
addBtn.onclick = addNewObserver;
 
// المُراقِب الحقيقيّ
 
function addNewObserver(){
 
  // أنشئ مربّع اختيار جديد لإضافته إلى الصّفحة
  var check  = document.createElement( "input" );
  check.type = "checkbox";
 
  // وسّع مربّع الاختيار لجعله مُراقبًا
  extend( new Observer(), check );
 
  // بدّل دالّة التّحديث بأخرى خاصّة
  check.update = function( value ){
    this.checked = value;
  };
 
  // أضف هذا المُراقب إلى قائمة المراقبين في الفاعل
  controlCheckbox.addObserver( check );
 
  // أضف مربّع الاختيار إلى الصفحة
  container.appendChild( check );
}
```

###الاختلافات بين نمط المُراقِب ونمط النشر/الاشتراك
فهم نمط المُراقب أمرٌ مفيد، ولكن غالبًا ما يُستخدم في JavaScript تعديلٌ عليه يُسمّى نمط النشر/الاشتراك (Publish/Subscribe)، ومع أنّ النمطين متشابهان، إلّا أنّه يجب ملاحظة بعض الاختلافات بينهما.

في نمط المُراقب، يجب على المُراقب الّذي يرغب بمتابعة التغيّرات في الفاعل تسجيل نفسه في قائمة المراقبين الّتي يحتفظ بها الفاعل.

أمّا في نمط النشر/الاشتراك فإنّنا نستخدم قناةً تتوسّط بين هذين الطّرفين، فيبثّ أحدهما (النّاشر) أحداثًا (events) ذات مواضيع مختلفة تمرّ عبر هذه القناة لتتلقّاها الأطراف الأخرى (المُشتركون)، ممّا يلغي الحاجة لأن يعتمد أحد الطّرفين على الآخر مُباشرةً ويسمح للمشترك بتلقّي نوعيّة مُعيّنة من الأحداث الّتي يمكن أن تُرفق ببعض المعلومات الخاصّة بالحدث.

يختلف هذا عن نمط المُراقب، إذ يسمح لأيّ مُشترك بواجهة مناسبة لتولّي الأحداث (event handler) أن يُسجّل ويستقبل الأحداث الّتي يبثّها النّاشر المتعلقّة بموضوع معيّن.

فيما يلي مثال عن كيفيّة استخدام هذا النّمط:

```javascript
// برنامج بسيط للتّعامل مع رسائل البريد الإلكترونيّ الجديدة
 
// عدد الرّسائل الواردة
var mailCounter = 0;
 
// هيئّ المُشتركين الّذين يُنصتون لحدث اسمه "inbox/newMessage".
 
// اعرض عيّنة من الرّسائل الجديدة
var subscriber1 = subscribe( "inbox/newMessage", function( topic, data ) {
 
  // سجّل موضوع الحدث لأغراض التنقيح (debugging)
  console.log( "A new message was received: ", topic );
 
  // استخدم البيانات الّتي وصلت من الفاعل لعرض معاينة الرّسالة للمستخدم
  $( ".messageSender" ).html( data.sender );
  $( ".messagePreview" ).html( data.body );
 
});
 
// فيما يلي مُشترك يُنصت للحدث ذاته ولكنّه يستخدم البيانات لغرض آخر
 
// حدّث قيمة العدّاد ليوافق عدد الرّسائل
 
var subscriber2 = subscribe( "inbox/newMessage", function( topic, data ) {
 
  $('.newMessageCounter').html( ++mailCounter );
 
});
 
publish( "inbox/newMessage", [{
  sender:"hello@google.com",
  body: "Hey there! How are you doing today?"
}]);
 
// يمكننا فيما بعد إيقاف اشتراك المُشتركين كما يلي:
// unsubscribe( subscriber1 );
// unsubscribe( subscriber2 );
```

الفكرة الأساسيّة هنا هي تشجيع فصل المكوّنات عن بعضها قدر الإمكان، فبدلًا من جعل بعض الكائنات تستدعي وظائف كائنات أخرى مباشرةً، نقوم بجعل الأخيرة تشترك بنوعٍ معيّن من الأحداث الّتي تبثّها الأولى لتُعلمها بالتّغييرات عند حدوثها.

###المحاسن
يدفعنا كلا النّمطين السّابقين إلى التفكير مطوّلًا في العلاقة بين أجزاء تطبيقاتنا المُختلفة، ويُساعدنا في تحديد الطّبقات الّتي تحوي علاقات مباشرة بين الأجزاء والّتي يمكن إعادة صياغتها بأسلوب الفاعل والمُراقبين. يمكن استغلال هذه الفكرة عمليًّا لتجزئة التّطبيق إلى أجزاء أصغر، واعتماد أحدها على غيره أقل لنحصل في النّهاية على مشروعٍ أسهل إدارة، وربّما يمكن إعادة استخدام بعض تلك الأجزاء في مشاريع أخرى.

إحدى دوافع استخدام نمط المُراقب أيضًا الحاجةُ إلى ضمان الانسجام بين الكائنات المتشابهة دون أن يعتمد بعضها بشدّة على بعض، كأن يحتاج كائن إلى إعلام كائنات أخرى بتغيير طرأ عليه دون أن يضطّر إلى معرفة بنية هذه الكائنات وتفاصيلها.

يسمح استخدام كلا النّمطين السّابقين بإنشاء علاقة ديناميكيّة بين الفاعل والمُراقبين، وهذه العلاقة تُعطي التّطبيق مرونة كبيرة قد لا تكون مُتاحة عندما تكون أجزاء التّطبيق مرتبطة بشدّة فيما بينها.

ربّما لا يكون هذان النّمطان الخيار الأنسب لكلّ المشاريع، ولكنّهما يبقيان اثنين من أفضل أدوات تصميم المشاريع المنفصلة الأجزاء، ويجب أن يبقيا دومًا نُصب عينيّ كلّ مطوّر JavaScript.

###المساوئ
تنشأ بعض مساوئ هذين النّمطين من محاسنهما ذاتها؛ ففي نمط النّشر/الاشتراك يصبح من الصّعوبة بمكان التأكّد من أنّ أجزاء تطبيقاتنا تعمل كما يُتوقّع منها، وذلك لأنّ هذه الأجزاء منفصلة عن بعضها كما يفترض التّصميم.

لنقل مثلاً أنّ النّاشر يتوقّع أنّ واحدًا من مشتركيه يُنصت لأحداثه، ولنفترض أنّه يتوقّع أنّ هذا المُشترك يُسجّل تقارير الأخطاء الواقعة في التّطبيق الّتي يبثّها النّاشر؛ إذا تعطّل هذا المُشترِك أو لم يؤدِّ وظيفته كما ينبغي لسبب أو لآخر، فلن يعلم النّاشر بذلك، لأنّ طبيعة تصميمنا تفصل بين المكوّنات كما قلنا.

سيئِّة أخرى لهذا التّصميم: لا يمكن للُمشتركين أن يعلم أحدهم بوجود الآخر ولا أن يعلم باستبدال النّاشر بناشر آخر.

###تطبيقات لنمط النشر/الاشتراك
يلائِم تصميم النِّشر/الاشتراك بيئة JavaScript، لأنّها لغة قائمة على مبدأ وقوع الأحداث والإنصات إليها، وخصوصًا في المتصفّحات حيث تُعتمد الأحداث كطريقة رئيسيّة للتفّاعل مع مكوّنات الصّفحة.

ومع ذلك، فإنّ JavaScript لا تُقدّم وسائل مُدمجَة فيها لإنشاء أحداث خاصّة والإنصات لها، ربّما باستثناء `CustomEvent` الّذي يتبع للإصدار الثّالث من مواصفات DOM، وهذا يعني أنّه مرتبط بـDOM وليس مُفيدًا للاستخدام خارج المتصفّح.

للتّغلّب على هذه المشكلة، توفّر مكتبات JavaScript الشّهيرة مثل dojo وjQuery وYUI وسائل تُساعدِنا في تصميم نظام النّشر/الاشتراك دون عناء كبيرٍ. الفقرات التّالية تستعرض أمثلةً من هذه المكتبات:

```javascript
// النشر
 
// jQuery: $(obj).trigger("channel", [arg1, arg2, arg3]);
$( el ).trigger( "/login", [{username:"test", userData:"test"}] );
 
// Dojo: dojo.publish("channel", [arg1, arg2, arg3] );
dojo.publish( "/login", [{username:"test", userData:"test"}] );
 
// YUI: el.publish("channel", [arg1, arg2, arg3]);
el.publish( "/login", {username:"test", userData:"test"} );
 
 
// الاشتراك
 
// jQuery: $(obj).on( "channel", [data], fn );
$( el ).on( "/login", function( event ){...} );
 
// Dojo: dojo.subscribe( "channel", fn);
var handle = dojo.subscribe( "/login", function(data){..} );
 
// YUI: el.on("channel", handler);
el.on( "/login", function( data ){...} );
 
 
// إلغاء الاشتراك
 
// jQuery: $(obj).off( "channel" );
$( el ).off( "/login" );
 
// Dojo: dojo.unsubscribe( handle );
dojo.unsubscribe( handle );
 
// YUI: el.detach("channel");
el.detach( "/login" );
```
إذا كنت ترغب باستخدام نمط النّشر/الاشتراك دون الاعتماد على مكتبات ضخمة مثل السّابقة، فيمكنك استخدام [AmplifiyJS‏](http://amplifyjs.com) الّتي تقدّم تطبيقًا بسيطًا لهذا النّمط وغير مرتبط بمكتبة معيّنة. من المكتبات المُشابهة: [Radio.js‏](http://radio.uxder.com/) و[PubSubJS‏](https://github.com/mroderick/PubSubJS) و[Pure JS PubSub‏](https://github.com/phiggins42/bloody-jquery-plugins/blob/55e41df9bf08f42378bb08b93efcb28555b61aeb/pubsub.js) لكاتبها Peter Higgins.

تتوفّر لمستخدمي jQuery خيارات أخرى من بينها إضافة jQuery كتبها Peter Higgins مبنيّة على مكتبة Ben Alman على GitHub:

* مكتبة Pub/Sub كتبها Ben Alman ‏https://gist.github.com/661855 (أنصح بها)
* نسخة مطوّرة عن السّابقة لـRick Waldron‏ https://gist.github.com/705311
* مكتبة Pub/Sub في AmplifyJS‏ http://amplifyjs.com
* مكتبة كتبها Ben Truyman‏ https://gist.github.com/826794

سنُلقي الآن نظرة على نسخة مُبسّطة من نمط النّشر/الاشتراك أصدرتُها على GitHub باسم [pubsubz‏](http://github.com/addyosmani/pubsubz)، وتتضمّن المفاهيم الأساسيّة: الاشتراك، والنّشر، وإلغاء الاشتراك.

اخترتُ أنّ أستخدمها في الأمثلة لأنّها تحاول قدر الإمكان الالتزام بالمصطلحات الّتي يُتوقّع وجودها في نسخة JavaScript تقليدّيّة من هذا النّمط.

####تطبيق لنمط النشر/الاشتراك
```javascript
var pubsub = {};
 
(function(myObject) {
 
    // كائن يخزّن الأحداث الّتي يمكن بثّها والإنصات لها
    var topics = {};
 
    // معرّف للحدث
    var subUid = -1;
 
    // بثّ الأحداث المهمّة تحت اسم معيّن مع البيانات مرتبطة بها لإيصالها إلى المُشتركين
    
    myObject.publish = function( topic, args ) {
 
        if ( !topics[topic] ) {
            return false;
        }
 
        var subscribers = topics[topic],
            len = subscribers ? subscribers.length : 0;
 
        while (len--) {
            subscribers[len].func( topic, args );
        }
 
        return this;
    };
 
    // اشترك بالأحداث المهمّة مع دالّة تُستدعى عندما يقع الحدث
    myObject.subscribe = function( topic, func ) {
 
        if (!topics[topic]) {
            topics[topic] = [];
        }
 
        var token = ( ++subUid ).toString();
        topics[topic].push({
            token: token,
            func: func
        });
        return token;
    };
 
    // ألغِ الاشتراك من أحداث ذات موضوع معيّن، بناءً على معرّف 
    // محفوظ لهذا الاشتراك
    myObject.unsubscribe = function( token ) {
        for ( var m in topics ) {
            if ( topics[m] ) {
                for ( var i = 0, j = topics[m].length; i < j; i++ ) {
                    if ( topics[m][i].token === token ) {
                        topics[m].splice( i, 1 );
                        return token;
                    }
                }
            }
        }
        return this;
    };
}( pubsub ));
```

####مثال عن استخدام التّطبيق السّابق
نستخدم الآن المكتبة السّابقة لنشر الأحداث والاشتراك بها كما يلي:

```javascript
// برنامج آخر للتّعامل مع الرسائل
 
// مُسجّل للمواضيع والبيانات الّتي تصل إلى المُشترك
var messageLogger = function ( topics, data ) {
    console.log( "Logging: " + topics + ": " + data );
};
 
// يُنصت المشتركون للأحداث ثمّ يستدعون وظيفة (مثل messageLogger) عند وصول إشعار جديد
var subscription = pubsub.subscribe( "inbox/newMessage", messageLogger );
 
// يتولّى النّاشرون إرسال الإشعارات ذات المواضيع المختلفة
// مثال:
 
pubsub.publish( "inbox/newMessage", "hello world!" );
 
// أو
pubsub.publish( "inbox/newMessage", ["test", "a", "b", "c"] );
 
// أو
pubsub.publish( "inbox/newMessage", {
  sender: "hello@google.com",
  body: "Hey again!"
});
 
// بإمكاننا كذلك إلغاء الاشتراك إن لم نعد مهتمّين بتلقّي الإشعارات
pubsub.unsubscribe( subscription );
 
// بعد إلغاء الاشتراك، لن تُستدعى الدالة messageLogger لأنّ
// المُشتركين لا ينصتون إلى التغيّرات
pubsub.publish( "inbox/newMessage", "Hello! are you still there?" );
```

####مثال: الإشعارات في الواجهة المرئية
لنتخيّل أن لدينا تطبيق ويب يعرض تحديثات لحظيّة عن أسهم البورصة، قد يحتوي هذا التّطبيق على شبكة تعرض إحصاءات الأسهم وأسعارها، وعدّادًا يعرض وقت آخر تحديث لهذه الأرقام. عندما تتغيّر البيانات، ينبغي على التّطبيق أن يُحدّث القيم في الشّبكة وقيمة العدّاد. في هذا السّيناريو يمثِّل نموذج البيانات هذا (data model) دور الفاعل (subject) الذي يقوم بنشر التغيّرات إلى المُشتركين (عناصر الشّبكة والعدّاد).

يحدِّث المُشتركون أنفسهم بالبيانات المناسبة عندما يتلقّون إعلامًا بتغيّر طرأ على البيانات.

سُينصت المُشتركون في مثالنا إلى الموضوع `"newDataAvailable"` لمعرفة التّغيّر في أسعار الأسهم، وعندما يحدث ذلك، ستُستدعى الدّالّة `gridUpdate` لإضافة سطر جديد إلى الشّبكة يحوي المعلومات الجديدة، وسيُحدّث العدّاد ليوافق وقت آخر إضافة.

```javascript
// اقرأ التّاريخ والوقت المحليّين لاستخدامهما في الواجهة لاحقًا
getCurrentTime = function (){
 
   var date = new Date(),
         m = date.getMonth() + 1,
         d = date.getDate(),
         y = date.getFullYear(),
         t = date.toLocaleTimeString().toLowerCase();
 
        return (m + "/" + d + "/" + y + " " + t);
};
 
// أضف صفًّا جديدًا من البيانات إلى شبكتنا المُتخيّلة
function addGridRow( data ) {
 
   // ui.grid.addRow( data );
   console.log( "updated grid component with:" + data );
 
}
 
// حدّث الشّبكة المُتخيّلة لعرض تاريخ آخر تحديث
function updateCounter( data ) {
 
   // ui.grid.updateLastChanged( getCurrentTime() );
   console.log( "data last updated at: " + getCurrentTime() + " with " + data);
 
}
 
// حدّث الشّبكة بالبيانات الّتي أرسِلت إلى المُشتركين
gridUpdate = function( topic, data ){
 
  if ( data !== undefined ) {
     addGridRow( data );
     updateCounter( data );
   }
 
};
 
// اشترك بموضوع newDataAvailable
var subscriber = pubsub.subscribe( "newDataAvailable", gridUpdate );
 
// فيما يلي تمثيل لكيفيّة تحديث بياناتنا
// يمكن أن يتمّ هذا عن طريق طلبات AJAX تُرسل إعلامًا
// بوجود بيانات جديدة إلى بقيّة أجزاء التّطبيق.
 
// انشر التّغيرات تحت موضوع gridUpdated
pubsub.publish( "newDataAvailable", {
  summary: "Apple made $5 billion",
  identifier: "APPL",
  stockPrice: 570.91
});
 
pubsub.publish( "newDataAvailable", {
  summary: "Microsoft made $20 million",
  identifier: "MSFT",
  stockPrice: 30.85
});
```

####مثال: فصل الاعتماد بين أجزاء التطبيق باستخدام أسلوب النشر/الاشتراك بحسب Ben Alman
هذا المثال عن تطبيق لتقييم الأفلام، وسنستخدم فيه إضافة jQuery لـBen Alman لنتعرّف كيف يمكن الفصل بين مكوّنات الواجهة المرئيّة. لاحظ أنّ إرسال التّقييم لا يغيّر شيئًا سوى نشر حقيقة أنّ مستخدمًا جديدًا قد أضاف تقييمًا، ويُترك التّصرّف في هذه البيانات إلى المُشتركين، في حالتنا هذه سنحفظ هذه البيانات في مصفوفات مُعرّفة في التّطبيق ثمّ نعرضها في الواجهة باستخدام وظيفة `‎.template()‎` من مكتبة Underscore.


#####‏HTML والقوالب
```html
<script id="userTemplate" type="text/html">
   <li><%= name %></li>
</script>
 
 
<script id="ratingsTemplate" type="text/html">
   <li><strong><%= title %></strong> was rated <%= rating %>/5</li>
</script>
 
 
<div id="container">
 
   <div class="sampleForm">
       <p>
           <label for="twitter_handle">Twitter handle:</label>
           <input type="text" id="twitter_handle" />
       </p>
       <p>
           <label for="movie_seen">Name a movie you've seen this year:</label>
           <input type="text" id="movie_seen" />
       </p>
       <p>
 
           <label for="movie_rating">Rate the movie you saw:</label>
           <select id="movie_rating">
                 <option value="1">1</option>
                  <option value="2">2</option>
                  <option value="3">3</option>
                  <option value="4">4</option>
                  <option value="5" selected>5</option>
 
          </select>
        </p>
        <p>
 
            <button id="add">Submit rating</button>
        </p>
    </div>
 
 
 
    <div class="summaryTable">
        <div id="users"><h3>Recent users</h3></div>
        <div id="ratings"><h3>Recent movies rated</h3></div>
    </div>
 
 </div>
```

#####‏JavaScript
```javascript
;(function( $ ) {
 
  // ابنِ القوالب واحتفظ بنسخة مؤقتّة جاهزة للاستخدام
  var
    userTemplate = _.template($( "#userTemplate" ).html()),
    ratingsTemplate = _.template($( "#ratingsTemplate" ).html());
 
  // اشترك بموضوع المستخدمين الجديد
  // لإضافة مستخدم إلى قائمة المستخدمين الّذي قيّموا الفيلم
  $.subscribe( "/new/user", function( e, data ){
 
    if( data ){
 
      $('#users').append( userTemplate( data ));
 
    }
 
  });
 
  // اشترك بموضوع تقييم جديد. يحوي الحدث بيانات مكوّنة من العنوان والتقييم
  // تُضاف التقييمات الجديد إلى قائمة خاصّة بالتّقييمات.
  $.subscribe( "/new/rating", function( e, data ){
 
    if( data ){
 
      $( "#ratings" ).append( ratingsTemplate( data ) );
 
    }
 
  });
 
  // تولَّ إنشاء مستخدم جديد
  $("#add").on("click", function( e ) {
 
    e.preventDefault();
 
    var strUser = $("#twitter_handle").val(),
       strMovie = $("#movie_seen").val(),
       strRating = $("#movie_rating").val();
 
    // أعلِم التّطبيق بإنشاء مستخدم جديد
    $.publish( "/new/user",  { name: strUser } );
 
    // أعلم التّطبيق بوجود تقييم جديد
    $.publish( "/new/rating",  { title: strMovie, rating: strRating} );
 
    });
 
})( jQuery );
```

####مثال: فصل الاعتماد بين أجزاء تطبيق ويب يستخدم jQuery وAJAX
في مثالنا الأخير، سنُلقي نظرة على فصل أجزاء المشروع بشكل عمليّ باستخدام نمط النشر/الاشتراك منذ بداية تطوير التّطبيق مما يُجنّبنا تعقيد إعادة صياغته فيما بعد.

كثيرًا ما نحتاج إلى تنفيذ أكثر من إجراء واحد عندما نستقبل جوابًا لطلب AJAX في مشاريعنا المُعتمدة على AJAX بشدّة. إحدى طرق تحقيق ذلك كتابة كلّ الشّيفرة المسؤولة عن هذه الإجراءات جميعها في الاستدعاء الرّاجع (callback) عند نجاح الطّلب، ولكن لهذا سيّئات عديدة.

ربط أجزاء التّطبيقات بهذه الصّورة سيؤدّي إلى عناء أكبر عند الحاجة إلى إعادة استخدام بعض الوظائف من المشروع بسبب اعتماد هذه الأجزاء على بعضها أكثر من اللّازم. الخلاصة أنّ كتابة كلّ هذه الإجراءات مباشرةً ضمن استدعاء الطّلب ممكنة، ولكنّها غير ملائمة عندما نريد أن نُرسل الطّلب ذاته ولكن بدون تنفيذ الإجراءات ذاتها كلّ مرة، لأنّ ذلك يعني إعادة كتابة أجزاء من الشّيفرة عدّة مرّات. من الأفضل إذن أن نستخدم نمط النّشر/الاشتراك من البداية بدل من العودة إلى كلّ استدعاء للطّلب ذاته وتوحيد الشّيفرة بهذه الطّريقة، مُوفِّرين بذلك وقتًا طويلاً.

باستخدام المُراقبين، يمكننا عزل الإشعارات ذات المواضيع المختلفة على مستوى التّطبيق  واستخدامها للتحكّم بأجزاءه عند أيّ مستوى، وهذا أمرٌ لا يمكن تحقيقه بالسهولة ذاتها بأنماط التّصميم الأخرى.

لاحظ في مثالنا التّالي كيف أنّ إشعارًا وحيدًا يُبثّ عندما يرغب المستخدم بإجراء عمليّة بحث، ثمّ يُبثّ إشعار آخر عندما يصل جواب الطّلب وتصبح البيانات جاهزة للاستخدام. يُترك الخيار للمُراقبين للتصرّف بحقيقة وقوع هذا الحدث (والبيانات المتوفّرة معه). لو شئنا أنّ نستخدم 10 مُراقبين مختلفين ويتصرّف كلٌُ منهم بالبيانات المُتاحة بطريقة أخرى، فلا شأن لطلب AJAX بذلك، ولا حاجة لأن نُغيّر في الشّيفرة الّتي تستدعيه شيئًا، فواجبه الوحيد هي إرسال الطّلب وإعادة البيانات ثمّ إرسالها لكلّ مراقب يرغب باستخدامها. الفصل بين الاهتمامات بهذه الطّريقة يجعل شيفرتنا أبسط وأنظف.

#####HTML والقوالب
```html
<form id="flickrSearch">
 
   <input type="text" name="tag" id="query"/>
 
   <input type="submit" name="submit" value="submit"/>
 
</form>
 
 
 
<div id="lastQuery"></div>
 
<ol id="searchResults"></ol>
 
 
 
<script id="resultTemplate" type="text/html">
    <% _.each(items, function( item ){  %>
        <li><img src="<%= item.media.m %>"/></li>
    <% });%>
</script>
```

#####JavaScript
```javascript
;(function( $ ) {
 
   // ابنِ القوالب واحتفظ بنسخة مؤقتّة جاهزة للاستخدام
   var resultTemplate = _.template($( "#resultTemplate" ).html());
 
   // اشترك بالأحداث المتعلقّة بوسوم البحث
   $.subscribe( "/search/tags" , function( e, tags ) {
       $( "#lastQuery" )
                .html("<p>Searched for:<strong>" + tags + "</strong></p>");
   });
 
   // اشترك بأحداث نتائج البحث الجديدة
   $.subscribe( "/search/resultSet" , function( e, results ){
 
       $( "#searchResults" ).empty().append(resultTemplate( results ));
 
   });
 
   // أرسل طلبًا بالبحث ثم انشر حدثًا موضوعه /search/tags 
   $( "#flickrSearch" ).submit( function( e ) {
 
       e.preventDefault();
       var tags = $(this).find( "#query").val();
 
       if ( !tags ){
        return;
       }
 
       $.publish( "/search/tags" , [ $.trim(tags) ]);
 
   });
 
 
   // اشترك بالوسوم الجديدة الّتي تُنشر ونفّذ
   // بحثًا باستخدامها، ما إن تصل البيانات الجديدة
   // انشرها لبقيّة أجزاء التّطبيق لتتمكن من استخدامها
 
   $.subscribe("/search/tags", function( e, tags ) {
 
       $.getJSON( "http://api.flickr.com/services/feeds/photos_public.gne?jsoncallback=?" ,{
              tags: tags,
              tagmode: "any",
              format: "json"
            },
 
          function( data ){
 
              if( !data.items.length ) {
                return;
              }
 
              $.publish( "/search/resultSet" , { items: data.items } );
       });
 
   });
 
 
})( jQuery );
```

الخلاصة: يفيد نمط المُراقِب بالفصل بين أجزاء التّطبيقات من عدّة نواحي، وإن لم تستخدمه من قبل فإنّني أنصحك باختيار إحدى المكتبات السّابقة وتجربتها ولو مرّة، فهي من أسهل أنماط التّصميم تعلّمًا وأكثرِها قدرةً في الوقت ذاته.

_**ملاحظة**: هذا جزء مُترجم —بشيء من التصرف— عن كتاب [Learning JavaScript Design Patterns‏](http://addyosmani.com/resources/essentialjsdesignpatterns/book) لمؤلّفه Addy Osmani._
