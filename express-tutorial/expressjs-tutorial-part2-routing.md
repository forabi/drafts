توجيه الرّوابط في Express
=======================
في الدّرس السّابق قمنا بثتبيت Node.js وخادوم MySQL وبقيّة متطلّبات المشروع، حان الوقت لنبدأ العمل الحقيقيّ!

##إنشاء صفحة المدوّنة الرئيسيّة
أهمّ ما تعرضه الصّفحة الرئيسيّة لكلّ مدوّنة عادةً آخر التّدوينات بتاريخ كتابتها من الأحدث للأقدم، وسنركّز الآن على تطبيق هذا الجزء على أنّ نتوسّع في إضافة الميّزات في وقتٍ لاحق.

أنشئ الملفّ `index.js` الّذي يُمثّل نقطة انطلاق مشروعنا، ولنبدأ باستيراد Express ضمنه:

```javascript
var express = require("express");
```

لنُنشئ الآن تطبيق Express جديد، وهو يمثّل الخادوم الذي يُدير مدوّنتنا بالكامل، يتمّ إنشاء تطبيق Express ببساطة باستدعاء دالّة `express` الّتي أنشأناها لتوّنا:

```javascript
var express = require("express");
var app = express();

```

تكون الصّفحة الرئيسيّة للمدوّنة على الرّابط الجذر للموقع عادةً، وهو ما نُعبّر عنه بـ`/`، سنطلب من تطبيقنا الاستجابة للطّلبات التي تصل إلى هذا الرّابط بعرض صفحة HTML تحوي آخر 10 تدوينات مرتّبة وفق تاريخ كتابتها من الأحدث إلى الأقدم:

```javascript
var express = require("express");
var app = express();

app.get("/", function(request, response) {
   // أرسل HTML
});
```

لندع إرسال الصّفحة جانبًا ولنفهم أسلوب استخدام Express، لكلّ تطبيق Express وظائف أربعة تُستخدم في استقبال وتوجيه الطّلبات، وهي `get` و`post` و`put` و`del`، وهذه الوظائف توافق أفعال HTTP الشّائعة. ولكن ما هي أفعال HTTP؟

###كيف يعمل HTTP؟
في كلّ مرّة تزور صفحة على الويب فإنّ متصفّحك يرسل للخادوم الذي يستضيف الموقع طلبًا بالحصول (`GET`) على المحتوى في الرابط الذي كتبته، يكون طلب HTTP هذا مشابهًا للمثال التّالي (المُبسَّط عمدًا):

```http
GET www.myblog.com/hello-world HTTP/1.1
Accept: text/html
Accept-Language: ar-sy,ar;
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:34.0) Gecko/20100101 Firefox/34.0
```
تُسمّى الحقول `Accept` و`Accept-Language`... بترويسات الطّلب (Request Headings)، ولكلّ ترويسة معنىً بالنّسبة للخادوم الذي يستقبل الطّلب، فمثلاً يقوم المتصفّح في الحقل `User-Agent` بالتّعريف عن نفسه، وهو ما يسمح للخادوم بإرسال جواب مخصّص لكلّ متصفّح مثلاً (إن شاء)، وفي الحقل `Accept-Language` يُرسِل المتصفّح اللّغات الّتي يرغب المستخدم برؤية الجواب بها، فيقوم الخادوم بإرسالة الصّفحة بالعربيّة (سوريا) `ar-sy` في حالتنا إن توفّرت لديه، أو بالعربية `ar` كخيار ثانٍ... وهكذا. يردّ الخادوم على الطّلب بجواب HTTP (‏HTTP Response) الذي يُشبه مثالنا هذا:

```http
HTTP/1.1 200 OK
Content-Type: text/xml; charset=utf-8
Content-Length: 3918

<!DOCTYPE html>
<html lang="ar">
    <head>
    <title>مُدوّنتي - مرحبًا بالعالم!</title>
    </head>
    <body>
    مرحبًا بكم في مدوّنتي المتواضعة! 
    </body>
</html>
```

