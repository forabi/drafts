تصميم النّصوص البرمجيّة: تنظيم JavaScript
====================================
التّصميم الجيّد للمُنتجات يعني الاعتناء بالنواحي المهمّة للمنتج وإيلاءها اهتمامًا أكبر لتكونَ النتيجة واجهة استخدام جميلة ومُفيدة ومفهومة، لكن إياك أن تظنّ أنّ التصميم يقع على عاتق المُصمّمين فقط!

التصميم مطلوب في النصوص البرمجية، ليس فقط في النصوص البرمجية المكتوبة لبناء الواجهات المرئيّة للمستخدم — بل المقصود تصميم النّصوص البرمجيّة _ذاتها_.

النّصوص المُصمّمة بإتقان أسهل صيانةً وإمكانيّة تطويرها والبناء عليها أكبر، ممّا يجعل المُطوّرين أكثر كفاءة، لأنّها تسمح بتوجيه الاهتمام والجهد نحو بناء مُنتجات أفضل، والنتيجة شعورٌ عامّ بالراحة - راحة المطوّرين والمستخدمين وكلّ من يهمّه الأمر!


لتصميم النّصوص البرمجيّة نواحٍ ثلاث عامّة غير مرتبطة بلغة برمجة مُعيّنة:

1. بنية النظام: المُخطّط العامّ لمجموعة ملفّات المشروع، والقواعد الّتي تُحدد كيف تتواصل مكوّناته المختلفة فيما بينها، سواء كانت نماذج البيانات أم واجهات العرض أم المُتحكّمات.
2. إمكانية الصيانة: إلى أي حدّ يمكن تحسين النصّ البرمجيّ والبناء عليه؟
3. إعادة الاستخدام: هل يمكن إعادة استخدام مكوّنات المشروع؟ هل يسهُل تخصيص أحد هذه المكوّنات لإعادة استخدامه؟

التصميم المُتقن للنّصوص البرمجية في اللّغات المرنة مثل JavaScript يفرض على المطوّر إلزام نفسه بمجموعة من القواعد، لأنّ بيئة JavaScript المُتسامحة قد تسمح للمشروع أن يعمل وإن كانت أجزاؤه مبعثرة هنا وهناك. لذلك فإن تأسيس بنية المشروع والالتزام بها منذ البداية يضمنان انسجامه حتّى تمامه.

نمط الوحدات (module pattern) هو إحدى الطّرق المُجرّبة والّتي أثبتت فائدتها في تصميم البرمجيّات، إذ تتلاءم بنيتها القابلة للتوسيع مع ما يتطلّبه بناء مشروع برمجيّ متماسك وسهل الصّيانة. أعتمدُ هذا النّمط في بناء إضافات jQuery لأنّه يسمح بإعادة استخدام الوحدات وضبطها من خلال مجموعة من الخيارات عبر واجهة برمجيّة مُتقنة التّصميم.

سنستعرض فيما يلي كيف يُكتبُ نصّ برمجيّ موزّع في مكوّنات يمكن استخدامها في مشاريع أخرى لاحقة.

