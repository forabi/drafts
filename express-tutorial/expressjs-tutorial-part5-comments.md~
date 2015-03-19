إنشاء مدوّنة باستخدام Node.js وExpress (الجزء الخامس)
==============================================
بعد أن أنشأنا نظام المستخدمين والجلسات، أصبحنا جاهزين الآن لبناء نظام التّعليقات، ثم إتاحة إمكانية إنشاء تدوينة جديدة وتعديل التّدوينات السّابقة.

##إتاحة التّعليق
لحفظ التّعليقات نحتاج أولًا إلى جدول جديد يحفظ نصّ التعليق وتاريخه وكاتبه والتّدوينة التي أُضيف إليها، افتح صدفة MySQL واتصّل بقاعدة البيانات ثم نفّذ هذا الاستعلام:

```sql
CREATE TABLE `comments` (id INT PRIMARY KEY AUTO_INCREMENT, post_id INT NOT NULL, user_id INT NOT NULL, body VARCHAR(500) NOT NULL, created TIMESTAMP, FOREIGN KEY (post_id) REFERENCES `posts` (id), FOREIGN KEY (user_id) REFERENCES `users` (id), INDEX (post_id));
```

سنُعدّل قالب صفحة التّدوينة `post.jade` ونضيف حقلاً يسمح للمستخدم المُسجّل دخوله بإضافة التّعليق، ويعرض للزوّار إمكانيّة تسجيل الدّخول:

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
        #comments
            h3 التعليقات
            if post.comments
                for comment in post.comments
                    p يقول 
                        b #{ comment.full_name }: 
                        br
                        | #{ comment.body }
                    small #{ formatDate(comment.created) }
                    hr
            else
                p لا تعليقات إلى الآن

        if user
            form(action="/posts/" + post.id + "/comments", method="POST")
                textarea(name="comment", placeholder="أكتب تعليقك")
                input(type="submit", value="أرسل التّعليق")
        else
            span لإضافة تعليقاتك، 
            a(href="/login") سجّل دخولك
            |  أو 
            a(href="/signup") أنشئ حسابًا جديدًا
```

لقد استبقنا الأمور قليلاً، سيرسل النّموذج الذي يشمل التّعليق إلى الرّابط `‎/posts/:post_id/comments`، لنقم بتوجيه هذا الرّابط:

```javascript
app.post("/posts/:post_id/comments", parseBody, function(request, response) {
    var body = request.body.comment;
    var user_id = request.user.id;
    var post_id = request.params.post_id;
    var created = new Date();
    
    connection.query("INSERT INTO `comments` (post_id, user_id, body, created) VALUES (?, ?, ?, ?)", [ post_id, user_id, body, created ], function(err) {
        if (err) {
            response.status(500);
            response.send("تعذّرت إضافة التّعليق، حاول مجدّدًا.");
            return;
        }
        
        response.status(201);
        response.send("أُضيف التعليق");
    })
})
```

ملاحظة: اخترنا أن يُرسل الرّابط إلى النّمط `‎/posts/:post_id/comments` بدل `‎/posts/:slug/comments` على سبيل تبسيط الأمور، لا يُميّز Express بين `‎:slug` و`‎:post_id` فكلاهما بالنّسبه له متُغيّران لا يخضعان لأيّة شروط، يمكننا التأكد من كون `‎:post_id` رقمًا باستخدام الوظيفة `param()‎` على التّطبيق والّتي نشترط بها أن يطابق المُتغيّر `post_id` التعبير النظامي (regular expression) التّالي: 

```javascript
app.param('post_id', /^[0-9]+$/);
```
وعندها لن تتلقّى هذا الدّالة سوى الرّوابط التي تحمل رقمًا في موقع `‎:post_id`.

هذه الشّيفرة تكفي لإضافة التّعليق، لكنّنا بحاجة إلى تعديل دالّة توجيه رابط التّدوينة لإضافة التّعليقات إلى الصّفحة:

```javascript
app.get("/posts/:slug", function(request, response, next) {

    var slug = request.params.slug;
    
    connection.query("SELECT * from `posts` WHERE slug = ?", [ slug ], function(err, rows) {
        if (err || rows.length == 0) return next();
        var post = rows[0];
        connection.query("SELECT * FROM `comments` JOIN `users` ON comments.user_id=users.id WHERE post_id=?", [ post.id ], function(err, comments) {
            if (err) return next(err);
            post.comments = comments;
            response.render("post", { post: post, formatDate: formatDate, user: request.user });
        })
    });

})
```

لنُجرّب أولًا زيارة صفحة التّدوينة http://localhost:3000/posts/hello-world دون تسجيل الدّخول وقبل إضافة أيّ تعليق:

![لا تعليقات، نموذج إضافة التّعليق غير متاح قبل تسجيل الدّخول](comments-none.jpg)

لنقم الآن بتسجيل الدّخول باسم admin وكلمة مرور 123456، ولنعُد للصفحة ونُحدّثها:

![نموذج إضافة التّعليق أصبح متاحًا](comments-signed-in.jpg)

لنجرّب إضافة تعليق:

![أرسلنا أوّل تعليق!](comments-added.jpg)

لنعُد لصفحة التّدوينة ونُعد تحميلها:

![أوّل تعليق!](comments-first-comment.jpg)

رائع جدًا! لقد أنشأنا نظام تعليق بسيطًا بخطوات بسيطة بعد أن أتممنا القسم الأكبر من العمل عندما أنشأنا الجلسات ونظام المستخدمين.

##إتاحة كتابة التّدوينات وتعديلها لمدير المُدوّنة
لنبدأ أوّلاً بصفحة إنشاء تدوينة جديدة، ولنُنشئ رابطًا جديدًا `‎/new` لعرض القالب `views/post-editor.jade`:

```jade
doctype html
html(lang="ar", dir="rtl")
    head
        title تدوينة جديدة
    body
        style
            :css
                body {
                    font-family: Arial, sans-serif;
                }
        
        h1 مُدوّنتي
        hr
        h2 تسجيل الدخول
        form(action="/posts", method="POST")
            label(for="title") عنوان التدوينة: 
            input(type="text", name="title", required)
            br
            label(for="slug") الرابط الفرعي: 
            input(type="text", name="slug" required)
            br
            label(for="body") نص التّدوينة: 
            textarea(name="body")
            br
            input(type="submit", value="أرسل التّدوينة")