السّطر الأوّل في الجواب يُسمّى سطر الحالة، ويتضمّن حالة الطّلب (حيث الرّقم `200` يعني أن الخادوم تلقّى الطّلب وردّ عليه بما هو متوقّع)، بقيّة السّطور هي ترويسات الجواب (Response Headings) الّتي تعني كلّ واحدة منها شيئًا ما لمستقبل الجواب (المتصفّح). يلي الترويسات متن الجواب (Response Body) الذي يحوي في حالتنا صفحة HTML الّتي سيقوم المتصفّح بعرضها على المستخدم.

فعل `GET` الذي استخدمناه ليس وحيدًا، فهناك أفعال أخرى مثل `POST` الذي يُستخدم في المتصفّح لإرسال الحقول التي يُعبّئها المستخدم (كتعبئة حقل تسجيل الدّخول)، والفعل `DELETE` الذّي يستخدم ليطلب من الخادوم _حذف_ محتوى ما (مثل حذف تدوينة من قبل المستخدم). الجدير بالذّكر أن الخادوم حرّ التّصرّف بالطّلبات التي يتلقّاها، والطّريقة التي شرحناها بهذه الأفعال مبنيّة على التّقاليد الشّائعة لاستخدامها، فلا شيء في الحقيقة يمنع الخادوم من حذف تدوينة عندما يتلقّى طلب `GET` بدلاً من `DELETE` وإنّما هو عُرف متّفق عليه.

لنعد الآن إلى مثالنا السّابق، تقبل الوظيفة `get` مُعاملين أولهما الرّابط المطلوب التّعامل معه، والأخرى دالّة تقرأ الطّلب وتعدّل جوابه قبل إرسال الجواب للمُتصفّح، يمكن إرسال متن الجواب للمتصفّح من خلال الوظيفة `send()‎` للكائن `response`:


```javascript
var express = require("express");
var app = express();

app.get("/", function(request, response) {
    var html = "<!DOCTYPE html><html lang='ar'>" +
        "<head><title>مُدوّنتي!</title></head>" +
        "<body>" + posts.map(function(post) { return "<li>" + post.title + "</li>"; }).join("") + "</body></html>"
    response.send(html);
});
```

في الحالة الافتراضية سيكون جواب هذا الطّلب بالرّمز `‎200 OK` مع متن يطابق محتوى المُتغيّر `html`. سنتعرّف فيما بعد على كيفيّة تغيير رموز الحالة بحيث نُرسل الرّمز الشّهير `‎404 Not Found` عندما لا نجد تدوينة على الرّابط المطلوب.

سيتوقّف البرنامج في هذه الحالة مُعطيًا خطأ بسبب كون `posts` غير معرّف، كلّ ما علينا الآن هو جلب التّدوينات من خلال قاعدة البيانات وتخزينها ضمن المُتغيّر `posts`، نحتاج إذًا لتنفيذ استعلام MySQL لجلب أحدث التدوينات، ولهذا سنقوم باستيراد وحدة `mysql` التي قمنا بتثبيتها وتأمين الاتصال بقاعدة البيانات:

```javascript
var express = require("express");
var mysql = require("mysql");

var connection = mysql.createConnection({ host: "localhost", user: "root", password: "", database: "myblog" });
connection.connect();

var app = express();

app.get("/", function(request, response) {
    connection.query( "SELECT * from `posts` ORDER BY date DESC LIMIT 10;", function(err, posts) {
        if (err) throw err;

        var html = "<!DOCTYPE html><html lang='ar'>" +
        "<head><title>مُدوّنتي!</title></head>" +
        "<body>" + posts.map(function(post) { return "<li>" + post.title + "</li>"; }).join("") + "</body></html>";
        
        response.send(html);
    });
    
});
```

ملاحظة: لا تنسَ تغيير اسم المستخدم وكلمة المرور ليتوافقا مع ما اخترته أثناء تثبيت MySQL.
تمتلك وحدة `mysql` وظيفة `createConnection()‎` الّتي تُعيد لنا نسخة من اتّصال بقاعدة البيانات الّتي حدّدناها، والذي يمكن بدؤه باستدعاء الوظيفة `connect()‎` ثم تّنفيذ الاستعلامات `query()‎` الّتي تتمّ بأسلوب غير متزامن (asynchronous) لتُعيد لنا الصّفوف النّاتجة عن الاستعلام ضمن المعامل الثّاني للدّالة (`function(err, posts) { ... }‎`) الّتي تُستدعى بعد انتهاء الاستعلام.

