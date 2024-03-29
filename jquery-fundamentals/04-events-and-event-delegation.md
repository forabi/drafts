تعلّم jQuery‏ (4): الأحداث وتفويضها
=============================
تُسهّل jQuery الاستجابة لتفاعل المُستخدم مع صفحات الويب. معنى هذا أنّ بإمكانك تنفيذ أمرٍ ما عندما ينقر المُستخدم على جزءٍ مُعيّن من الصّفحة، أو عندما يُحرّك مؤشّر الفأرة فوق عنصر في نموذج مثلاً. في المثال التّالي، لدينا أمر يُنفَّذ عندما ينقر المُستخدم فوق أيّ عنصر قائمة في الصّفحة (تأكد من ضغط الزرّ Run with JS في كلّ الأمثلة التّالية):

<a class="jsbin-embed" href="http://jsbin.com/fopitohami/30/embed?js,console,output">ساحة التّجربة</a><script src="http://static.jsbin.com/js/embed.js"></script>

يُحدّد النّصّ السّابق كلّ عناصر القائمة ويُسند إليها دالّة تتولّى حدث النّقر على كلّ عنصر، باستخدام وظيفة ‎`.click()`‎ في jQuery.

توفّر jQuery وظائف مُختصرةً عديدةً لربط الأحداث، وكلّ من هذه الوظائف يوافق حدث DOM أصليًّا:

اسم الحدث الأصليّ       | الوظيفة المُختصرة
------------------|--------------------
‏click‎             |            .click()
‏keydown           |          .keydown()
‏keypress          |         .keypress()
‏keyup             |            .keyup()
‏mouseover         |        .mouseover()
‏mouseout          |         .mouseout()
‏mouseenter        |       .mouseenter()
‏mouseleave        |       .mouseleave()
‏scroll            |           .scroll()
‏focus             |            .focus()
‏blur              |             .blur()
‏resize            |           .resize()

تستخدم هذه الوظائف المُختصرة الوظيفة ‎`.on()`‎ ما وراء الكواليس، وهي وظيفة يمكنك استخدامها بنفسك لمرونةٍ أكبر. وعند استخدامك لها، فإنّك تُمرّر اسم الحدث الأصليّ كمُعامل أوّل للوظيفة، ثمّ دالّة تتولى الحدث كمعامل ثانٍ:

<a class="jsbin-embed" href="http://jsbin.com/fopitohami/31/embed?js,console,output">ساحة التّجربة</a><script src="http://static.jsbin.com/js/embed.js"></script>

ما إن "تربط" مُتولّي الحدث بعنصرٍ من العناصر، فبإمكانك إثارة هذا الحدث بـjQuery أيضًا:

```javascript
$( 'li' ).trigger( 'click' );
```

وإن كان للحدث الّذي تُريد إثارته وظيفةٌ مختصرة (كما ورد في الجدول السّابق)، فبإمكانك إثارة الحدث باستدعاء الوظيفة المختصرة ذاتها:

```javascript
$( 'li' ).click();
```

_ملاحظة: عندما تُثير حدثًا باستخدام ‎`.trigger()`‎، فإنّك تستدعي مُتولّيات الأحداث الّتي أُنشئت في JavaScript فقط ولا تستدعي السّلوك الافتراضيّ للحدث. فمثلًا إن أثرت حدث النّقر على رابط (عنصر `<a>`) فلن ينتقل المتصفّح إلى الرّابط المُسند إليه في صفة `href` (مع أنّ بإمكانك كتابة أوامر تُنفّذ هذه الغاية)._

بعد أن تربط حدثًا بُعنصر، بإمكانك فكّ هذا الارتباط باستخدام الوظيفة ‎`.off()`‎ الّتي تُزيل أية مُتولّيات ارتبطت بهذا الحدث:

```javascript
$( 'li' ).off( 'click' );
```

##حصر الأحداث ضمن فضاء أسماء
من المزايا الّتي تُتيحها ‎`.on()`‎ إمكانيّة حصر الأحداث ضمن "فضاء أسماء". قد تتساءل عن الحاجة لذلك. افترض مثلًا أنّك تريد ربط بعض الأحداث بعنصر ما، ثمّ إزالة بعض المُتولّيات، يمكنك أن تفعل ما فعلناه في الفقرة الماضية:

**تحذير: أسلوب برمجيّ غير مُفضّل**

```javascript
$( 'li' ).on( 'click', function() {
  console.log( 'a list item was clicked' );
});

$( 'li' ).on( 'click', function() {
  registerClick();
  doSomethingElse();
});

$( 'li' ).off( 'click' );
```