```

```javascript
app.get("/new", function(request, response, next) {
    if (request.user && request.user.is_author) {
        response.render("post-editor", { user: request.user });
    } else {
        response.status(403);
        response.send("ليس لديك صلاحيات إضافة تدوينة.");
    }
})

app.post("/posts", parseBody, function(request, response) {
    if (request.user && request.user.is_author) {
        var title = request.body.title,
            body = request.body.body,
            date = new Date(),
            author_id = request.user.id,
            slug = request.body.slug;
            
        connection.query("INSERT INTO `posts` (title, body, date, author_id, slug) VALUES (?, ?, ?, ?, ?)", [ title, body, date, author_id, slug ], function(err) {
            if (err) {
                response.status(500);
                response.send("تعّذرت إضافة التّدوينة");
                return;
            }
            
            response.status(201);
            response.send("أضيفت التّدوينة.");
        })
    } else {
        response.status(403);
        response.send("ليس لديك صلاحيات إضافة تدوينة.");
    }
})
```

الجزء الأكثر أهمّيّة في شفرتنا هو التّحقق من كون المستخدم يحمل صلاحيات الكتابة، فإن لم يكن كذلك، نُرسل الرّمز `‎403 Forbidden` (محظور). بإمكاننا تجاوز التّحقق قبل عرض القالب ونترك عرض الرسالة المناسب للقالب ذاته، لكنّ المهمّ هو إجراء التّحقّق عند إدخال التّدوينة في فاعدة البيانات.

سنتيح تعديل التّدوينة على رابط التّدوينة ذاته متبوعًا بـ`‎/edit`:

```javascript
app.get("/posts/:slug/edit", function(request, response, next) {
    var user_id = request.user.id;
    var slug = request.params.slug;
    
    connection.query("SELECT * FROM `posts` WHERE author_id=? AND slug=?", [ user_id, slug ], function(err, rows) {
        if (!err && rows[0]) {
            response.render("post-editor", { post: post });
        } else {
            response.status("401");
            response.send("إمّا أن التّدوينة غير موجودة، أو أنّك لا تملك الصلاحيات للوصول إليها");
        }        
    })
})

