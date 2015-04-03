لا تكرّر نفسك: أعد استخدام دوالّ Sass التي كتبتها
=======================================
لعلّ من أقوى ميّزات Sass الدّوالّ (mixins) ﻷنّها تسمح بتحديد نمط مُتكرِّر وعزله في صورة قطعة برمجيّة يمكن إعادة استخدامها مرارًا. مثال ذلك: عزل كلّ الخصائص المسؤولة عن تنسيق زرّ في صفحة الويب ثمّ —بدل الحاجة لتذكّر كل هذه الخصائص— جمعها في قطعة منفصلة يمكن استدعاؤها في مُحدِّد آخر (selector). هذا يحفظ أنماط الزرّ في موضع واحد ممّا يجعل تعديلها وتحديثها أسهل.

المشكلة في هذا أنّ كثيرًا من دوّال Sass هذه تُكتب بأسلوب يجعل الخصائص مُكرّرة، وهذا قد يؤدي إلى حدوث أخطاء في عدة مواضع عند تحويلها إلى CSS بالإضافة إلى زيادة حجم الملفّ النّهائي دون طائل. صحيح أنّ استخدام Sass هو خطوةٌ على الطّريق الصّحيح لإنتاج CSS أخفّ حجمًا وأكثر اختصارًا ممّا قد يكتبه مبرمج متوسّط المستوى، لكنّك إن كنت مهووسًا بتحسين الأداء مثلي، فلن ترضى بوجود أدنى مقدار من التّكرار الّذي لا مُبرِّر له. سأدلكّ في هذه المقالة على كيفيّة تحسين مستوى CSS النّاتج عن دوالّ Sass الّتي تكتبها.