بهذه السّطور القليلة التي يمكن فهمها بالقليل من الجهد تمكنّنا من إنشاء مدوّنة بسيطة، وهنا يبرز جمال Node.js الذي يسمح للمبتدئين بتطبيق أفكار قد تبدو بعيدة المنال وجعلها واقعًا ملموسًا!

الآن حان وقت تجربة المشروع، نحتاج لإخبار Express بالإنصات إلى الطّلبات الّتي ترد على منفذ معيّن على جهازنا (`localhost`):

```javascript
var express = require("express");
var mysql = require("mysql");

var connection = mysql.createConnection({ host: "localhost", user: "root", password: "", database: "myblog" });
connection.connect();

var app = express();

app.get("/", function(request, response) {
    connection.query( "SELECT * from `posts` ORDER BY date DESC LIMIT 10;", function(err, posts) {
        if (err) throw err;

        var html = "<!DOCTYPE html><html lang='ar'>" +
        "<head><title>مُدوّنتي!</title></head>" +
        "<body>" + posts.map(function(post) { return "<li>" + post.title + "</li>"; }).join("") + "</body></html>";
        
        response.send(html);
    });
    
});

app.listen(3000);
```

لبدء البرنامج، افتح الطّرفيّة وانتقل إلى مجلّد المشروع، ثم نفّذ الأمر التّالي:

```bash
node index.js
```

ستتوقّف المؤشّر في الطّرفيّة عن الاستجابة بسبب انشغال هذه الطّرفيّة بتنفيذ البرنامج، اذهب إلى المتصفّح وانتقل إلى الرابط http://localhost:3000/‎ وشاهد النّتيجة:

![الصّفحة الرئيسية للمدونة](home-page.jpg)

قد تبدو الصّفحة غاية في البساطة وخالية من أي عُنصر جماليّ، لكنّ ما يهمّنا الآن هو أنّنا قمنا بإنشاء خادوم يتواصل مع قاعدة بيانات ويعرض النّتائج على المستخدم... كلّ هذا في 16 سطرًا من JavaScript!

لإنهاء البرنامج عُد إلى الطّرفيّة ذاتها واضغط Ctrl+C.

##تعرّف على لغة القوالب Jade
بعد أن تأكدنا من تنفيذ المكوّن الرئيسيّ لمشروعنا، سنعمل على تحسين شيفرتنا لجعلها أكثر بساطة وقابلة للتّطوير بسهولة فيما بعد. إذا ألقينا نظرةً على آخر ما كتبناه، سرعان ما نكتشف التّعقيد الذي ستصل إليه شيفرتنا إن أردنا إضافة المزيد من المزايا ضمن HTML، لأنّ هذا يعني إضافة المزيد من النّصّ إلى المتغيّر `html` بحيث يصبح طويلاً جدًّا وصعب القراءة؛ لا بدّ أن توجد طريقة أفضل من هذه!

تتوفّر في كلّ اللّغات طريقة لتوليد صفحات HTML ديناميكيّة على الخادوم، بمعنى أنّه يمكن تغيير بعض محتوياتها وإدخال محتوى مُتغيّر فيها قبل إرسالها إلى المستخدم، هل تساءلت يومًا كيف يعرض فيس بوك لكلّ مستخدم صفحةً خاصّة به؟ بحيث يكون هيكلها متماثلاً لكلّ المستخدمين ولكن محتواها من الأخبار مختلف من مستخدم لآخر، الجواب هو باستخدام القوالب؛ لن نقوم بإنشاء فيس بوك جديد الآن، لكنّنا سنستفيد من ميزات القوالب الدّيناميكيّة لتوليد HTML بدلًا من كتابتها يدويًّا ضمن شيفرتنا!

