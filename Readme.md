<div dir="rtl">

# چک‌لیست امنیتی API
چک‌لیستی عملیاتی از مهم‌ترین کارهایی که باید برای حفظ امنیت در زمان طراحی، توسعه، تست و انتشار API انجام شود.

> **هدف:** این سند مثل یک تخته‌کار (Kanban) عمل می‌کند؛ تسک‌های امنیتی را به شکل چک‌باکس کنار Featureها/اسپرینت‌ها بچسبانید تا فراموش نشوند.

---

## فهرست مطالب
- [نحوه استفاده](#نحوه-استفاده)
- [چک‌لیست سریع](#چک-لیست-سریع)
- [احراز هویت](#احراز-هویت)
- [دسترسی](#دسترسی)
- [ورودی](#ورودی)
- [پردازش](#پردازش)
- [خروجی](#خروجی)
- [ذخیره‌سازی و داده‌ها](#ذخیره-سازی-و-داده‌ها)
- [پایش، مشاهده‌پذیری و پاسخ‌گویی](#پایش-مشاهده‌پذیری-و-پاسخ-گویی)
- [مدیریت خطا و نسخه‌بندی](#مدیریت-خطا-و-نسخه-بندی)
- [CI & CD](#ci--cd)
- [منابع پیشنهادی بیشتر](#منابع-پیشنهادی-بیشتر)
- [مشارکت](#مشارکت)

---

## نحوه استفاده
- قبل از هر انتشار، هر دسته‌بندی را مرور کنید و با تیک زدن، وضعیت امنیتی را شفاف کنید.
- اگر یک مورد در پروژه کاربرد ندارد، دلیل عدم انطباق را مستند کنید تا در ممیزی بعدی قابل دفاع باشد.
- مواردی که نیاز به Action دارند را به بک‌لاگ منتقل کنید و مالک/موعد مشخص کنید.

## چک‌لیست سریع
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Dev/Test و Prod را کامل از هم جدا کرده‌اید (محیط، داده، حساب‌ها).
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Secrets داخل کد، ایمیل یا پیام‌رسان نگه‌داری نمی‌شود و چرخش دوره‌ای دارند.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;هیچ Endpoint بدون Rate Limit عمومی نشده است.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;خطاها و رویدادهای امنیتی مانیتور می‌شوند و Alert عملی دارند.

---

## احراز هویت
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;از `Basic Auth` یا همان `اصالت‌سنجی برای دسترسی‌های اولیه` استفاده نکنید. به جای آن از روش‌های استاندارد احراز هویت استفاده کنید (مثلا [JWT](https://jwt.io/) یا [OAuth](https://oauth.net/)).
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;برای کارهایی مثل `احراز هویت`، `تولید توکن` و `ذخیره پسوورد` چرخ را دوباره اختراع نکنید. از استانداردها استفاده کنید.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;برای لاگین محدودیت‌های `تعداد ماکسیمم تلاش مجدد`  و تعداد دفعات ورود را قرار بدید.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;همه‌ی داده‌های حساس را رمزگذاری کنید.

### JWT (JSON Web Token)
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;از یک کلید پیچیده‌ی تصادفی برای `JWT Secret` استفاده کنید تا حمله‌ی بروت‌فورس به توکن بسیار سخت باشد.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;الگوریتم را از هدر استخراج نکنید. در بک‌اند الگوریتم را تحمیل کنید (`HS256` یا `RS256`).
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;انقضای توکن (`TTL` یا `RTTL`) را تا حد ممکن کوتاه کن.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;اطلاعات حساس را در پی‌لود JWT ذخیره نکنید چون [به راحتی](https://jwt.io/#debugger-io) قابل رمزگشایی است.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;از ذخیره بیش از حد داده ها خودداری کنید. JWT معمولاً در هدر به اشتراک گذاشته می شود و محدودیت اندازه دارند.

### OAuth
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;همیشه `redirect_uri` را در سمت سرور اعتبارسنجی کنید تا تنها به URLهای مجاز اجازه داده شود.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;همیشه تلاش کنید تا code را به جای token تبادل کنید (اجازه `response_type=token` را ندهید).
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;از پارامتر `state` با یک هش تصادفی استفاده کنید تا از CSRF روی پروسه‌ی احراز هویت OAuth جلوگیری کنید.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;مقدار scope پیش‌فرض را تعریف کنید و پارامترهای scope را برای هر اپلیکیشن اعتبارسنجی کنید.

## دسترسی
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;رکوئست‌ها را محدود کنید (Throttling) تا از حملات DDos یا بروت‌فورس جلوگیری شود.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;در سمت سرور از HTTPS استفاده کنید تا از حملات مرد میانی جلوگیری شود.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;از هدر `HSTS` استفاده کنید تا از حمله‌ی SSL Strip جلوگیری شود.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;لیست های دایرکتوری را خاموش کنید.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;برای APIهای خصوصی، فقط از IPها/میزبانهای لیست سفید اجازه دسترسی داشته باشید.

## ورودی
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;از متد HTTP مناسب با توجه به نوع عملیات استفاده کنید: `GET` برای خواندن، `POST` برای ایجاد کردن، `PUT/PATCH` برای جایگزین یا بروزرسانی و `DELETE` برای حذف یک رکورد، و در صورتی‌که متد درخواستی برای منبع درخواست‌شده مناسب نباشد با `405 Method Not Allowed` پاسخ بدهید.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;مقدار `content-type` را در هدر Accept رکوئست (مذاکره محتوا یا Content Negotiation) اعتبارسنجی کنید تا فقط به فرمت‌های مورد پشتیبانی اجازه داده شود (مثلا `application/xml`، `application/json` و ...). و در صورت عدم تطابق با یک پاسخ `406 Not Acceptable` پاسخ دهید.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;مقدار `content-type` در داده‌ی پست‌شده را اعتبارسنجی کنید (مثلا `application/x-www-form-urlencoded`، `multipart/form-data`، `application/json` و ...).
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ورودی کاربر را اعتبارسنجی کنید تا از آسیب‌پذیری‌های معمول جلوگیری شود (مثلا `XSS`، `SQL-Injection` و `Remote Code Execution`). 
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;هیچ داده‌ی حساسی مثل (داده‌های اعتبارسنجی، پسوورد‌ها، توکن‌های امنیتی یا کلید‌های API) را داخل URL قرار ندهید و از هدر Authorization استاندارد استفاده کنید.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;فقط از رمزگذاری سمت سرور استفاده کنید.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;از یک سرویس API Gateway استفاده کنید تا کش‌کردن و سیاست‌های Rate Limit (مثلا `Quota`، `Spike Arrest` یا `Concurrent Rate Limit`) فعال شوند و منابع APIها را به صورت داینامیک دپلوی کنید.

## پردازش
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;چک کنید که تمامی endpointها توسط احراز هویت محافظت شوند تا از پروسه‌ی احراز هویت ناقص جلوگیری شود.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;از استفاده از ID ریسورس خود کاربر اجتناب کنید. به جای `user/654321/orders` از `/me/orders` استفاده کنید.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;از IDهای auto-increment استفاده نکنید. به جای آن از `UUID` استفاده کنید.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;اگر فایل‌های XML را parse می‌کنید مطمئن شوید تا entity parsing غیرفعال باشد تا از `XXE` (XML External entity attack) جلوگیری شود.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;اگر فایل‌های XML، YAML یا هر زبان دیگری را با استفاده از anchor ها و ref ها parse می‌کنید، مطمئن شوید تا entity expansion غیرفعال باشد تا از `Billion Laughs/XML bomb` توسط exponential entity expansion attack جلوگیری شود.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;از یک CDN برای آپلودهای فایل استفاده کنید.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;اگر با مقادیر بسیار حجیمی از داده سر و کار دارید، از Workerها و Queueها استفاده کنید تا حد الامکان پردازش در بک‌گراند انجام شود و سریع پاسخ را برگردانید تا از HTTP Blocking جلوگیری شود.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;خاموش کردن حالت DEBUG را فراموش نکنید.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;در صورت وجود از پشته های غیر قابل اجرا استفاده کنید.

## خروجی
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;هدر `X-Content-Type-Options: nosniff` را ارسال کنید.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;هدر `X-Frame-Options: deny` را ارسال کنید.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;هدر `'Content-Security-Policy: default-src 'none` را ارسال کنید.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;هدرهایی که به نوعی اثرانگشت برجای می‌گذارند را حذف کنید، مثلا `X-Powered-By`، `Server` و ‍`X-AspNet-Version`.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;مقدار `content-type` را برای پاسخ اجباری کنید. اگر `application/json` برمیگردانید، پس `content-type` پاسخ، `application/json` است.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;اطلاعات حساس مثل `داده‌های اعتبارسنجی`، `رمز های عبور` و `توکن‌های امنیتی` را برنگردانید.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;با توجه به عملیات انجام‌شده، status code مناسب را برگردانِد. مثلا `200 OK`، `400 Bad Request`، `401 Unauthorized` و `405 Method Not Allowed`.

## ذخیره‌سازی و داده‌ها
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;داده‌های در حال استراحت (Data at Rest) را با AES-256 یا معادل آن رمزگذاری کنید و کلیدها را در KMS نگه دارید.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Backupها را رمزگذاری کنید و دسترسی‌شان را محدود کنید؛ تست بازیابی (Restore) دوره‌ای انجام دهید.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;داده‌های حساس را حتی در محیط‌های Log و Analytics ماسک/توکنایز کنید.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Retention Policy مشخص کنید و داده‌هایی که نیاز نیست را حذف نمایید (Data Minimization).

## پایش، مشاهده‌پذیری و پاسخ‌گویی
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;لاگ‌های امنیتی را ساختاری ثبت کنید (الگو، Correlation ID، User ID و ...).
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Alert برای رخدادهای حساس (Rate spike، خطای احراز هویت، تغییر تنظیمات) بسازید.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Trace توزیع‌شده برای مسیر درخواست‌های حساس فعال باشد تا حملات پیچیده قابل ردیابی باشد.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;سناریوهای پاسخ به حادثه (IR Playbook) را به همراه چک‌لیست تمرین کنید.

## مدیریت خطا و نسخه‌بندی
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;پیام خطا هیچ‌وقت Stack Trace، Query یا Secret را نمایش ندهد؛ فقط کد خطا و شناسه پیگیری.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;API Versioning واضح باشد و نسخه‌های منسوخ شده برنامه خروج (Sunset) داشته باشند.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Schemaها (OpenAPI/AsyncAPI) به‌روز و با تست Contract اعتبارسنجی می‌شوند.

## CI & CD
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;طراحی و پیاده سازی خودتان را با پوشش تست‌های unit/integration بازرسی کنید.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;از یک پروسه‌ی مرور کد استفاده کنید و خود-تاییدی را نادیده بگیرید.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;مطمئن شوید تا تمامی اجزای سرویس‌هایتان، شامل کتابخانه‌های استفاده‌شده و دیگر وابستگی‌ها، قبل از انتشار در حالت production، به طور ایستا توسط نرم‌افزارهای آنتی‌ویروس اسکن شده‌اند.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;به صورت پیوسته روی کدتان تست‌های امنیتی (آنالیز ایستا و پویا)، اجرا کنید.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;وابستگی‌هایتان (نرم افزار و سیستم عامل، هردو) را برای آسیب‌پذیری‌های شناخته شده، چک کنید.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;برای دپلوی‌هایتان، یک راه‌حل با قابلیت عقبگرد (rollback) طراحی کنید.
- [ ] &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Pipeline CI/CD باید Secrets را از Vault بخواند و Artifactها را امضاء (Sigstore، Cosign) کند.

---

## منابع پیشنهادی بیشتر
- [yosriady/api-development-tools](https://github.com/yosriady/api-development-tools) - یک مجموعه از منابع مفید برای ساختن APIهای RESTful با HTTP و JSON -
- [API-Security-Checklist on GitHub](https://github.com/MrAlfak/API-Security-Checklist) - نسخه آنلاین و به‌روز همین سند.

---

## مشارکت
برای همکاری و کمک می‌توانید به راحتی این مخزن را fork کنید، تغییرات مورد نظرت را اعمال کنید و یک pull request ثب کنید. اگر سوالی داشتید به آدرس `info@zahedalfak.ir` ایمیل بزنید. برای جزئیات بیشتر [CONTRIBUTING.md](./CONTRIBUTING.md) و [SECURITY.md](./SECURITY.md) را ببینید.
</div>