إلّا أنّ هذا الأسلوب سيُزيل _كلّ_ مُتولّيات النقر على كلّ عناصر القوائم، وليس هذا ما نريد. إذا ربطت متولّيًا للأحداث محصورًا ضمن فضاء أسماء، فبإمكانك استهدافه بدقّة:

```javascript
$( 'li' ).on( 'click.logging', function() {
  console.log( 'a list item was clicked' );
});

$( 'li' ).on( 'click.analytics', function() {
  registerClick();
  doSomethingElse();
});

$( 'li' ).off( 'click.logging' );
```

هذا الأسلوب لا يؤثّر على الأحداث المرتبطة بالنّقر والمُتعلّقة بأغراض إحصاءات الاستخدام في الصّفحة، بينما يزيل أحداث النّقر المُتعلّقة بالسّجلّات.

بإمكاننا استخدام ميزة حصر الأحداث لإثارة أحداثٍ مُحدّدة:

```javascript
$( 'li' ).trigger( 'click.logging' );
```

##ربط أحداث مُتعدّدة في وقت واحد
ميزة أخرى تُوفّرها  ‎`.on()`‎، وهي إمكانيّة ربط أحداث مُتعدّدة في وقتٍ واحد. افترض مثلًا أنّك تريد تنفيذ أمرٍ مُعيّن عندما يُمرّر المستخدم الصّفحة أو يغيّر قياس النّافذة. فهذه الوظيفة تُتيح لك تمرير الحدثين معًا مفصولين بمسافة في سلسلة نصيّة، يتبعهما الدّالّة الّتي تريد أن تتولّى الحدثين:

<a class="jsbin-embed" href="http://jsbin.com/fopitohami/32/embed?js,console,output">ساحة التّجربة</a><script src="http://static.jsbin.com/js/embed.js"></script>

##تمرير دوالّ مُسمّاة كُمتولّيات الأحداث
في كلّ أمثلتنا السّابقة، كنّا نُمرّر دوالّ مجهولة كمُتولّيات للأحداث، ولكن يمكننا إنشاء دالّة قبل إمرارها وحفظها في مُتغيّر ثمّ تمرير هذا المُتغيّر كمُتولّي الحدث. هذا يُفيد في حال أردت استخدام الدّالة نفسها لتتولّى أحداثًا مُختلفة أو أحداثًا من عناصر مُختلفة:

```javascript
var handleClick = function() {
  console.log( 'something was clicked' );
};

$( 'li' ).on( 'click', handleClick );
$( 'h1' ).on( 'click', handleClick );
```