نمط الوحدات
----------
تكثر أنماط التّصميم، وتكثر معها مصادر تعلّمها. كتب [Addy Osmani‏](https://twitter.com/addyosmani) كتابًا ممتازًا (ومجّانيًّا!) عن أنماط التّصميم في JavaScript، أنصح كلّ المطوّرين من كلّ المستويات بالاطّلاع عليه.

يٌسهّل [نمط الوحدات](http://addyosmani.com/resources/essentialjsdesignpatterns/book/#modulepatternjavascript) تنظيم المشروع ويضمن بقاءه نظيفًا ومرتّبًا، والوحدة التي نقصدها ليست سوى كائن JavaScript حرفيّ تقليديّ (object literal) يتضمّن وظائف (methods) وخصائص (properties)، وهذه البنية البسيطة هي أفضل ما في نمط الوحدات لأنّها تسمح حتى لغير المتمرّسين ممّن يطّلعون على النّصّ البرمجيّ أن يقرؤوه ويفهموا بسرعة كيف يعمل.

في التّطبيقات التي تتبنّى هذا النّمط، يكون لكلّ مكوّن فيه وحدة منفصلة تتبع له؛ فمثلاً، يمكن لحقل نصّ في الواجهة المرئيّة يسمح بالإكمال التلقائيّ للكلمات أن يُبنى من وحدتين، الأولى للحقل ذاته، والأخرى لقائمة الكلمات المُقترحَة. تتواصل الوحدتان فيما بينهما دون أن يتداخل نصّاهما البرمجيّان معًا.

هذا العزل بين المكوّنات هو ما يجعلها مناسبة لإقامة بنية متماسكة للمشاريع، حيث تكون العلاقات بين أجزاء التطبيق محدّدة بدقّة؛ ويتولّى حقل النّصّ ما يرتبط به ضمن وحدته، ولا تُترك هذه الأمور مبعثرة بين الملفّات — لنحصل بالنّتيجة على نصّ برمجيّ واضح.

توفّر البنية القائمة على الوحدات فائدة أخرى، إذ تسمح طبيعتها بتسهيل صيانة المشروع، وذلك بتطوير كلّ وحدة بصورة منفصلة دون أن تتأثّر بقيّة أجزاء التّطبيق.

استخدمت نمط الوحدات لإنشاء إضافة [jPanelMenu‏](http://jpanelmenu.com)، وهي إضافة لـjQuery تسمح بإنشاء قائمة للموقع خارج الشاشة (off-canvas menu). سأستخدم هذه الإضافة كمثال عن بناء وحدة وفق هذا النمط.

بناء الوحدة
--------
بدايةً سأفرض ثلاث وظائف وخاصّة واحدة تُستخدم جميعها للتفاعل مع القائمة.

```javascript
var jpm = {
    animated: true,
    openMenu: function( ) {
        …
        this.setMenuStyle( );
    },
    closeMenu: function( ) {
        …
        this.setMenuStyle( );
    },
    setMenuStyle: function( ) { … }
};
```

الفكرة هنا هي تجزئة النصّ إلى أصغر أجزاء مُفيدة ويمكن إعادة استخدامها. كان بإمكاننا كتابة وظيفة واحدة `toggleMenu( )‎` لتبديل وضع القائمة بين الظهور والخفاء، ولكن إنشاء وظيفتين منفصلتين `openMenu( )‎` و`closeMenu( )‎` يعطينا تحكّمًا أكبر ويسمح بإعادة استخدامهما ضمن الوحدة.

لاحظ أنّ استدعاء وظائف الوحدة وخواصّها من ضمن الوحدة ذاتها (مثل استدعاء `setMenuStyle( )‎`) يُسبق بالكلمة المفتاحية `this`، فهذه هي طريقة وصول الوحدات إلى أعضائها.

هذه هي البنية الأساسيّة لكلّ وحدة. يمكنك إضافة خواص ووظائف أخرى عند الحاجة إليها، ولكنّ الوحدة ستبقى بالبساطة ذاتها. بعد إقامة الأساسات السابقة، يمكن بناء طبقة فوقها لإعادة استخدام الوحدة، وذلك من خلال إتاحة ضبط خيارات الوحدة والتحكم بها.

إضافات jQuery
-------------
قد يكون الجانب الثالث للتصميم المُتقن هو الجانب الأهمّ: إعادة الاستخدام. هذه الفقرة يرافقها تحذير؛ فعلى الرغم من وجود طرق لكتابة مكوّنات يمكن إعادة استخدامها بـJavaScript "ساده"، إلّا أنّني أفضّل بناء الوحدة على صورة إضافة jQuery عندما تكون الأمور مُعقّدة، ولهذا أسباب عدّة.

أهمّ هذه الأسباب: بناء الوحدة بصورة إضافة jQuery يوصل للمُطوّرين رسالة ضمنيّة بأنّ وحدتنا تعتمد على وجود jQuery، فهذا يجب أن يكون واضحًا منذ البداية لمن يُريد استخدامها.

يُضاف إلى السّبب السّابق أنّ النّص البرمجيّ الّذي سنكتبه سيكون مُنسجمًا مع بقيّة أجزاء المشروع المُقامة على jQuery، وهذا جيّد لاعتبارات جماليّة أولًا، ولأنّه يسمح للمطوّرين بفهم استخدام الإضافة إلى حدٍّ ما دون كثير عناء، فهي إذًا طريقة أخرى لتحسين الواجهة البرمجيّة الّتي سيتخدمها المطوّرون.

قبل البدء ببناء إضافة jQuery، تأكّد من أنّها لا تتعارض مع مكتبات JavaScript أخرى تستخدم الرّمز `$`. لا تقلق فالأمر أبسط ممّا يبدو، كلّ ما عليك هو إحاطة نصّ الإضافة بما يلي:

```javascript
(function($) {
    // نص الإضافة البرمجي هنا
})(jQuery);
```

بعد ذلك سنُعدُّ الإضافة ونُلقي بالوحدة الّتي كتبناها سابقًا ضمن الإضافة. إضافة jQuery ليس إلا وظيفة مُعرِّفة على الكائن `$` (كائن jQuery).

```javascript
(function($) {
    $.jPanelMenu = function( ) {
        var jpm = {
            animated: true,
            openMenu: function( ) {
                …
                this.setMenuStyle( );
            },
            closeMenu: function( ) {
                …
                this.setMenuStyle( );
            },
            setMenuStyle: function( ) { … }
        };
    };
})(jQuery);
```

كل ما علينا لاستخدام الإضافة استدعاء الدّالة الّتي أنشأناها للتّوّ.

```javascript
var jpm = $.jPanelMenu( );
```

الخيارات
-------
لا تكون الإضافة صالحةً لإعادة الاستخدام إن لم تُتِح تخصيصها وفق متطلّبات المشروع، فكلّ مشروع له أسلوبه الخاصّ وبنيته الخاصّة، وإتاحة ضبط الوحدة بالخيارات يسمح للإضافة بالتكيّف مع حاجات المشاريع المختلفة.

تزويد الوحدة بقيم مبدئية للخيارات أمرٌ مفضَّل. أسهل طريقة لفعل ذلك استخدام وظيفة jQuery ‏`‎$.extend()‎` الّتي تستقبل على الأقل مُعاملين اثنين (arguments).

اجعل المُعامل الأول لهذه الوظيفة كائنًا يوفّر كل الخيارات المتاحة وقيهما المبدئيّة، واجعل المعامل الثاني الخيارات الّتي يُقدّمها المُطور الّذي يستخدم الإضافة. ستقوم الوظيفة بدمج الكائنين مع إحلال الخيارات الّتي قدّمها المّطوّر مكان مقابلاتها المبدئيّة.

```javascript
(function($) {
    $.jPanelMenu = function(options) {
        var jpm = {
            options: $.extend({
                'animated': true,
                'duration': 500,
                'direction': 'left'
            }, options),
            openMenu: function( ) {
                …
                this.setMenuStyle( );
            },
            closeMenu: function( ) {
                …
                this.setMenuStyle( );
            },
            setMenuStyle: function( ) { … }
        };
    };
})(jQuery);
```

إضافةً إلى توفير القيم المبدئيّة، تشرح الخيارات نفسها بنفسها، أي يستطيع من يقرأ النّصّ البرمجيّ رؤية كلّ الخيارات المُتاحة مباشرةً.

أتِح أكبر عددٍ ممكن من الخيارات، فهذا قد يكون مُفيدًا في المستقبل، والمرونة لن تضرّ!

الواجهة البرمجيّة
------------
الخيارات الّتي تتيحها الإضافة وسيلة رائعة لتخصيص عملها، وأمّا الواجهة البرمجيّة، فهي تتيح توسيع وظيفة الإضافة بكشف محتوى الإضافة من خصائص ووظائف ليستطيع المشروع الاستفادة منها.

مع أنّ إتاحة أكبر ما يمكن من الوحدة عبر الواجهة البرمجيّة أمرٌ حسن، إلا أنّه ليس على من يستخدمها من الخارج الوصول إلى كلّ المكوّنات الدّاخليّة. أتِح في الواجهة البرمجيّة العناصر الّتي ستُستخدم فقط.

في مثالنا، يجب على الإضافة أن تُتيح الوصول إلى وظائف فتح القائمة وإغلاقها فقط، أمّا الوظيفة الداخليّة `setMenuStyle( )‎` فيجب أن تُستدعى عندما تُفتح القائمة أو تغلق، ولكن ليس على الأجزاء الخارجيّة من المشروع أن تصل إليها.

لكشف الواجهة البرمجيّة، أعِد كائنًا يحوي الوظائف والخصائص المرغوب كشفها في نهاية نصّ الإضافة، بإمكانك أيضًا ربط الكائنات المُعادة مع تلك المُحتواة ضمن الوحدة؛ وفي هذا التنظيم يكمن جمال نمط الوحدات.


```javascript
(function($) {
    $.jPanelMenu = function(options) {
        var jpm = {
            options: $.extend({
                'animated': true,
                'duration': 500,
                'direction': 'left'
            }, options),
            openMenu: function( ) {
                …
                this.setMenuStyle( );
            },
            closeMenu: function( ) {
                …
                this.setMenuStyle( );
            },
            setMenuStyle: function( ) { … }
        };

        return {
            open: jpm.openMenu,
            close: jpm.closeMenu,
            someComplexMethod: function( ) { … }
        };
    };
})(jQuery);
```

ستكون خصائص الواجهة ووظائفها متاحةً عبر هذا الكائن الّذي أُعيد بعد تهيئة الإضافة.

```javascript
var jpm = $.jPanelMenu({
    duration: 1000,
    …
});
jpm.open( );
```

"صقل" واجهة المُطوّرين
-------------------
باتّباع الخطوط العامّة البسيطة والقليل من المفاهيم، أنشأنا لأنفسنا إضافة صالحةً لإعادة الاستخدام والتّوسعة، وهذا سيُسهّل عملنا كثيرًا، وككلّ ما نُنجزه تبقى التّجربة العامل الحاسم قبل اعتماد هذا النّمط على مستوى فريقك، وبما يُناسب سياق عملك.

كلّ ما وجدت نفسي أكتب شيئًا يمكن إعادة استخدامه لاحقًا، بادرتُ لفصله في إضافة jQuery مبنيّة كوحدة. أفضل ما في هذا الأسلوب أنّه يجبرك على استخدام النّصّ الّذي تكتبه وتجربته، وعندما تستخدم شيئًا وأنت تُنشئه، يكون تحديد مواضع القوّة ومواضع الضّعف أسرع، الأمر الّذي يسمح لك بتغيير خطّة العمل باكرًا.

هذه العمليّة تُعطي في النّهاية نصًّا برمجيًّا مُجرّبًا وجاهزًا لإتاحته كمشروع مفتوح المصدر يستقبل المساهمات، وقد قمت بالفعل بنشر إضافاتي (الّتي أعتبرها شبه ممتازة) على [GitHub‏](https://github.com/acolangelo).

وحتّى إن لم ترغب بنشر ما تكتبه للعموم، فإنّ تصميم النّصّ البرمجيّ أمر شديد الأهمّيّة، وستشكر نفسك بسببه في المستقبل!