```

الجزء المهمّ من شفرتنا هو اشتراط أن يكون مؤلف التّدوينة المطلوب تعديلها هو صاحب الجلسة ذاته وهو ما كتبناه في استعلام MySQL، وإلّا سيكون بإمكان أن شخص أن يضيف `‎/edit` إلى نهاية التّدوينات ويجري ما يشاء من التغييرات عليها. لنقم بتعديل القالب `post-editor.jade` لجعله يتعامل مع التّدوينات الموجودة مسبقًا بالإضافة إلى التّدوينات الجديدة:

```jade
doctype html
html(lang="ar", dir="rtl")
    head
        title تدوينة جديدة
    body
        style
            :css
                body {
                    font-family: Arial, sans-serif;
                }
        
        h1 مُدوّنتي
        hr
        - var editMode = post && post.id
        h2 #{editMode ? "تعديل التّدوينة" : "تدوينة جديدة" }
        form(action= editMode ? ("/posts/" + post.slug + "?_method=PUT") : "/posts", method="POST")
            label(for="title") عنوان التدوينة: 
            input(type="text", name="title", required, value= editMode ? post.title : "")
            br
            if !editMode
                label(for="slug") الرابط الفرعي: 
                input(type="text", name="slug" required)
                br
            label(for="body") نص التّدوينة: 
            textarea(name="body") #{ editMode ? post.body : "" }
            br
            input(type="submit", value="أرسل التّدوينة")
```

سيُرسل طلب التّعديل باستخدام الفعل `PUT` الّذي يُستخدم للطلب من الخادوم "تحديث" محتوى موجود لديه، خلافًا لـ`POST` المستخدم لإضافة محتوى جديد. المشكلة أنّ المتصفّحات لا تدعم استخدام سوى فعلين ضمن نماذج HTML هما `POST` و`GET`، ولذلك سنضطر إلى إيجاد طريقة "ملتوية" لتجاوز هذه المشكلة. استخدمنا الفعل `POST` ذاته في حالة التّعديل مع إضافة حقل إلى رابط `action` في النّموذج، سيستخدم هذا الحقل من قبل وحدة `method-override` الّتي تقرأه وتغيّر من فعل الطّلب إلى `PUT` ليمرّ عبر دالّة التّوجيه الّتي كتبناها:

```javascript
var methodOverride = require('method-override');
app.use(methodOverride('_method'));

/*
...
*/

app.put("/posts/:slug", parseBody, function(request, response) {
    if (!requestest.user) {
        response.status(403);
        response.send("يجب تسجيل الدخول لتعديل التدوينات.");
        return;
    }

    var slug = request.params.slug;
    var new_title = request.body.title;
    var new_body = request.body.body;
    var user_id = request.user.id;
    
    connection.query("UPDATE `posts` SET title=?, body=? WHERE slug=? AND author_id=?", [ new_title, new_body, slug, user_id ], function(err) {
        if (err) {
            console.log(err);
            response.status("500");
            response.send("حدث خطأ أثناء تعديل التدوينة");
            return;
        }
        
        response.send("حُدِّثت التّدوينة.");
    })
})
```

تأكد من اشتراطك كون مؤلّف التّدوينة هو ذاته صاحب الجلسة مرّة ثانية قبل إدخال البيانات.

قلنا أنّ أفعال HTTP تستخدم استخدامًا دلاليًّأ (semantic) ولا شيء يُجبرك على استخدام `PUT`، بل يمكنك استخدام `POST` للحصول على نفس النّتيجة، لكنّه العرف المتّفق عليه، والذي ستعتاد على اتّباعه عندما تتقدّم في مستويات أعلى كبناء واجهة برمجيّة للمدوّنة (RESTful API) الّتي يتوقّع الطّرف الذي يتعامل معها هذا الأسلوب الدّلاليّ.

يحقّ لنا الاحتفال الآن، فقد أنشأنا مدوّنة حقيقيّة من الصّفر! لنقم الآن بتحسين مظهرها وتنظيف شيفرتنا!

##تنظيف الشّيفرة
حسنًا، قد تبدو شيفرتنا طويلة في الملفّ `index.js` طويلة بعض الشيء وفيها الكثير من التّكرار، وحالما نُشاهد سطورًا مكرّرة في شيفرة برمجيّة، نعلم أنّ بإمكاننا كتابة شيفرة أفضل. لقد أهملنا ذلك قليلًا لنحصل على برنامج يعمل بأسرع وقت ممكن، لكن علينا الآن أن نعود لنلقي نظرة أكثر إمعانًا في برنامجنا.

تكرّر في كثير من المواضع استخدامنا للدّالة `parseBody` لتفسير متن طلبات POST، وحدة `body-parser` هي واحدة من البرامج الوسيطة التي يمكن استعمالها على مستوى التّطبيق أيضًا، لنقم بحذف عبارة `parseBody` من كل طلبات POST ولننقل تفسير متن الطّلب إلى مستوى التّطبيق، ذكرنا أنّه بإمكاننا استخدام `app.use()‎` لذلك:
```javascript
/*
...

*/

