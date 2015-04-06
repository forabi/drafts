تعلّم jQuery‏ (6): AJAX والوعود المُؤجّلة (Deferreds)
============================================

##‏AJAX
‏AJAX هي اختصار للعبارة "asynchronous JavaScript and XML"، وهي وسيلة لجلب البيانات من الخادوم دون الحاجة لإعادة تحميل الصّفحة، وهي تقوم على استخدام كائن مُتاح في المتصفّح اسمه `XMLHttpRequest` (أو XHR اختصارًا) لإرسال الطّلب إلى الخادوم ثمّ التّعامل مع البيانات الّتي يُجيب بها الخادوم.

تُوفّر jQuery الوظيفة ‎`$.ajax`‎ (ووظائف أخرى مرافقة مُختصرة) لتسهيل العمل مع طلبات XHR في جميع المتصفّحات.

###‏‎$.ajax‎
بإمكاننا استخدام الوظيفة ‎`$.ajax()`‎ المُرفقة مع jQuery بعدّة أساليب: إحداها أن نُمرّر إليها كائنًا يحوي الإعدادات فقط، أو أن نُمرّر الرّابط مع أو بدون كائن الإعدادات. لنُلقِ نظرة على الأسلوب الأول:

```javascript
// أنشئ دالّة الاستدعاء الرّاجع الّتي ستُنفّذ عندما ينجح طلب AJAX
var updatePage = function( resp ) {
  $( '#target').html( resp.people[0].name );
};

// وعندما يفشل
var printError = function( req, status, err ) {
  console.log( 'something went wrong', status, err );
};

// أنشئ كائن الإعدادات الذي يصف الطّلب
var ajaxOptions = {
  url: '/data/people.json',
  dataType: 'json',
  success: updatePage,
  error: printError
};

// أرسل الطّلب
$.ajax(ajaxOptions);
```

بإمكانك طبعًا أن تُمرّر كائنًا حرفيًّا مباشرةً إلى الوظيفة‎`$.ajax()` ‎ وأن تستخدم دالّة مجهولة محلّ `success` و`error`، هذا الأسلوب كتابته أسهل، وصيانته في المستقبل أسهل:

```javascript
$.ajax({
  url: '/data/people.json',
  dataType: 'json',
  success: function( resp ) {
    $( '#target').html( resp.people[0].name );
  },
  error: function( req, status, err ) {
    console.log( 'something went wrong', status, err );
  }
});
```

كما قلنا، بإمكانك استخدام الوظيفة‎`$.ajax()` ‎ بأسلوب ثانٍ، وذلك بتمرير الرّابط أوّلًا ثمّ كائن الإعدادات ثانيًا (ليس إلزاميًّا). يُفيدك هذا في حال رغبت في استخدام الإعدادات المبدئيّة للوظيفة أو في حال رغبت في استخدام كائن الإعدادات نفسه لأكثر من رابط:

```javascript
$.ajax( '/data/people.json', {
  type: 'GET',
  dataType: 'json',
  success: function( resp ) {
    console.log( resp.people );
  },
  error: function( req, status, err ) {
    console.log( 'something went wrong', status, err );
  }
});
```

في المثال السّابق، لا تشترط الوظيفة سوى الرّابط، ولكنّ إضافة كائن الإعدادات تسمح لنا بإخبار jQuery بنوع البيانات الّتي نُرسلها، وأي فعل HTTP نستخدمه (POST، GET، إلخ...‏‏)، وما نوع البيانات الّتي نتوقّع استقبالها من الخادوم، وما الّذي يجب فعله إن نجح الطّلب أو فشل...