##مقدّمة سريعة عن Sass
يمكن وصف Sass بأنّها مرحلة وسيطة بين ورقة الأنماط الّتي يكتبها مُصمّم الموقع وناتج CSS النّهائي الّذي يطلبه المتصفّح، وتفيد Sass في هذه المرحلة بإضافة العديد من الميّزات الّتي تُسهلّ كتابة CSS وصيانتها. واحدة من فوائد Sass أنّها تُساعِد الُمصمّمين في تجنّب التّكرارات في CSS، ممّا يجعل صيانتها أكثر سهولة. اصطلح على مبدأ "لا تُكرِّر نفسك" (Don't Repeat Yourself أو اختصارًا DRY) في كتاب ["المُبرمج البراغماتيّ"](http://pragprog.com/the-pragmatic-programmer)، والذي نصّ على التّالي:

> كلّ مفهوم يجب أن يُمثّله مُكوِّنٌ مفردٌ واضح ضمن النّظام.

لا تُساعِدنا CSS كثيرًا في تطبيق هذا المبدأ، لأنّها تُضطرّنا إلى تكرار كتابة الكثير من الخصّائص (كما في مثال الأزرار الذي ضربناه). فلو احتجت إلى إنشاء أنماط لثلاثة أنواع من الأزرار، فستضطر إلى كتابة هذه الأنماط مرّة لكلّ نوع من الأزرار، أو تجزئة الأنماط على أكثر من مُحدّد. وهما أمران أحلاهما مُرٌّ. فالأوّل يعني نسخ الخصائص ولصقها (وزيادة حجم الملفّ بالنتيجة) وتكرار مواضع الأخطاء، وغياب مصدر واحدٍ للحقيقة، وبنية هشّة في حصيلة الأمر؛ وأمّا الثاني فيعني غياب تمثيل مُفرد ومُوحَّد لكلّ نوع من الأزرار في النّظام، لأنّه يُجبرك على تجزئة الأنماط على عدّة مُحدِّدات، وهو ما يجعل البنية هشّة أيضًا، لأنّ تكوينها غير واضح.

لو أردنا تحرّي المثاليّة، فعلينا إيجاد طريقة لتعريف الأنماط الأساسيّة في موضع واحد دون تكرار، ثمّ تعريف مُحدِّد مُفرد يمكن استخدامه لتطبيق كلّ الأنماط.

##لماذا قد أرغب باتّباع مبدأ "لا تُكرِّر نفسك"؟
قد تسأل نفسك: لم كلّ هذا التّعقيد؟ الجواب باختصار: لأنّ اتّباع مبدأ DRY يُحسّن من أداء الموقع. فعندما تؤسّس هيكل الموقع، سيكون الأداء عاملًا مُهمًّا، بدءًا من صيغ الصّور الّتي نختارها ومرورًا بطريقة كتابة مُحدِّدات CSS. وهذا العاملّ يُصبح أكثر أهمّية عندما نتحدّث عن الأجهزة المحمولة، ففي هذه الأجهزة قد يفرض مُجرّد طلب HTTP بسيط [تحدّيات كبيرةً في الأداء](http://alistapart.com/blog/post/breaking-the-1000ms-time-to-glass-mobile-barrier)، وخطأ الاعتقاد الشّائع بعدم أهمّيّة حجم ملفّ CSS في أداء الموقع يظهر أكثر وضوحًا في الأجهزة المحمولة الّتي [لا تسمح بأكثر من 100 ميغابايت من الحجم الكلّي للتّخزين المؤقّت لكلّ المواقع](http://www.guypo.com/uncategorized/mobile-browser-cache-sizes-round-2/)، وعندها يجب استغلال أكبر مقدار ممكن من مساحة التّخزين المؤقّت بأفضل صورة.

الهدف من ذلك إذن هو إنشاء مُحدِّدات يمكن صيانتها في Sass وفي HTML، ويمكن تمثيلها في CSS بأقصر أسلوبٍ ممكن لتقليل حجم التّخزين المؤقّت.

##الدّوال (mixins) والتّوسعة (extends): حلّان غير كاملين
تٌقدّم دوالّ Sass حلًّا لإحدى هاتين المُشكلتين، فهي تسمح بإنشاء موضع واحد يمكن فيه تعريف الأنماط الأساسيّة والرّجوع إليها. يمكن لهذه الدّوال أن تقبل مُعاملات (arguments) كذلك، مما يسمح باختلافات طفيفة بين استدعاء وآخر للدّالة وإنشاء أشكال مختلفة لنفس النّمط. لكن الأمر لا يخلو من مُشكلات: فقد تُسبّب هذه الدّوال تكرارًا غير مُبرّر للخصائص لأنّها ستكتب الخصائص الّتي تحويها في كلّ موضع استدعيت فيه. فالدّوالّ إذن تحل مشكلة التُمثيل المُفردّ في مبدأ DRY، لكنّها تترك خصائص متكرّرة في النّاتج النّهائيّ من CSS.

ولهذا تُقدّم Sass مفهومًا آخر يُساعِد في تطبق مبدأ DRY: التّوسعة (extends)، والّتي يمكن استخدامها بكتابة الكلمة `‎@extend`، والّتي تسمح لمُصمّم CSS أن يقول: "أريد للُمحدّد A أن يظهر كما يظهر المُحدّد B"، وعندها تقوم Sass بإنشاء نمط جديد يجمع بين A وB (مفصول بينهما بفاصلة)، ثمّ كتابة الخواصّ غير المشتركة بالأسلوب العاديّ. لا يمكن تمرير مُعاملات للتّوسعة (خلافًا للدّوالّ)، فهي حلّ من نوع "الكلّ أو لا شيء".

```sass
.couch {
	padding: 2em;
	height: 37in;
	width: 88in;
	z-index: 40;  
}
  
.couch-leather {
	@extend .couch;
	background: saddlebrown;
}

.couch-fabric {
	@extend .couch;
	background: linen;
}
```

```css
.couch, 
.couch-leather, 
.couch-fabric {
	padding: 2em;
	height: 37in;
	width: 88in;
	z-index: 40;
}

.couch-leather {
	background: saddlebrown;
}

.couch-fabric {
	background: linen;
}
```

تحلّ التّوسعة مشكلة تكرار الخصائص والمُحدِّد المُفرّد في CSS النّاتج، لكنّها لا تُخلّص مُصمّمي المواقع من الحاجة لصيانة مجموعتين منفصلتين من الأنماط في ملفّات Sass، ولا من الحاجة لتذكّر أيّ الخصائص ينطبق على أيّ نوع—فالأمر لا يختلف كثيرًا فيما لو كتبنا محدّدين منفصلين في الأساس.

كما نرى، فكلا الحّلين (الدّوالّ والتّوسعة) غير كاملين، ولكن عند جمعهما معًا ثمّ اعتماد بنيّة ذكيّة في تصميم مشروعنا، مع الاستفادة من بعض ميّزات Sass سنحصل على دالّة نهائيّة تخضع لمبدأ DRY تمامًا، وتجمع نصفي الحلّ في صيغة موحّدة سواءٌ في ملفّات Sass الأصليّة أو في ناتج CSS.

##المكوّنات الأساسيّة لمبدأ "لا تُكرِّر نفسك"
تضع المُكوّنات الأربعة التّالية حجر الأساس لبناء دوالّ موافقة لمبدأ DRY في Sass: المُحدّدات بالنّيابة (placeholder selectors)، والجداول (maps)، والكلمة `‎@at-root`، والدّالة `unique-id()‎`.

###المُحدّدات بالنّيابة
وهي نوعٌ خاصّ من المُحدّدات يُستخدم مع الكلمة `‎@extend` في Sass. تُكتب هذه المُحدّدات كما تُكتب الأصناف التّقليديّة (classes)، ولكنّها تبدأ بالرّمز `%` بدل النّقطة `.`، وتتصرّف عند توسعتها بصورة عاديّة، ولكنّها لا تُضاف إلى النّاتج النّهائيّ إلّا عند توسعتها. وكما في التّوسعة العاديّة، تُضاف خواصّ المحدّدات في الموضع الّذي عُرّف فيه المُحدّد.

```sass
%foo {
	color: red;
}

.bar {
	@extend %foo;
	background: blue;
}

.baz {
	@extend %foo;
	background: yellow;
}
```

```css
.bar, 
.baz {
	color: red;
}

.bar {
	background: blue;
}

.baz {
	background: yellow;
}
```

###الجداول (maps)
وهي نوعٌ من أنواع البيانات في Sass ‎3.3 (مثلها مثل الأرقام والسلاسل النّصيّة والقوائم) تتصرّف بطريقة مُشابهة للكائنات في JavaScript. تتألّف الجداول من أزواج مفتاح/قيمة (key/value)، حيث يمكن للمفتاح والقيمة أن يكونا أي نوع من أنواع البيانات في Sass (بما في ذلك الجداول نفسها). المفاتيح فريدة دومًا ويمكن الوصول إليها باسمها، ممّا يجعلها مثاليّة لتخزين البيانات المُميّزة واسترجاعها.

```sass
$properties: (
	background: red,
	color: blue,
	font-size: 1em,
	font-family: (Helvetica, arial, sans-serif)
);

.foo {
	color: map-get($properties, color);
}
```

###الكلمة `‎@at-root`
قُدّمت هذه الكلمة في Sass ‎3.3، وهي تقوم بنقل الخواصّ المُعرّفة ضمنها إلى جذر ورقة الأنماط (أعلى مستوىً فيها)، بغض النّظر عن الموضع الّذي استخدمت فيه.

###الدّالّة `unique-id()‎`
قدّمت في Sass ‎3.3 كذلك، وهي تُعيد مُعرّف CSS [يُضمن كونُه فريدًا](http://hugogiraudel.com/2013/10/17/sass-random/) في كلّ عمليّة تحويل من Sass إلى CSS.

##كتابة دالّة بسيطة
يتطلّب تحويل نمط مُتكرِّر إلى دالّة النّظرَ في الأنماط الأساسيّة المُكوّنة لها ثم تحديد ما المشترك بينها وما الذي يختلف بحسب مُدخَلات المستخدم. سنستخدم زرًّا بسيطًا كمثال في حالتنا:

```sass
.button {
	background-color: #b4d455;
	border: 1px solid mix(black, #b4d455, 25%);
	border-radius: 5px;
	padding: .25em .5em;
	
	&:hover {
		cursor: pointer;
		background-color: mix(black, #b4d455, 15%);
		border-color: mix(black, #b4d455, 40%);
	}
}
```

لتحويل هذا إلى دالّة، اختر الخصائص الّتي يتحكّم بها المستخدم (الديناميكيّة)، والخصائص الثّابتة. يُتحكّم بالخصائص الدّيناميكيّة عبر مُعامِلات تُمرّر إلى الدّالة، أمّا الخصائص الثّابتة فتكتب بالأسلةب العاديّ. في حالة الزّرّ الذي نُصمّمه، لا نريد سوى أن يتغيّر اللّون، ولهذا نستدعي الدّالّة بمُعامل اللّون، لُينتَج CSS كما نتوقّع:

```sass
@mixin button($color) {
	background-color: $color;
	border: 1px solid mix(black, $color, 25%);
	border-radius: 5px;
	padding: .25em .5em;
	
	&:hover {
		cursor: pointer;
		background-color: mix(black, $color, 15%);
		border-color: mix(black, $color, 40%);
	}
}

.button {
	@include button(#b4d455);
}
```
لا بأس بهذا الحلّ، ولكنّه سيؤدّي إلى تكرار خصائص كثيرة، افترض مثلًا أنّنا نريد إنشاء زرّ آخر بلون مختلف، عندها ستبدو شيفرة Sass كما يلي (دون تعريف الدّالّة):

```sass
.button-badass {
	@include button(#b4d455);
}

.button-coffee {
	@include button(#c0ffee);
}
```

وستبدو شيفرة CSS كما يلي:

```css
.button-badass {
	background-color: #b4d455;
	border: 1px solid #879f3f;
	border-radius: 5px;
	padding: .25em .5em;
}
.button-badass:hover {
	cursor: pointer;
	background-color: #99b448;
	border-color: #6c7f33;
}

.button-coffee {
	background-color: #c0ffee;
	border: 1px solid #90bfb2;
	border-radius: 5px;
	padding: .25em .5em;
}
.button-coffee:hover {
	cursor: pointer;
	background-color: #a3d8ca;
	border-color: #73998e;
}
```

هناك الكثير من الخصائص المُكرَّرة هنا، لا نريد هذا! لذا سنلجأ إلى استخدام المُحدِّدات بالنّيابة.

##إزالة التّكرار من دالّة
إزالة التّكرار من الدّالة يقتضي تجزئتها إلى أجزاء ثابتة وأخرى ديناميكيّة. الأجزاء الدّيناميكيّة هي ما سيستدعيه المستخدم، وأمّا الثّابتة فتحوي الأجزاء الّتي ستكون مُكرّرة فيما لو لم نجمعها في دالّة.

```sass
@mixin button($color) {
		@include button-static;

	background-color: $color;
	border-color: mix(black, $color, 25%);
  
	&:hover {
		background-color: mix(black, $color, 15%);
		border-color: mix(black, $color, 40%);
	}
}

@mixin button-static {
	border: 1px solid;
	border-radius: 5px;
	padding: .25em .5em;
	
	&:hover {
		cursor: pointer;
	}
}
```


الآن وقد فصلنا دالّتنا على جزأين، نريد أن نُوسِّع العناصر في `button-static` لتجنّب التّكرار. يمكن إنجاز ذلك باستخدام مُحدّد بالنّيابة بدل الدّالة، ولكن هذا يعني نقل المُحدّدات في ورقة الأنماط، لذا سنقوم بإنشاء مُحدّد بالنّيابة في الموضع ذاته ديناميكيًّا، بحيث يُنشأ حالما يُحتاج إليه ويبقى بترتيبه الأصليّ كما نتوقّع. لفعل ذلك، سنقوم أوّلًا بإنشاء مُتغيّر في النّطاق العامّ لحفظ أسماء المُحدّدات الدّيناميكيّة:

```sass
$Placeholder-Selectors: ();
```

ثمّ نحترّى وجود مفتاح يُطابق محدّدنا في `button-static`، وسندعو هذا المفتاح `"button"` في الوقت الحالي. باستخدام الدّالّة `map-get`، سنحصل على قيمة المفتاح المطلوب أو القيمة `null` إن لم يُوجد، وفي الحالة الأخية سنُعيّن قيمته إلى مُتغيّر فريد (unique ID) باستخدام `map-merge`. سنستخدم الوسم `‎!global` كوننا نريد كتابة قيمة متغيّر عُرِّف في النّطاق العامّ:

```sass
$Placeholder-Selectors: ();
// ...
@mixin button-static {
	$button-selector: map-get($Placeholder-Selectors, 'button');
	
	@if $button-selector == null {
		$button-selector: unique-id();
		$Placeholder-Selectors: map-merge($Placeholder-Selectors, ('button': $button-selector)) !global;
	}
	
	border: 1px solid;
	border-radius: 5px;
	padding: .25em .5em;
	
	&:hover {
		cursor: pointer;
	}
}
```
بعد معرفة وجود مُعرّف لمُحدّدنا، نُريد إنشاء هذا المُحدّد، ونفعل هذا باستخدام الكلمة `‎@at-root` وصياغة تعويض القيم في النّصوص (`‎#{}‎`) لإنشاء مُحدّد بالنّيابة في جذر ورقة الأنماط اسمه هو المُعرِّف الفريد الّذي حصلنا عليه. محتويات هذا المُحدِّد ستكون استدعاء للدّالة الثّابتة (لاحظ الاستدعاءات المتداخلة! يا للرّوعة!). ثمّ نُوسّع هذا المُحدّد بالنّيابة ذاته، ممّا يُفعّله ويؤديّ لكتابة خواصّه إلى CSS.

باستخدام مُحدّد بالنّيابة في هذه الحالة بدل توسعة مُحدّد تقليديّ كصنف (class)، فإنّ محتويات المُحدّد ستُضاف إلى CSS النّهائي فقط إن وُسِّع المُحدِّد، ممّا يسمح بتقليص حجم الملفّ. وباستخدام التّوسعة بدل كتابة الخصائص مباشرةً، نكون قد تجنّبنا تكرار الخصائص في الوقت ذاته. في النّهاية سيمنع هذا هشاشة النّاتج النّهائي من CSS، لأنّه في كلّ مرّة تُستدعى هذه الدّالة، فستكون الخصائص المُشتركة ضمنها مُشاركة بالفعل ضمن ناتج CSS، وليس فقط مرتبطة مع بعضها في مرحلة المُعالجة المسبقة لـCSS فحسب.

```sass
$Placeholder-Selectors: ();
// ...
@mixin button-static {
	$button-selector: map-get($Placeholder-Selectors, 'button');
	@if $button-selector == null {
		$button-selector: unique-id();
		$Placeholder-Selectors: map-merge($Placeholder-Selectors, ('button': $button-selector)) !global;
	  
		@at-root %#{$button-selector} {
			@include button-static;
		}
	}
	@extend %#{$button-selector};   
	
	
	border: 1px solid;
	border-radius: 5px;
	padding: .25em .5em;
	
	&:hover {
		cursor: pointer;
	}
}
```

لكن مهلًا، فلم ننتهِ بعدُ. ستبقى هناك محتويات مُكرّرة في النّاتج النّهائي، وهذا شيء لا نريده (حيث سنخصل على مُحدّد يوسّع نفسه، وهذا لا نريده أيضًا). لتجنّب هذا، سُنضيف مُعاملًا إلى `button-static` يُحدّد إن كان يجب المُضيّ بعمليّة التّوسعة أم لا. سُنضيفه هذا إلى دالّتنا الدّيناميكيّة أيضًا، ونمرّره إلى دالّتنا الثّابتة، وفي النّهاية ستكون لدينا الدّوالّ التّالية:

```sass
$Placeholder-Selectors: ();

@mixin button($color, $extend: true) {
	@include button-static($extend);
	
	background-color: $color;
	border-color: mix(black, $color, 25%);
	
	&:hover {
		background-color: mix(black, $color, 15%);
		border-color: mix(black, $color, 40%);
	}
}

@mixin button-static($extend: true) {
	$button-selector: map-get($Placeholder-Selectors, 'button');
	
	@if $extend == true {
		@if $button-selector == null {
			$button-selector: unique-id();
			$Placeholder-Selectors: map-merge($Placeholder-Selectors, ('button': $button-selector)) !global;
			
			@at-root %#{$button-selector} {
				@include button-static(false);
			}
		}
		@extend %#{$button-selector};
		}
		@else {
		border: 1px solid;
		border-radius: 5px;
		padding: .25em .5em;
		
		&:hover {
			cursor: pointer;
		}
	}
}
```

بعد كلّ هذا العناء، أنشأنا لأنفسنا طريقة لتسهيل صيانة أنماط Sass وتوفير مُحدِّد مُفرد في HTML، وإبقاء حجم CSS في أدنى الحدود. فهمها بلغ عدد استدعاءات دالّة `button` في شيفرتنا، لن تُكرَّر الخصائص الثّابتة فيها.

عند استدعاء دالّتنا لأوّل مرّة، سُتنشأ الأنماط الّتي فيها ضمن CSS في الموضع الّذي استدعيت فيه، ممّا يُحافظ على تراكب الأنماط بالتّرتيب المُتوقّع، ويقلّل هشاشة البنية. وبما أنّنا نسمح بعدّة استدعاءات للدّالّة نفسها، فبإمكاننا إنشاء "نكهات" مُختلفة بسهولة وصيانتها في كلا Sass وHTML.

في هذه المرحلة سيكون لدينا مصدر Sass وناتج CSS كما يلي:


```sass
.button-badass {
	@include button(#b4d455);
}

.button-coffee {
	@include button(#c0ffee);
}

.button-decaff {
	@include button(#decaff);
}
```

```css
.button-badass {
	background-color: #b4d455;
	border-color: #879f3f;
}
.button-badass, 
.button-coffee, 
.button-decaff {
	border: 1px solid;
	border-radius: 5px;
	padding: .25em .5em;
}
.button-badass:hover, 
.button-coffee:hover, 
.button-decaff:hover {
	cursor: pointer;
}
.button-badass:hover {
	background-color: #99b448;
	border-color: #6c7f33;
}

.button-coffee {
	background-color: #c0ffee;
	border-color: #90bfb2;
}
.button-coffee:hover {
	background-color: #a3d8ca;
	border-color: #73998e;
}

.button-decaff {
	background-color: #decaff;
	border-color: #a697bf;
}
.button-decaff:hover {
	background-color: #bcabd8;
	border-color: #857999;
}
```

لاحظ كيف يُفصل بين الخصائص الثّابتة بفواصل في موضع تعريفها، ممّا يجعل تتبّع الأخطاء أسهل، ويحافظ على ترتيب النّصّ المصدريّ، ويقلّل حجم ملف CSS، وبحيث لا تُنشأ مُحدّدات جديدة إلّا للخصائص الّتي تتغيّر. أليس هذا رائعًا؟

##المتابعة
الحقيقة أنّ كتابة النّمط نفسه لكلّ دالّة لا يتوافق مع مبدأ DRY على الإطلاق؛ بل على العكس تمامًا فهو WET (اختصارًا لـWrite Everything Twice، هل لاحظت ظرافة المبرمجين؟!). لا نريد هذا، بل يجب أن نُفكّر في كتابة دالّة لتوليد المُحدّدات بالنّيابة، يمكننا استدعاءها بدل ذلك.  أو في حال استخدام إضافة [Toolkit](https://github.com/team-sass/toolkit) لـSass (إما من خلال Bower أو كإضافة لـCompass)، فيمكن في هذه الحالة استخدام الدّالة `dynamic-extend` لإيجاد وإنشاء وتوسعة مُحدّد ديناميكيّ بالنّيابة. كلّ ما عليك فعله إرسال سلسلة نصيّة للبحث عنها (مثل `"button"`):

```sass
@import "toolkit";

@mixin button($color, $extend: true) {
	@include button-static($extend);
	
	background-color: $color;
	border-color: mix(black, $color, 25%);
	
	&:hover {
		background-color: mix(black, $color, 15%);
		border-color: mix(black, $color, 40%);
	}
}

@mixin button-static($extend: true) {
	$button-selector: map-get($Placeholder-Selectors, 'button');
	
	@if $extend == true {
		@include dynamic-extend('button') {
			@include button-static(false);
		}
	}
	@else {
		border: 1px solid;
		border-radius: 5px;
		padding: .25em .5em;

		&:hover {
			cursor: pointer;
		}
	}
}
```

وهكذا يمكن القضاء على أي تكرار في دالّتنا، مما يقلّل حجم ملفّات Sass الأصلية وملفّات CSS النّهائيّة معًا، ممّا يدفعنا خطوة على طريق إعادة استخدام مكوّناتنا في مشاريع أخرى.

مُترجم (بشيء من التصرف) عن مقال ‎[DRY-ing out Your Sass Mixins](http://alistapart.com/article/dry-ing-out-your-sass-mixins)‎ لصاحبه Sam Richard.