var app = express();

var parseBody = bodyParser.urlencoded({ extended: true });

app.use(session({
    secret: "my top secret",
    resave: true,
    saveUninitialized: true
}));

app.use(parseBody);

app.use(cookieParser());

// ...
```

سيكون من المفيد بعد إضافة تدوينة جديدة أو تعديلها أو تعليق جديد على تدوينة العودة مجدًّدا إلى هذه التّدوينة بدل عرض رسالة تفيد بنجاح العمليّة فقط، يوفّر Express الوظيفة `redirect()‎` على الكائن `response` التي تُخبر المتصفّح بالانتقال إلى صفحة أخرى كجواب على الطّلب الّذي أُرسل. سأدع لك تنفيذ هذه المهمّات:

* عند كتابة تدوينة جديدة، انتقل إلى صفحة هذه التّدوينة.
* عند إضافة تعليق جديد، عُد إلى صفحة التّدوينة المعنيّة.
* عند إنشاء مستخدم جديد، انتقل إلى صفحة تسجيل الدّخول.
* عند تسجيل الدّخول، انتقل إلى صفحة الملفّ الشّخصيّ.

في معظم دوالّ التّوجيه التي كتبناها، قمنا بالتّحقّق من الخطأ وإرسال رسالة مناسبة مع رمز حالة مثل `404` و`403`... من الأفضل أن نُصمّم صفحة خطأ خاصّة تتلقّى الخطأ ورسالته وتعرضه للمستخدم بأسلوب موحّد، سنحذف كلّ عبارات `response.send()‎` الّتي ترسل رسالة خطأ ونبدلها بالتّوجيه إلى الدّالة التّالية `next()‎` الّتي ستعرض قالب صفحة الخطأ `views/error.jade`:

```jade
doctype html
html(lang="ar", dir="rtl")
    head
        title مُدوّنتي! - خطأ
    body
        style
            :css
                body {
                    font-family: Arial, sans-serif;
                }
        
        h1 مُدوّنتي
        hr
        h2 خطأ #{ response.statusCode }
        p #{ error.message || error.toString() }