##كائن الحدث
في كلّ مرّة يُثار فيها حدثٌ ما، تستقبل الدّالّة المُتولّية للحدث مُعاملًا واحدًا، وهو كائن الحدث الّذي يتبع معايير مُتّفقًا عليها بين كلّ المُتصفّحات. ولهذا الكائن [خصائص مُفيدة كثيرة](http://api.jquery.com/category/events/event-object/)، منها:

<a class="jsbin-embed" href="http://jsbin.com/fopitohami/33/embed?js,console,output">ساحة التّجربة</a><script src="http://static.jsbin.com/js/embed.js"></script>

##داخل مُتولّي الأحداث
عندما تُحدِّد الدّالة المُتولّية لحدث ما، فإنّه يُتاح لهذه الدّالّة وصول إلى عنصر DOM الخام الّذي أطلق الحدث كسياق الدّالّة `this`، فإن أردت استخدام jQuery للتّعامل مع الحدث، فأحطه بـ‎`$()`‎:

<a class="jsbin-embed" href="http://jsbin.com/pebico/2/embed?js,console,output">ساحة التّجربة</a><script src="http://static.jsbin.com/js/embed.js"></script>

##منع السّلوك المبدئيّ
قد ترغب أحيانًا في منع السّلوك المبدئيّ لحدثٍ ما، كأن ترغب في تولّي النّقر فوق رابط باستخدام AJAX، بدلًا من بدء إعادة تحميل كاملةٍ للصّفحة (وهو السّلوك المبدئيّ). يصل العديد من المُطوّرين إلى هذه الغاية بإعادة `false` من مُتولّي الحدث، ولكنّ لهذا تأثيرًا جانبيًّا آخر: فهو يمنع _تفشّي_ الحدث (propagation) أيضًا (سنشرحه بعد قليل)، ممّا قد يعطي نتائج غير مرغوبة. الطّريقة السّليمة لمنع السّلوك المبدئيّ لحدث تكون باستدعاء ‎`.preventDefault()`‎ على كائن الحدث:

<a class="jsbin-embed" href="http://jsbin.com/fopitohami/34/embed?js,console,output">ساحة التّجربة</a><script src="http://static.jsbin.com/js/embed.js"></script>

##صعود الأحداث (Event bubbling)
تمعّن النّص البرمجيّ التّالي:

<a class="jsbin-embed" href="http://jsbin.com/fopitohami/35/embed?js,console,output">ساحة التّجربة</a><script src="http://static.jsbin.com/js/embed.js"></script>

يربط هذا النّص مُتولّيًا للنقر على كلّ العناصر في الصّفحة (وهو أمر يجب _ألّا_ تفعله _نهائيًّا_ في المواقع الحقيقيّة)، بالإضافة إلى عنصري النّافذة والمُستند. ولكن ما الّذي يحدث عندما تنقر على عنصر `<a>` مُدرج داخل عناصر أخرى؟ الحقيقة أنّ الحدث سيُثار على العنصر `<a>` وعلى كلّ العناصر الّتي تُحيط به صعودًا حتّى الوصول إلى العنصرين `document` و`window`.

يُسمّى هذا السّلوك "صعود الأحداث"، فالحدث يُثار على العنصر الّذي نقر عليه المُستخدم، ثمّ ينتقل صاعدًا إلى كلّ العناصر الّتي تحويه وصولًا إلى أعلى DOM، إلّا إن استدعيت الوظيفة ‎`.stopPropagation()`‎ على كائن الحدث.

بإمكانك فهم ذلك بسهولة أكبر في هذا المثال:

```html
<a href="#foo"><span>I am a Link!</span></a>
<a href="#bar"><b><i>I am another Link!</i></b></a>
```

```javascript
$( 'a' ).on( 'click', function( event ) {
  event.preventDefault();
  console.log( $( this ).attr( 'href' ) );
});
```

عندما تنقر على "I am a Link!‎"، فإنك لا تنقر فعليًّا على العنصر `<a>`، بل على العنصر `<span>` داخله، ولكن الحدث "يصعد" نحو العنصر `<a>` ويُثير حدث النّقر المُرتبط به.

##تفويض الأحداث (Event delegation)
يسمح لنا مفهوم "صعود الأحداث" بإنجاز فكرة "تفويض الأحداث"، أي ربط الأحداث بعناصر في مستوى أعلى، ثمّ اكتشاف أيّ عنصر فرعيّ ضمنها أثار الحدث. بإمكاننا مثلًا ربط حدث بقائمة غير مُرتّبة، ثمّ تحديد أيّ العناصر أثار الحدث:

<a class="jsbin-embed" href="http://jsbin.com/fopitohami/36/embed?js,console,output">ساحة التّجربة</a><script src="http://static.jsbin.com/js/embed.js"></script>

بالطّبع ستتعقّد الأمور إذا احتوت عناصر القائمة على عناصر فرعيّة ضمنها، ولهذا تُقدّم jQuery وظيفةً مُساعدة تسمح لنا بتحديد أي العناصر نهتمّ بها، مع الاحتفاظ بالحدث مُرتبطًا بالعنصر ذي المُستوى الأعلى:

<a class="jsbin-embed" href="http://jsbin.com/xucuha/1/embed?js,console,output">ساحة التّجربة</a><script src="http://static.jsbin.com/js/embed.js"></script>


لتفويض الأحداث فائدتان اثنتان: أولاهما أنّه يسمح بربط عددٍ أقلّ من مُتولّيات الأحداث مقارنة بالعدد الّذي نحتاجه لو قرّرنا ربط الأحداث بالعناصر المُنفردة، وهذا يُحسّن الأداء في الصّفحة بصورة كبيرة. وثاني الفائدتين أنّه يسمح لنا بربط الأحداث بالآباء (كالقائمة غير المرتّبة في مثالنا)، مع اطمئنانا إلى أنّ الأحدث ستُثار _حتّى وإن تغيّرت مُحتويات العنصر الأب_.

هذا النّصّ مثلًا، يُضيف عنصر قائمةٍ جديدًا بعد تفويض الحدث إلى العنصر الأب، والنّقر فوق هذا العنصر سيُثير الحدث كما ينبغي، دون الحاجة لربط أيّة أحداث جديدة:

<a class="jsbin-embed" href="http://jsbin.com/fopitohami/40/embed?js,console,output">ساحة التّجربة</a><script src="http://static.jsbin.com/js/embed.js"></script>
##خاتمة
تعلّمنا في هذا الجزء الوسائل المُختلفة للإنصات إلى تفاعل المُستخدم مع صفحتنا، بما في ذلك كيفيّة الاستفادة من التفويض لتحسين كفاءة ربط الأحداث بالعناصر. سنتعرّف الجزء القادم كيف نُحرّك العناصر باستخدام وظائف التأثيرات الحركيّة في jQuery.
