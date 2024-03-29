تعلّم jQuery‏ (5): تحريك الصّفحة بـjQuery
===================================
تجعل jQuery إضافة التأثيرات الحركيّة على الصّفحة أمرًا سهلًا للغاية، ويمكن لهذه التأثيرات أن تعتمد الإعدادات المبدئيّة أو إعدادات يُعيّنها المُطوّر. بإمكانك أيضًا إنشاء حركاتٍ مُخصّصة من خصائص CSS عشوائيّة.

اطّلع على [وثائق التأثيرات](http://api.jquery.com/category/effects/) لتفاصيل أكثر عن تأثيرات jQuery.

_**ملاحظة مهمّة عن الحركات:** يكون إنجاز الحركات باستخدام CSS بدل JavaScript أكثر كفاءةً في المُتصفّحات الحديثة، وخصوصًا في الأجهزة المحمولة. تفاصيل إنجاز هذه الحركات خارجةٌ عن نطاق السّلسلة، ولكن إن كنت تستهدف المُتصفّحات والأجهزة المحمولة الّتي تدعم حركات CSS، فقد ترغب بتعيين الإعداد `jQuery.fx.off` إلى القيمة `true` على الأجهزة ذات المواصفات الضّعيفة؛ فهذا من شأنه إبطال الحركات والوصول بالعنصر المطلوب تحريكه إلى حالته النّهائية مباشرةً دون تطبيق الحركة._

##التأثيرات المُرفقة مع jQuery
تُرفَق الحركات المُستخدم بكثرة مع jQuery كوظائف يمكنك استدعاؤها على أي كائن jQuery:

* ‏‎`.show()`‎: أظهر العناصر المُحدّدة.
* ‏‎`.hide()`‎: أخفِ العناصر المُحدّدة.
* ‏‎`.fadeIn()`‎: حرّك ظلاليّة العناصر (opacity) المُحدّدة إلى 100%.
* ‏‎`.fadeOut()`‎: حرّك ظلاليّة العناصر المُحدّدة إلى 0%.
* ‏‎`.slideDown()`‎: أظهر العناصر المُحدّدة بحركة سحب شاقوليّة.
* ‏‎`.slideUp()`‎: أخفِ العناصر المُحدّدة بحركة سحب شاقوليّة.
* ‏‎`.slideToggle()`‎: أخفِ العناصر المُحدّدة أو أظهرها بحركة سحبٍ شاقوليّة، اعتمادًا على كون العناصر المُحدّدة مخفيّة أو ظاهرة.

يسهل تطبيق إحدى هذه التأثيرات على التّحديد بعد إنشائه (تأكد من ضغط الزرّ Run with JS في كلّ الأمثلة التّالية):

<a class="jsbin-embed" href="http://jsbin.com/huxefi/2/embed?js,console,output">ساحة التّجربة</a><script src="http://static.jsbin.com/js/embed.js"></script>

بإمكانك أيضًا تحديدُ مدّة للتأثيرات السّابقة، وهناك طريقتان لتحديدها، الأولى: تعيين الوقت بالميللي ثانيّة:

<a class="jsbin-embed" href="http://jsbin.com/huxefi/3/embed?js,console,output">ساحة التّجربة</a><script src="http://static.jsbin.com/js/embed.js"></script>
والثّانية استخدام إحدى السُرعات المُعرّفة مُسبقًا:

<a class="jsbin-embed" href="http://jsbin.com/huxefi/4/embed?js,console,output">ساحة التّجربة</a><script src="http://static.jsbin.com/js/embed.js"></script>

عُرِّفت هذه السُرعات في الكائن `jQuert.fx.speeds`؛ ممّا يعني أنّ بإمكانك تعديله لتغيير القيم المبدئيّة، أو إضافة سُرعات جديدة إليه:

```javascript
// أعد تعيين سرعةٍ مُعرّفة
jQuery.fx.speeds.fast = 50;

// عرّف سرعة جديدة
jQuery.fx.speeds.turtle = 3000;

// بما أنّنا غيّر قيمة السّرعة `fast`، فإنّ هذه الحركة ستستغرق 50 ميللي ثانية
$( '.hidden' ).hide( 'fast' );

// بإمكاننا استخدام السّرعات الّتي عرفناها بأنفسنا تمامًا كتلك المُعرّفة مسبقًا
$( '.other-hidden' ).show( 'turtle' );
```

كثيرًا ما يرغب المُطوّر بفعل شيءٍ ما بعد انتهاء الحركة مباشرةً، فإن حاول فعله قبل انتهاء الحركة، فقد يسبّب تشوّه الحركة وتقطّعها، أو قد يحذف سهوًا عناصر تتحرّك في لحظة حركتها. بإمكانك تمرير استدعاء راجع (callback) إلى وظائف الحركة إن رغبت بتنفيذ أمرٍ ما بعد انتهاء التأثير، وتُشير `this` داخل هذا الاستدعاء إلى عنصر DOM الخام الّذي طُبقّت عليه الحركة، ومثلها ومثل دوالّ تولّي الأحداث، يمكن إحاطة `this` بالوظيفة ‎`$()`‎ لاستخدامها ككائن jQuery:

<a class="jsbin-embed" href="http://jsbin.com/huxefi/5/embed?js,console,output">ساحة التّجربة</a><script src="http://static.jsbin.com/js/embed.js"></script>`

إن لم يحوِ التّحديد أيّة عناصر، فلن تُستدعى الدّالة. إن احتجت إلى استدعاء الدّالة بصرف النّظر عن وجود العناصر أو غيابها في التّحديد، بإمكانك إنشاء دالّة تتعامل مع الحالتين:

<a class="jsbin-embed" href="http://jsbin.com/huxefi/6/embed?js,console,output">ساحة التّجربة</a><script src="http://static.jsbin.com/js/embed.js"></script>

##تأثيرات مُخصّصة باستخدام ‎`.animate()`‎
إن لم تُلبِّ الحركات المُرفقة مع jQuery حاجتك، فبإمكانك استخدام الوظيفة ‎`.animate()`‎ لإنشاء حركات مخصّصة قائمة على خصائص CSS مُتعدّدة (إحدى الاستثناءات: الخاصّ' `color` الّتي لا يمكن تحريكها، ولكن تتوفّر [إضافة](https://github.com/jquery/jquery-color/) تسمح بذلك).

تقبل الوظيفة ‎`.animate()`‎ ثلاثة مُعاملات على الأكثر:

* كائن يُحدّد الخصائص الّتي يُراد تحريكها
* مدّة الحركة، مُقدّرة بالميللي ثانية
* دالّة تُستدعى عند انتهاء الحركة

يمكن أن تُعيّن قيمة الحركة بكتابة القيمة النّهائيّة المُراد التّحريك إليها، أو كتابة المقدار الّذي يجب تحريكه (الفرق بين موضعي الحركة):

<a class="jsbin-embed" href="http://jsbin.com/huxefi/8/embed?js,console,output">ساحة التّجربة</a><script src="http://static.jsbin.com/js/embed.js"></script>

_ملاحظة: إن أردت تحريك خاصّة CSS يحوي اسمها على الإشارة "-"، فعليك تحويل الاسم إلى صيغة camelCase أوّلًا إن لم تشأ إحاطة اسم الخاصّة بعلامات اقتباس، فمثلًا الخاصّة `font-size` تُصبح `fontSize`._

##إدارة الحركات
تُوفّر jQuery وظيفتين مُهمّتين لإدارة الحركات:

* ‏‎`.stop()`‎: تُوقف الحركات الجارية على العناصر المُحدّدة.
* ‏‎`.delay()`‎: تُؤخِّر بدء الحركة القادمة بالمقدار الذي يُمرّر إليها (بالميللي ثانية).

تُوفّر jQuery أيضًا وظائف لإدارة تعاقب الحركات وتنظيمها في "طوابير"، وإنشاء طوابير مُخصّة، وإضافة دوالّ مُخصّصة إلى هذه الطّوابير. مناقشة هذه الوظائف موضوع أكبر من هذه السّلسلة، ولكن قد ترغب بالاطّلاع عليها في [وثائق jQuery](http://api.jquery.com/category/effects/).