```

هذا مثال عن تعديل دالّة التّوجيه للرّابط `‎/new`:

```javascript
app.get("/new", function(request, response, next) {
    if (request.user && request.user.is_author) {
        response.render("post-editor", { user: request.user });
    } else {
        response.status(403);
        return next(new Error("ليس لديك صلاحيات إضافة تدوينة."));
    }
})
```

تقبل الدّالة `next` معاملاً اختياريًّا يشير إلى وجود خطأ، وهي الطّريقة المناسبة لتمرير الخطأ عبر دوالّ التّوجيه، عدم تمرير الخطأ يعني أنّ التّوجيه يسير من دالّة إلى أخرى بشكل سليم، يستفيد Express من وجود هذا المعامل لعرض الخطأ في حال انتهت عمليّة التّوجيه دون توفير دالّة تتعامل معه. سيلجأ Express إلى الدّالة التّالية الّتي يجب أن نُضيفها إلى نهاية شيفرتنا:

```javascript
app.use(function(err, request, response, next) {
    response.render("error", { error: err, statusCode: response.statusCode })
})
```

لاحظ أنّه خلافًا لدوالّ التّوجيه السّابقة، فقد استخدمنا 4 معاملات، قد تتساءل كيف يمكن لدالّة واحدة أن تتلقّى عددًا مختلفًا من المعاملات وتتصرّف بطريقة مختلفة، أو كيف تعرف الدّالة أن العنصر الأوّل هو كائن الخطأ وليس كائن الطّلب، الجواب هو أنّ Express يُجري تحقّقًا من عدد المعاملات في دالّة التّوجيه ويغيّر تصرّفه، وهذا الأمر متاح لأن JavaScript توفّر الكائن `arguments` بشكل تلقائيّ لكلّ الدّوالّ، والذي يمكن التّحقّق من طوله (`length`) بجملة شرطيّة وتغيير سلوك الدّالة. الهدف النّهائي من هذا أن يكون Express سهل الاستعمال وبديهيًّا، وهذا الأسلوب ستجده كثيرًا في Node.js. من الضّروري استخدام 4 معاملات ليستطيع Express التفريق بين: `err, request, response` و`request, response, next`.

لنجرّب الآن زيارة بعض الصّفحات الّتي نتوقّع حدوث خطأ عندها:

![صفحة إنشاء تدوينة جديدة دون تسجيل الدّخول](cleaning-up-error-403.jpg)

![صفحة تدوينة غير موجودة](cleaning-up-error-404.jpg)

هذا أفضل! توحيد صفحة الخطأ سيجعلنا نفكّر في تقديم حلول لهذا الخطأ بناء على رمز الحالة، مثلاً نستطيع تقديم مربّع بحث في حال كان الرّمز `404` (غير موجود)، أو نستطيع أن نطلب من المستخدم تسجيل الدّخول في حال كان `403`... إلخ.

شيفرة JavaScript نظيفة الآن، لنلقِ نظرةً على القوالب الّتي أنشأناها، يتكرّر في معظمها استخدام ترويسة للصّفحة مع استخدام تنسيق موحّد، قد يبدو تضمين CSS في الصّفحة مقبولًا الآن، لكنّنا سنحتاج إلى نقله إلى ملفّ منفصل عندما نتوسّع في إضافة الأنماط لكي لا نحتاج لتكرارها في كلّ القوالب. لنُنشئ ملفًا للأنماط `style.css` في مجلّد جديد ضمن المشروع نُسمّيه `public`، ولننقل إليه شيفرة CSS من أحد القوالب:

```css
body {
    font-family: Arial, sans-serif;
}
```

لنحذف الآن شيفرة CSS من القوالب ونكتب بدلاً منها رابطًا لملفّنا:

```jade
head
    title إنشاء مستخدم جديد
    link(rel="stylesheet", href="/style.css")
body
    h1 مُدوّنتي
    //- ...
```

حسنًا، لن يعثر الخادوم على الملفّ `style.css` عندما يطلبه المتصفّح، لأنّنا نحتاج لتوفيره صراحةً. من الشّائع استضافة كلّ الملفّات الثّابتة (static) مثل ملفّات CSS وJavaScript للمتصفّح   ضمن مجلّد `public`، ثمّ توفير هذا المجلّد بكامل محتوياته على الخادوم. تتوفّر آليّة مُدمجة في Express للقيام بذلك:

```javascript
app.use(express.static(__dirname + '/public'));
```

تتوفّر أيضًا وحدات خارجيّة يمكنها القيام بالمهمّة ذاتها وبخيارات أكثر مثل تحديد لواحق الملفّات وأذوناتها...

مُلاحظة: المُتغيّر `‎__dirname` توفّره Node.js وهو يشير إلى المجلّد الذي يحوي الملفّ الحاليّ (`index.js`).

سنستفيد من هذا المجلّد في استضافة ملفّات favicon وJavaScript الّتي تعمل في المتصفّح عندما نطوّر مدوّنتنا لتستخدم AJAX.

##تحسين مظهر المدوّنة
سنحتاج إلى إجراء تغييرات في القوالب كإضافة بعض المُعرّفات (IDs) والأصناف (classess) لنقوم بتنسيقها وفق القواعد الّتي نكتبها في ملفّ `style.css` الّذي أنشأناه للتّوّ. يوفّر Express صياغتين مختصرتين للتّعبير عن الأصناف والمُعرّفات لكونهما شديدتي الشّيوع، لإضافة صنفين ومُعرّف على عنصر `div` ما، يمكن كتابة:

```jade
#comments.post-comments.card
```

وهي مكافئة لكتابة:

```jade
div(class="post-comments card", id="comments")
```

والتي ستُترجم إلى HTML الّتالي:

```html
<div class="post-comments card" id="comments"></div>
```

لاحظ أنّك لست بحاجة لكتابة `div`، لأنّ Jade يفهم المغزى من هذا الأسلوب على أنّه كائن `div` تلقائيًّا، لأنّه الكائن الأكثر استخدامًا لإضافة الأصناف والمعرّفات بهدف تنسيق مكوّنات الصّفحة.

قد تلاحظ أثناء العمل الحاجة لتكرار أجزاء معيّنة من الشّيفرة في كلّ القوالب مثل إظهار عنوان المدوّنة وروابطها على المواقع الاجتماعيّة ومربّع البحث ضمن ترويسة (header) في كلّ الصّفحات، مع تذييل (footer) يحوي بعض الرّوابط الإضافيّة في نهاية كلّ صفحة، يمكنك التّخلصّ من عناء التّكرار باستخدام الكلمة المفتاحية `extends` لبناء قالب على قالب آخر، فلنقم ببناء قالب يتضمّن الهيكل العامّ لكلّ الصّفحات، ولنسمّه `_layout.jade` (اجعل اسمّ هذه الملفّات يبدأ بالرّمز `_` لتستطيع فيما بعد تمييزها بسرعة بين ملفّات القوالب):

```jade
doctype html
html(lang="ar", dir="rtl")
    head
        title مُدوّنتي!
        link(rel="stylesheet", href="/style.css")
    body
        header
            h1#blog-title مُدوّنتي
            ul#blog-nav
                li: a(href="/") الرئيسية
                li: a(href="/login") تسجيل الدخول
                li: a(href="/signup") إنشاء حساب
        block content
        footer
            hr
            p جميع الحقوق غير محفوظة