اطّلع على [وثائق الوظيفة‎`$.ajax()` ‎](http://api.jquery.com/jQuery.ajax/#jQuery-ajax-settings) لقراءة كامل الخيارات الّتي يمكن إضافتها إلى كائن الإعدادات.

###‏A في AJAX تعني "لامتزامن"
تجري طلبات AJAX بصورة _لا متزامنة_، وهذا يعني أنّ الوظيفة‎`$.ajax` ‎ تنتهي قبل انتهاء الطّلب، وقبل أن تُستدعى دّالة `success`، أيّ أنّ جملة `return` تُنفّذ قبل أن يصل جواب الطّلب. فالدّالة `getSomeData` في المثال التّالي ستُعيد قيمة `data` قبل أن تُعرّف، مما يؤدّي إلى وقوع خطأ:

**تحذير: نصّ برمجيّ غير سليم**

```javascript
var getSomeData = function() {
  var data;

  $.ajax({
    url: '/data/people.json',
    dataType: 'json',
    success: function(resp) {
      data = resp.people;
    }
  });

  return data;
}

$( '#target' ).html( getSomeData().people[0].name );
```

###‏X في AJAX تعني JSON!
وضع المصطلح [AJAX](http://www.adaptivepath.com/ideas/ajax-new-approach-web-applications) عام 2005 ليصف طريقة لجلب البيانات من الخادوم دون الحاجة لإعادة تحميل كامل الصّفحة. في ذلك الوقت، كانت الصّيغة الأكثر شيوعًا للبيانات الّتي تُرسلها الخوادم هي [XML](http://en.wikipedia.org/wiki/XML)، أمّا اليوم، فإنّ [JSON](http://www.json.org/json-ar.html) هي الصّيغة الّتي تعتمدها أكثر التّطبيقات الحديثة.

صيغة JSON في أساسها هي سلسلة نصّيّة (string) تُمثّل البيانات، وتبدو مُشابهة كثيرًا لكائن JavaScript عاديّ، ولكنّها لا تستطيع تمثيل كلّ أنواع البيانات الّتي يستطيع كائن JavaScript تمثيلها. فمثلًا: لا يمكن لـJSON تمثيل كائنات التّاريخ (`Date`) ولا الدّوال (functions). فيما يلي مثال عن نصّ JSON، لاحظ كيف تُحاط كلّ أسماء الخصائص بعلامتي اقتباس مُضاعفتين:

```json
{ "people" : [
  {
    "name" : "Ben",
    "url" : "http://benalman.com/",
    "bio" : "I create groovy websites, useful jQuery plugins, and play a mean funk bass. I'm also Director of Pluginization at @bocoup."
  },
  {
    "name" : "Rebecca",
    "url" : "http://rmurphey.com",
    "bio" : "Senior JS dev at Bocoup"
  },
  {
    "name" : "Jory",
    "url" : "http://joryburson.com",
    "bio" : "super-enthusiastic about open web education @bocoup. lover of media, art, and fake mustaches."
  }
] }
``

تذكّر أنّ _JSON هو تمثيل نصّيّ لكائن_، ما يعني أنّه يجب تفسير السّلسلة النّصيّة لتحويلها إلى كائن JavaScript عاديّ قبل التّعامل معها. عندما تعمل مع جواب ورد من الخادوم بصيغة JSON، فإنّ jQuery تتولّى هذه المهمّة عنك. ولكن من المهمّ التمييز بين الكائنات الفعليّة، وطريقة تمثيلها في JSON.

_ملاحظة: إن أردت إنشاء سلسلة JSON نصّيّة من كائن JavaScript أو تفسير سلسلة JSON نصّيّة لتحويلها إلى كائن JavaScript دون الاستعانة بـjQuery، فإنّ المُتصفّحات الحديثة تُقدّم الوظيفتين ‎`JSON.stringify()`‎ و‎`JSON.parse()`‎، ويمكن إضافة هذه الخصائص إلى المُتصفّحات القديمة باستخدام المكتبة [`json2.js`](https://github.com/douglascrockford/JSON-js). توفّر jQuery أيضًا وظيفة ‎`jQuery.parseJSON()`‎، الّتي توافق الوظيفة ‎`JSON.parse()`‎ في المتصفّحات، إلّا أنّها لا توفّر وظيفة تُقابل ‎`JSON.stringify()`‎._

###وظائف مُختصرة
إن كان كلّ ما نريده إرسال طلب بسيط، دون الاهتمام بالتّعامل مع الأخطاء الّتي قد تقع، فإنّ jQuery تُوفّر وظائف مُختصرة تسمح لنا بفعل ذلك. تستقبل كل وظيفة مُختصرة رابطًا وكائن إعدادات غير إلزاميّ، ودالّة تُستدعى عند نجاح الطّلب فقط:

```javascript
$.get( '/data/people.html', function( html ){
  $( '#target' ).html( html );
});

$.post( '/data/save', { name: 'Rebecca' }, function( resp ) {
  console.log( resp );
});
```

###إرسال البيانات والعمل مع النّماذج
بإمكاننا إرسال بيانات مع طلبنا بتعيين قيمة للخاصة `data` في كائن الإعدادات، أو تمرير كائن كمُعامل ثانٍ للوظائف المُختصرة.

ستُضاف هذه البيانات إلى الرّابط في طلبات GET بصورة "جملة استعلام" (query string)، أمّا في طلبات POST فإنّها ستُرسل كبيانات نموذج. توفّر jQuery وظيفة مُفيدة ‎`.serialize()`‎ الّتي تستقبل مُدخلات نموذج وتُحوّلها إلى صيغة "جملة استعلام" (مثل `field1name=field1value&field2name=field2value...`):

```javascript
$( 'form' ).submit(function( event ) {
  event.preventDefault();

  var form = $( this );

  $.ajax({
    type: 'POST',
    url: '/data/save',
    data: form.serialize(),
    dataType: 'json',
    success: function( resp ) {
      console.log( resp );
    }
  });
});
```

###‏jqXHR
تُعيد‎`$.ajax()` ‎ والوظائف المُختصرة المرافقة لها، كائن jqXHR (اختصارًا _لـjQuery XML HTTP Request_) والّذي يتضمّن وظائف مُفيدةً كثيرة. بإمكاننا إرسال طلب باستخدام ‎`$.ajax()`‎ ثمّ حفظ كائن jqXHR في مُتغيّر:

```javascript
var req = $.ajax({
  url: '/data/people.json',
  dataType: 'json'
});
```

بإمكاننا استخدام هذا العنصر لربط الاستدعاءات الرّاجعة بالطّلب، حتّى بعد أن يكتمل الطّلب. بإمكاننا مثلًا استخدام الوظيفة ‎`.then()`‎ (ثُمَّ) لإرفاق استدعاءي نجاح الطّلب وفشله، إذ تقبل ‎`.then()`‎ دالّة أو اثنتين، تستدعى الأولى عند نجاح الطّلب، والثّانية إن فشل:

```javascript
var success = function( resp ) {
  $( '#target' ).append(
    '<p>people: ' + resp.people.length + '</p>'
  );
  console.log( resp.people );
};

var err = function( req, status, err ) {
  $( '#target' ).append( '<p>something went wrong</p>' );
};

req.then( success, err );
req.then(function() {
  $( '#target' ).append( '<p>it worked</p>' );
});
```

بإمكاننا استدعاء ‎`.then()`‎ على كائن الطّلب قدر ما نشاء، وستُنفّذ الاستدعاءات الرّاجعة بالتّرتيب ذاته الّتي أرفقت وفقه.

إن لم نُرد إرفاق استدعاءي النّجاح والفشل معًا، فبإمكاننا استخدام الوظيفتين ‎`.done()`‎ و‎`.fail()`‎ على كائن الطّلب:

```javascript
req.done( success );
req.fail( err );
```

لو أردنا إرفاق استدعاء راجعٍ يُنفَّذ دومًا، بغض النّظر عن نجاح الطّلب أو فشله، فيمكننا استخدام الوظيفة ‎`.always()`‎ على كائن الطّلب:

```javascript
req.always(function() {
  $( '#target' )
    .append( '<p>one way or another, it is done now</p>' );
});
```

###‏JSONP
يستغرب كثيرٌ من المبتدئين في JavaScript فشل طلبات XHR الّتي يرسلونها إلى نطاق آخر على الإنترنت، فمثلاً: يحاول بعض المُطوّرين جلب بيانات من واجهة برمجيّة من طرف ثالث (third-party API)، ليفاجؤوا بفشل الطّلب باستمرار.

السّبب وراء ذلك أنّ المُتصفّحات لا تسمح بإرسال طلبات XHR إلى نطاقات إنترنت أخرى لأسباب أمنيّة، ولكنّ بعض الواجهات البرمجيّة تُعيد البيانات بصيغة JSONP (اختصارًا لـJSON with Padding)، الّتي تسمح للمُطوّرين بجلب البيانات متجاوزين حظر المُتصفّح.

الحقيقة أن JSONP ليس طلب AJAX فعليًّا، فهو لا يستخدم طلب XHR الّذي يوفّره المُتصفّح، بل يعمل بإدراج وسم `<script>` في صفحة الويب، الّذي يحوي بدوره البيانات المطلوبة، مُحاطة بدالّة تُعيد هذه البيانات عند استدعائها. ليس هذه التّفاصيل مهمّة الآن، لأنّ jQuery تسمح لك بطلب JSONP كما لو كان XHR باستخدام الوظيفة ‎`$.ajax()`‎ بتعيين نوع البيانات `dataType` إلى `'jsonp'` في كائن الإعدادات.

```javascript
$.ajax({
  url: '/data/search.jsonp',
  data: { q: 'a' },
  dataType: 'jsonp',
  success: function( resp ) {
    $( '#target' ).html( 'Results: ' + resp.results.length );
  }
});
```
_ملاحظة: عادةً ما توفّر الواجهات البرمجيّة خيارًا لتعيين اسم الدّالّة الّتي تُحيط بالبيانات والّتي ستُستدعى في عنوان الرّابط. عادةً ما يكون هذا اسم مُعامل الرّابط `callback`، وهذا ما تتوقّعه jQuery مبدئيًّا، إلّا أن بإمكانك تغييره بتعيين قيمة للخاصة `jsonp` في كائن الإعدادات الّذي تُمرّره لـ ‎`$.ajax()`‎._

بإمكانك أيضًا استخدام الوظيفة المُختصرة ‎`$.getJSON()`‎ لإرسال طلب JSONP، حيث تستطيع jQuery تمييزه من خلال وجود ‎`callback=?`‎ أو ما يشبهها في الرّابط:

```javascript
$.getJSON( '/data/search.jsonp?q=a&callback=?',
  function( resp ) {
    $( '#target' ).html( 'Results: ' + resp.results.length );
  }
);
```

مشاركة الموارد عبر الأصول ([cross-origin resource sharing](http://en.wikipedia.org/wiki/Cross-origin_resource_sharing) أو CORS اختصارًا) هي خيارٌ آخر للسّماح بالطّلبات العابرة للأصول. ولكنّها غير مدعومة في المتصفّحات القديمة، كما أنّها تحتاج تهيئة خاصّة على الخادوم وتعديل ترويسات الطّلبات في XHR لتعمل.

##الكائنات المُؤجّلة (Deferreds)
ليست كائنات jqXHR الّتي تعرّفنا عليها إلا "نكهة" خاصّة ممّا يُعرف "بالكائنات المؤجّلة". تسمح jQuery لك بإنشاء كائنات مؤجّلة بنفسك، والّتي يمكن الاستفادة منها في تسهيل التّعامل مع الأوامر اللامتزامنة، فهي توفّر طريقة للاستجابة لعمليّة تجري بصورة غير متزامنة بعد نجاحها أو فشلها، وتجنّبك الحاجة لكتابة استدعاءات راجعة مُتداخلة فيما بينها.

##‏‎$.Deferred‎
بإمكانك إنشاء كائنٍ مؤجّل باستخدام `‏‎$.Deferred()‎`. في المثال التّالي نُنفّذ دالة داخل `setTimeout`، ثمّ "نفي" (resolve) بوعدنا بإعادة القيمة الّتي تُرجعها الدّالة هذه. نُعيد الوعد (promise)، وهو كائن يمكن ربط الاستدعاءات الرّاجعة به، ولكنّه لا يؤثّر في نتيجة الكائن المؤجّل بحدّ ذاته. بإمكاننا "الإخلاف" (reject) بالوعد إذا وقع خطأ ما أثناء عمل الدّالّة:

```javascript
function doSomethingLater( fn, time ) {
  var dfd = $.Deferred();

  setTimeout(function() {
    dfd.resolve( fn() );
  }, time || 0);

  return dfd.promise();
}

var promise = doSomethingLater(function() {
  console.log( 'This function will be called in 100ms' );
}, 100);
```

##‏‎.then()‎ و‎.done()‎ و‎.fail()‎ و‎.always()‎
يمكننا ربط دوالّ تتولّى حالات الخطأ والنّجاح بالوعود، تمامًا كما في كائنات jqXHR:

```javascript
function doSomethingLater( fn, time ) {
  var dfd = $.Deferred();

  setTimeout(function() {
    dfd.resolve( fn() );
  }, time || 0);

  return dfd.promise();
}

var success = function( resp ) {
  $( '#target' ).html( 'it worked' );
};

var err = function( req, status, err ) {
  $( '#target' ).html( 'it failed' );
};

var dfd = doSomethingLater(function() { /* ... */ }, 100);

dfd.then( success, err );
```

##‏‎.pipe()‎
بإمكاننا استخدام الوظيفة `‏‎.pipe()‎` للوعود للاستجابة إلى القيمة الّتي تُوفى وذلك بتعديلها ثمّ إعادة كائن مؤجّل جديد.

تعمل الوظيفة `‏‎.then()‎` بدءًا من الإصدارة 1.8 من jQuery كما تعمل الوظيفة `‏‎.pipe()‎`.

```javascript
function doSomethingLater( fn, time ) {
  var dfd = $.Deferred();

  setTimeout(function() {
    dfd.resolve( fn() );
  }, time || 0);

  return dfd.promise();
}

var dfd = doSomethingLater(function() { return 1; }, 100);

dfd
  .pipe(function(resp) { return resp + ' ' + resp; })
  .done(function(upperCaseResp) {
    $( '#target' ).html( upperCaseResp );
  });
```

##التّعامل مع العمليّات الّتي قد تكون لامتزامنة
أحيانًا تكون لدينا وظيفة قد تعمل بصورة متزامنة أو لا متزامنة وفق ظروف مُعيّنة، فمثلًا: دالّة تقوم بعمليّة لا متزامنة أوّل مرّة تُستدعى فيها، ثمّ تُخزّن القيمة الّتي أنتجتها العمليّة لتُعيدها مباشرةً عند استدعاءها مُستقبلًا. في هذه الحالة يمكننا الاستفادة من ‎`$.when()`‎ للاستجابة لكلا الحالتين:

```javascript
function maybeAsync( num ) {
  var dfd = $.Deferred();

  // أعِد وعدًا مؤجّلًا عندما num === 1
  if ( num === 1 ) {
    setTimeout(function() {
      dfd.resolve( num );
    }, 100);
    return dfd.promise();
  }

  // أنهِ مباشرة فيما سوى ذلك، مُعيدًا num
  return num;
}

// هذا سيُجرى بصورة غير متزامنة ويعِد بإعادة 1
$.when( maybeAsync( 1 ) ).then(function( resp ) {
  $( '#target' ).append( '<p>' + resp + '</p>' );
});

// هذا سُيعيد 0 مُباشرةً
$.when( maybeAsync( 0 ) ).then(function( resp ) {
  $( '#target' ).append( '<p>' + resp + '</p>' );
});
```

بإمكانك أيضًا تمرير أكثر من معامل إلى ‎`$.when()`‎، الأمر الّذي يسمح لك بدمج عمليّات متزامنة ولا متزامنة معًا ثمّ الحصول على نتائج تنفيذها كلّها كُمعاملات للاستدعاء الرّاجع:

```javascript
function maybeAsync( num ) {
  var dfd = $.Deferred();

  // أعد وعدًا مؤجّلًا عندما num === 1
  if ( num === 1 ) {
    setTimeout(function() {
      dfd.resolve( num );
    }, 100);
    return dfd.promise();
  }

  // أنهِ مباشرةً فيما سوى ذلك، مُعيدًا num
  return num;
}

$.when( maybeAsync( 0 ), maybeAsync( 1 ) )
  .then(function( resp1, resp2 ) {
    var target = $( '#target' );
    target.append( '<p>' + resp1 + '</p>' );
    target.append( '<p>' + resp2 + '</p>' );
  });
```

عندما يكون إحدى مُعاملات  ‎`$.when()`‎ كائن jqXHR، فإنّنا نحصل على مصفوفة من المُعاملات تُمرّر إلى استدعائنا الرّاجع:

```javascript
function maybeAsync( num ) {
  var dfd = $.Deferred();

  // أعد وعدًا مؤجّلًا عندما num === 1
  if ( num === 1 ) {
    setTimeout(function() {
      dfd.resolve( num );
    }, 100);
    return dfd.promise();
  }

  // أنهِ مباشرةً فيما سوى ذلك، مُعيدًا num
  return num;
}

$.when( maybeAsync( 0 ), $.get( '/data/people.json' ) )
  .then(function( resp1, resp2 ) {
    console.log( "Both operations are done", resp1, resp2 );
  });
```

##مصادر إضافية
* [توثيق AJAX](http://api.jquery.com/category/ajax/)
* [كائن jqXHR](http://api.jquery.com/jQuery.ajax/#jqXHR)
* [الكائنات المؤجّلة في jQuery](http://api.jquery.com/category/deferred-object/)