في عالم Node.js ستجد الكثير من لغات القولبة، لكنّ الامتداد الطّبيعيّ لاستخدام Express يكون باعتماد [Jade](‏http://jade-lang.com) كلغة قولبة كونها بدأت من المُطوّر ذاته، لنُعد كتابة HTML الصّفحة الرّئيسيّة لمدوّنتنا باستخدام Jade:

```jade
doctype html
html(lang="ar")
    head
        title "مُدوّنتي!"
    body
        for post in posts
            li #{ post.title }
```

قارن بين نصّ HTML ونصّ Jade الأخير، أوّل ما نلاحظه في Jade هو بساطة صياغتها، فهي تلغي الوسوم النّهائيّة (مثل `</head>` و`</body>`) وتستعيض عن ذلك بكونها حسّاسة للمحاذاة، فكون الوسم `title` مُزاحًا إلى يمين `head` يعني أنّه محتوىً ضمنه، وكذلك الأمر بالنّسبة لـ`body`، نلاحظ كذلك دعم Jade للحلقات والمُتغيّرات، وهي من أبرز مزايا لغات القوالب، لأنها تسمح بتوليد عناصر متكرّرة دون الحاجة لكتابتها يدويًّا.

سنحتاج أوّلًا لتثبيت Jade وحفظه في متطلّبات المشروع:
```bash
npm install jade --save
```

احفظ شيفرة Jade السابقة في ملفّ `home.jade` ضمن مجلّد جديد سمّه `views` داخل مُجلّد المشروع، ثمّ عُد للملفّ `index.js`، ولنقم باستخدام Jade عوضًا عن الأسلوب السابق:

```javascript
var express = require("express");
var mysql = require("mysql");

var connection = mysql.createConnection({ host: "localhost", user: "root", password: "", database: "myblog" });
connection.connect();

var app = express();

app.set("view engine", "jade");

app.get("/", function(request, response) {
    connection.query( "SELECT * from `posts` ORDER BY date DESC LIMIT 10;", function(err, posts) {
        response.render("home", { posts: posts });
    });
    
});

app.listen(3000);
```
ضبطنا الإعداد `view engine` في Express إلى القيمة `"jade"`، يستخدم Express هذا الإعداد عندما يُطلب منه عرض ملفّ ما باستخدام الوظيفة `render` التّابعة لكائن الجواب `response`، بحيث يبحث عن مُفسّر لغة القوالب (`jade` في حالتنا) ويطلب منه تحويل الملفّ "home" إلى HTML، مُمرّرًا له الكائن الذي يحوي المتغيّرات الّتي يحتاجها (`{ posts: posts }`). يبحث Express عن ملفّات العرض في المجلّد `views` بشكل افتراضيّ، وهو ما قمنا بإنشاءه للتّوّ.

قم بتشغيل البرنامج مرّة أخرى باستخدام الأمر `node index.js` ثمّ زر الرّابط http://localhost:3000/‎. لم يتغيّر شيء ظاهر، لكنّنا انتقلنا إلى استخدام لغة قوالب وراء الكواليس، وسنستفيد من هذا بكتابة شيفرة أبسط وأكثر تنظيمًا.

لنقم الآن بتعديل القالب `home.jade` ليبدو بشكل أجمل:

```jade
doctype html
html(lang="ar", dir="rtl")
    head
        title "مُدوّنتي!"
    body
        style
            :css
                body {
                    font-family: Arial, sans-serif;
                }
        
        h1 مُدوّنتي
        hr
        for post in posts
            h2 #{ post.title }
            p #{ post.body }
            small بتاريخ #{ post.date }
```

قمنا بتغيير اتّجاه النّص لجعله من اليمين إلى اليسار عبر الخاصة `"dir"`، ثمّ أدخلنا بعض التنسيق من خلال الوسم "`<style>`" في HTML، تسمح Jade بكتابة لغات أخرى ضمن القالب مثل كتابة CSS وCoffeeScript أو Markdown أو Sass عبر الصّياغة `:language` ليتم تحويلها إلى اللّغة المناسبة للمتصفّح إن تطلّب الأمر، وفي هذه الحالة أدخلنا CSS بسيط (الذي لا يحتاج للتّحويل) بكتابة `:css` قبل الشّيفرة. سنتعرّف على مزيد من مزايا Jade خلال عملنا.

![الصّفحة الرئيسية للمدونة بعد تحسين التنسيق](home-page-jade.jpg)

تبدو مدوّنتنا بشكل أجمل الآن، لكنّها بالتأكيد تحتاج المزيد من العمل! يمكننا تحسين عرض صيغة التّاريخ باستخدام مكتبة [moment‏](http://momentjs.com) للتّعامل مع التّواريخ والوقت، سنحتاج أولاً إلى تثبيتها وحفظها في متطلّبات المشروع:

```bash
npm install --save moment
```

سنُدخل التّعديلات اللّازمة على الملفّين `index.js` و`home.jade`:


```javascript

var express = require("express");
var mysql = require("mysql");

var moment = require("moment");
moment.locale("ar");

var formatDate = function(date) {
    return moment(new Date(date)).fromNow();
}

var connection = mysql.createConnection({ host: "localhost", user: "root", password: "", database: "myblog" });
connection.connect();

var app = express();

app.set("view engine", "jade");

app.get("/", function(request, response) {
    connection.query( "SELECT * from `posts` ORDER BY date DESC LIMIT 10;", function(err, posts) {
        response.render("home", { posts: posts, formatDate: formatDate });
    });
    
});

app.listen(3000);
```

```jade
doctype html
html(lang="ar", dir="rtl")
    head
        title "مُدوّنتي!"
    body
        style
            :css
                body {
                    font-family: Arial, sans-serif;
                }
        
        h1 مُدوّنتي
        hr
        for post in posts
            h2 #{ post.title }
            p #{ post.body }
            small كُتِبَت #{ formatDate(post.date) }
```

يمكن تمرير الدّوال (functions) إلى Jade كما نُمرّر المتغيّرات، وفي حالتنا قمنا بتعريف دالّة تقوم بتنسيق التّاريخ الذي تتلقاه بصياغة نسبيّة (منذ كذا يومًا، منذ ساعتين...) وذلك بالاستفادة من مكتبة moment التي استوردناها وعيّنّا لغة التّاريخ فيها إلى العربيّة. أجرينا التغييرات اللازمة في Jade مستخدمين الدّالة التي فرضناها وأصبحت متوفّرة ضمن القالب:

![الصّفحة الرئيسية للمدونة بعد تحسين التنسيق وصياغة التّاريخ](home-page-jade-moment.jpg)

##إنشاء صفحة التدوينة
من المعتاد لصفحات التّدوينات أن تكون بهذه الهيئة: http://myblog.com/posts/hello-world، ويمكن أن نشاهد في مدوّنات أخرى روابط تحوي تاريخ كتابة التّدوينة أو رقمًا خاصًّا بها... إلخ، لكنّنا سندع الأمور بسيطة. لدينا حاليًّا 4 تدوينات، ستكون روابطها:

* ‎/posts/hello-world
* ‎/posts/quotes-1
* ‎/posts/quotes-2
* ‎/posts/quotes-3

الثّابت بين هذه الرّوابط هو اعتمادها على الحقل `slug` الّذي أدخلناه في كلّ سطر في جدول التّدوينات. من غير المنطقيّ أن نُسجّل رابطًا لكلّ تدوينة على حدة في Express، وسيصبح هذا مستحيلاً مع إنشاء تدوينات جديدة. يوفّر Express آليّة للإجابة على الطّلبات الواردة على الروابط التي _تطابق نمطًا معيّنًا_، وهو في حالتنا `/posts/‏` متبوعًا بحقل متغيّر `slug`، أو `‎/posts/:slug` بصياغة Express، سنضيف الشيفرة التالية إلى برنامجنا (قبل آخر سطر):

```javascript
app.get("/posts/:slug", function(request, response) {

    var slug = request.params.slug;
    
    connection.query("SELECT * from `posts` WHERE slug = ?", [ slug ], function(err, rows) {
        var post = rows[0];
        response.render("post", { post: post, formatDate: formatDate });
    });

})
```

نطلب من Express الاستجابة لأي رابط يطابق النمط `"‎/posts/:slug"` بالبحث عن التدوينة التي تملك القيمة `slug` ضمن العمود الموافق في جدول التّدوينات، نلاحظ أنّ Express يوفّر لنا هذه القيمة المتغيّرة من خلال الكائن `params` التابع لكائن الطّلب `request` (كائن الطّلب يحوي كذلك ترويسات الطّلب الّتي تحدّثنا عنها في الجزء السّابق). من المهمّ أنّ نحمي قاعدة بياناتنا من العبث وذلك بتجنب [هجمات حقن SQL‏](https://www.youtube.com/watch?v=_jKylhJtPmI)، ولهذا توفّر وحدة `mysql` دالّة `query()‎` ذاتها لكن مع 3 معاملات بدل اثنين فقط، حيث يكون الثاني مصفوفة تحوي القيم الّتي نريد التأكّد من سلامتها (escape) قبل إحلالها محلّ إشارات الاستفهام في استعلاماتنا. هذا أسلوب شائع جدًا في استعلامات SQL، وهو أقلّ ما يمكننا فعله لحماية قاعدة البيانات. لم نقم بعد بإنشاء قالب صفحة التّدوينة، لننشئ ملفًا جديدًا اسمه `post.jade` ضمن مجلّد `views`:

```jade
doctype html
html(lang="ar", dir="rtl")
    head
        title مُدوّنتي!
    body
        style
            :css
                body {
                    font-family: Arial, sans-serif;
                }
        
        h1 مُدوّنتي
        hr
        h2 #{ post.title }
        p #{ post.body }
        small كُتِبَت #{ formatDate(post.date) }
```

لنبدأ برنامجنا، ونذهب إلى الصّفحة http://localhost:3000/posts/hello-world:

![صفحة التّدوينة "مرحبًا بالعالم!"](post-page.jpg)

لدينا الآن بعض المشكلات، ماذا يحدث لو أدخلنا رابطًا لتدوينة غير موجودة؟ جرّب مثلاً http://localhost:3000/posts/another-post:

![خطأ في تفسير القالب](post-page-parse-error.jpg)

وقع خطأ في تفسير Jade سببه أن المتغيّر `post` الّذي وصله هو في الحقيقة غير معرّف `undefined`، لأنّه ما من تدوينة في قاعدة البيانات يطابق حقل `slug` فيها القيمة `another-post`، وعندما أجرينا الاستعلام أُعيدت لنا مصفوفة فارغة `rows`، وفي JavaScript فإنّ محاولة الوصول إلى خاصّة غير موجودة (`"0"`) في عنصر مُعرّف (المصفوفة `rows` في حالتنا) تُرجع `undefined`. ما الذي كان علينا فعله لتجنب هذا الخطأ؟

أولاً يجب التأكّد قبل كلّ شيء أنّ الخطأ الذي يقع في مرحلة الاستعلام يتم التّعامل معه (handled) قبل الانتقال لما بعده، انتبه إلى أنّ الاستعلام الذي يتم بنجاح ويعيد مصفوفة فارغة لا يعتبر خطأ، لذا يجب التّعامل مع هذه الحالة أيضًا؛ مبدئيًا سنكتفي بإيقاف تنفيذ الدّالة:



```javascript
app.get("/posts/:slug", function(request, response) {

    var slug = request.params.slug;
    
    connection.query("SELECT * from `posts` WHERE slug = ?", [ slug ], function(err, rows) {
        if (err || rows.length == 0) return;
        var post = rows[0];
        response.render("post", { post: post, formatDate: formatDate });
    });

})
```
جرّب الآن إعادة تشغيل البرنامج وزيارة الصّفحة ذاتها... سيستمرّ المتصفّح بمحاولة تحميلها لوقت طويل قبل أن يفشل بسبب انتهاء مهلة الطّلب. لماذا يحدث هذا؟

علينا أن نفهم واحدًا من أهمّ المفاهيم في Express، وهو الكيفيّة التّي تسير بها عمليّة توجيه الرّوابط (routing)، في شيفرتنا الأخيرة سيتوقف Express عند `return` دون أن يعرف ما ينبغي فعله في **الخطوة التّالية**، وهذا يجعل البرنامج عالقًا في الفراغ، نحتاج لطريقة نخبر بها Express أن يتابع التّنفيذ ويفعل شيئًا ما عندما تنتهي إحدى وظائف التّعامل مع الرّوابط، ولهذا يعطينا Express دالّة `next` التي تتوفّر كمعامل ثالث للدالة التّي تتلقّى الرابط:
```javascript
app.get("/posts/:slug", function(request, response, next) {

    var slug = request.params.slug;
    
    connection.query("SELECT * from `posts` WHERE slug = ?", [ slug ], function(err, rows) {
        if (err || rows.length == 0) return next();
        var post = rows[0];
        response.render("post", { post: post, formatDate: formatDate });
    });

})
```
أعد تشغيل البرنامج وزر الصّفحة مجدًّدا:

![صفحة تدوينة غير موجودة](post-page-cannot-get.jpg)

هذا أفضل! لكن ما هي الدّالة التّالية التي استدعاها Express ليعرف أنّ صفحة على هذا الرّابط غير موجودة؟ الإجابة هي أنّ Express يحوي بشكل افتراضي دوالّ داخليّة يستدعيها عندما لا نزوّده بالدّالة التّالية، لكنّنا نستطيع فعل ذلك بسهولة:

```javascript
app.get("/posts/:slug", function(request, response, next) {

    var slug = request.params.slug;
    
    connection.query("SELECT * from `posts` WHERE slug = ?", [ slug ], function(err, rows) {
        if (err || rows.length == 0) return next();
        var post = rows[0];
        response.render("post", { post: post, formatDate: formatDate });
        return;
    });

})

app.get("/posts/:slug", function(request, response) {
    response.send("التدوينة غير موجودة");
})
```

سجّلنا أكثر من دالّة تتعامل مع الرّابط ذاته، سينفّذها Express جميعًا بالتّرتيب ذاته، يمكن لكلّ دالّة أن تستدعي الدّالة التّالية أو أن توقف سلسلة الاستدعاءات بإرسال الطّلب للمتصفّح وإيقاف التّنفيذ. (إرسال الطّلب لا يعني بالضّرورة أنّ الدّوال التّالية لن تنفّذ، بل يجب إيقاف التّنفيذ صراحةً إن لم نرغب بهذا السّلوك). أعد تشغيل البرنامج ثم زُر الصّفحة ذاتها:

![التّدوينة غير موجودة](post-page-not-found.jpg)

حدث ما نتوقّعه بالضّبط، على سبيل التّأكد من كوننا لم نعبث بالوظيفة الرئيسيّة، جرّب زيارة تدوينة موجودة مثل http://localhost:3000/posts/quotes-1.

كاختبار لك، قم بتعديل صفحة "التّدوينة غير موجودة" مستخدمًا قالبًا خاصًّا ولتجعله جميلاً! تصرّف براحتك!

سأقوم بإدخال تعديل بسيط على الدّالة الثّانية، لجعلها ترسل الرّمز `404` (غير موجود) للمتصفّح بدل القيمة الافتراضيّة (`200`):

```javascript
app.get("/posts/:slug", function(request, response) {
    response.status(404);
    response.send("التدوينة غير موجودة");
})
```
لن يغيّر هذا شيئًا في الظّاهر، لكنّه العرف المتّفق عليه، يمكن لبعض المتصفّحات أن تتعامل مع خطأ كهذا بعرض صفحة نتائج البحث على Google مثلاً (مع أنّه لا يوجد متصفّح يفعل ذلك)، لكنّها طريقة HTTP في التّفاهم بين الخادوم والمتصفّح.

عظيم! لدينا الآن صفحة رئيسيّة منسّقة وصفحات مفردة للتّدوينات، في الدّرس القادم سنقوم بإنشاء نظام للمستخدمين تمهيدًا لإتاحة التّعليقات وكتابة تدوينات جديدة.