```
العبارة `block` متبوعةً باسم نختاره نحن كما نشاء، تسمح لنا ببناء قوالب تشترك جميعها في الهيكل العّام لهذا الملفّ وتختلف فقط في هذا الجزء، مثلاً يمكننا الآن إعادة كتابة الصّفحة الرئيسيّة (`home.jade`) لتصبح:

```jade
extends _layout
block content
    for post in posts
        .post
            h2.post-title #{ post.title }
            p.post-body #{ post.body }
            small.post-date كُتِبَت #{ formatDate(post.date) }
```

سيبحث Jade عن القطعة المُسمّاة `content` ويضيفها في المكان المناسب للقالب. لا داع لإرفاق لاحقة الملفّ فهي معروفة بالنّسبة لـJade.

ملاحظة: التّعبير `li: a(href=...` هو اختصار تتيحه Jade للاستغناء عن الحاجة لكتابة الوسمين على سطرين.

كثيرًا ما تحتاج إلى إدخال أجزاء  متكرّرة من HTML مع إجراء بعض التّعديلات عليها، وهذا ما يمكن تنفيذه من خلال الدّوالّ في Jade والّتي تُسمّى mixins، وهي تشبه كثيرًا الدّوال في أي لغة برمجة، لتوضيح المفهوم أكثر، لنفترض أنّنا نريد توحيد مظهر التّدوينات بين صفحة التّدوينة والصّفحة الرئيسيّة، مع فارق بسيط هو جعل نصّ التّدوينة في الصّفحة الرئيسية محدودًا بمئتي حرف مثلاً، يمكن فعل هذا بنقل شيفرة التّدوينة المفردة إلى دالّة في ملفّ منفصل نُسمّيه `_mixins.jade`:

```jade
mixin post(post, full)
    h2.post-title: a(href="/posts/" + post.slug) #{ post.title }
    p.post-body #{ full ? post.body : (post.body.substr(0, 199) + "...") }
    small.post-date كُتِبَت #{ formatDate(post.date) }
```

لاحظ كم تشبه هذه الصّياغة صياغة الدّوالّ في لغات البرمجة، حيث يمكن إمرار مُعاملات لها بين قوسين. يمكن استدعاءها في قوالبنا بالرّمز `+` بعد تضمين الملفّ `_mixins.jade` بالكلمة المفتاحية `include` الّتي تشبه استدعاء وحدة خارجية بـ`require` في Node.js:

```jade
extends _layout
include _mixins
block content
    for post in posts
        .post
            +post(post, false)
```

سأقوم الآن بتنسيق المُدوّنة بأسلوبي الخاصّ، وسأترك المجال لك لتفعل الأمر ذاته! إذا أردت استلهام بعض الأفكار، أنصحك بالاطّلاع على مواقع مثل [Codrops](http://tympanus.net/codrops/).

في الدّرس القادم سنطّلع على بعض المواضيع الّتي يجب أخذها بالحُسبان قبل نشر المدوّنة على الويب.
