# التقرير الفني والهندسي الشامل — متصفح Strix Browser (الإصدار v0.1.0 - x64)

**اسم النظام:** Strix Browser — High-Performance Surveillance & Enterprise Web Browser  
**الملكية والتطوير:** SalemTechno | م. محمود سالم (Eng. Mahmoud Salem)  
**التواصل والموقع الرسمي:** 01000920353 | www.salemtechno.com  
**المعمارية وبيئة التشغيل:** Windows 64-bit (win-x64) | .NET 8.0 WPF (Self-Contained) | Chromium CefSharp 131.3.50  

---

## 1. نظرة عامة على الهندسة المعمارية (System Architecture)
تم تصميم وبناء متصفح **Strix Browser** كمنصة تصفح مكتبية فائقة السرعة ومستقلة بذاتها (Self-Contained)، تجمع بين قوة محرك **Chromium 131** وأداء تطبيقات سطح المكتب الأصلية المبنية على **.NET 8.0 WPF**، مع تخصيص هندسي دقيق لخدمة ثلاثة قطاعات رئيسية:
1. **أنظمة المراقبة والأمن (NVR / DVR / IP Cameras / CCTV):** التهيئة التلقائية الصامتة لأجهزة `Dahua / Hikvision / Uniview / Tiandy` وتشغيل البث المباشر على مدار الساعة (24/7) بدعم عتادي كامل لترميز `H.264 / H.265 (HEVC)` و `WebRTC / HTML5 / MSE`.
2. **التصفح المؤسسي واليومي الفائق السرعة:** إقلاع فوري، خاصية `Keep Alive Mode` لكل تبويب، موفر ذكي للذاكرة (`Memory Saver`)، واستعادة تلقائية للجلسات بعد الإغلاق المفاجئ (`Session Restore`).
3. **الأمان والتشخيص وإدارة التحميلات المباشرة:** تشخيص شبكات مدمج (`Network Doctor`)، خزنة كلمات مرور مشفرة (`DPAPI + Windows Admin UAC`)، استيراد من Chrome و Edge، تصفح خفي معزول، ومدير تحميلات متعدد القنوات للملفات المباشرة (`Direct HTTP/HTTPS Downloads`).

---

## 2. ما تم إنجازه برمجياً بالتفصيل (Implemented Modules & Technical Features)

### أولاً: نواة المتصفح، الأداء الفائق، والإقلاع الفوري (Core Engine & Instant Startup)
- **حزمة مستقلة بالكامل (100% Self-Contained Deployment):**
  - دمج كافة مكتبات `.NET 8.0 Runtime` ومكتبات `Chromium (libcef.dll)` ومكتبات `Visual C++ 140 Runtime (msvcp140.dll, msvcp140_atomic_wait.dll, vcruntime140.dll, vcruntime140_1.dll)` داخل مجلد التطبيق مباشرةً.
  - توليد وتجهيز ملف الاعتمادية `CefSharp.BrowserSubprocess.deps.json` برمجياً لضمان عمل عمليات العرض والشبكة الفرعية على أي نسخة ويندوز خام بدون الحاجة لتثبيت أي حزم خارجية.
- **تهيئة مسارات التحميل والعمليات (`App.OnStartup`):**
  - ضبط `SetDllDirectory(AppContext.BaseDirectory)` و `Environment.CurrentDirectory` في اللحظة الأولى للإقلاع لضمان عمل المتصفح من أي اختصار أو مسار.
  - نظام **Single-Instance الذكي** عبر `OS Mutex` و `EnumWindows` (`FindVisibleStrixWindow`) الذي يتحقق من وجود نافذة مرئية حالية ليضعها في المقدمة، أو ينهي أي عمليات خلفية معلقة فوراً ليفتح المتصفح خلال ثانية واحدة.

---

### ثانياً: نظام التحديث اللحظي للمواقع ومنع الكاش القديم (Startup Cache Hygiene & Hard Reload)
- **التنظيف الانتقائي الذكي عند الإقلاع (`PurgeStaleHttpCacheOnStartup`):**
  - مسح تلقائي لملفات الكاش المؤقتة (`Cache_Data`, `Code Cache`, `Service Worker CacheStorage & ScriptCache`) عند بدء التشغيل، مع **الحفاظ التام بنسبة 100%** على ملفات تعريف الارتباط (`Cookies`)، جلسات تسجيل الدخول، `Local Storage`، و `IndexedDB`.
- **التحديث المباشر من السيرفر (`Reload(ignoreCache: true)`):**
  - عند الضغط على زر التحديث أو `F5` / `Ctrl+R`، يتم تجاوز الكاش المحلي وتنظيف `caches` في `Service Worker` لعرض أحدث التعديلات المرفوعة على المواقع فوراً.

---

### ثالثاً: مدير التحميلات المباشرة متعدد القنوات (`DownloadsService` & `DownloadsPanel`)
- **دعم التحميل المباشر للملفات المصرح بها (`Direct HTTP/HTTPS File Downloads`):**
  - تحميل الملفات المباشرة (برامج، أرشيفات، تحديثات أجهزة، مستندات) مع دعم تعدد القنوات (`Multi-Connection HTTP Range`)، إيقاف مؤقت (`Pause`)، استئناف (`Resume`)، إلغاء (`Cancel`)، إعادة المحاولة (`Retry`)، فتح المجلد (`Open Folder`)، وحساب السرعة والوقت المتبقي (`ETA`).

---

### رابعاً: محرك استيراد كلمات المرور والسجل والمفضلة من Chrome و Edge (`ChromeImportService`)
- **دعم كافة البروفايلات (`Multi-Profile Discovery`):**
  - فحص جميع البروفايلات (`Default`, `Profile 1`, `Profile 2`, ...) في كلٍ من **Google Chrome** و **Microsoft Edge** (قراءة فقط دون المساس بملفات المتصفحات الأصلية).
- **النسخ الآمن لقواعد البيانات المفتوحة (`Unlocked SQLite + WAL/SHM Snapshot`):**
  - نسخ قواعد بيانات SQLite (`Login Data`, `History`, `Bookmarks`) مع ملفات المعاملات الحية (`-wal` و `-shm`) باستخدام `FileShare.ReadWrite | FileShare.Delete` لضمان سحب أحدث المواقع وكلمات المرور حتى أثناء فتح متصفح Chrome أو Edge.
- **فك التشفير المتقدم لكلمات المرور (`DPAPI + AES-256-GCM`) وحماية الخزنة (`PasswordVaultService`):**
  - استخراج المفتاح الرئيسي (`encrypted_key`) من ملف `Local State` وفك تشفيره عبر `Windows DPAPI`، ثم فك تشفير كلمات المرور المشفرة بـ `AES-256-GCM`، مع دعم استيراد ملفات CSV.
  - تشفير كلمات المرور المحفوظة محلياً عبر `DPAPI`، واشتراط صلاحيات مسؤول ويندوز (`Windows Admin UAC`) لإظهار أو تصدير كلمات المرور من الإعدادات.

---

### خامساً: التصفح الخفي الاحترافي (`Incognito Mode`)
- **فتح تبويب خفي من قائمة النقاط الثلاث (`⋮ → New Incognito Tab`) أو `Ctrl+Shift+N`:**
  - يعمل ببيئة ذاكرة معزولة (`CachePath = ""`, `PersistSessionCookies = false`)، لا يحفظ سجل التصفح أو كلمات المرور أو الجلسات، ويمسح كافة بيانات الجلسة فور إغلاقه.

---

### سادساً: طبيب الشبكة والتشخيص المتقدم (`Network Doctor & StrixNetworkObserver`)
- **تشخيص فوري لمراحل الاتصال (`DNS → TCP → TLS → HTTP`):**
  - يفتح عبر قائمة `⋮` أو الاختصار `Ctrl+Shift+D` (بدون زر دائم في شريط الأدوات الرئيسي).
  - يعرض لكل طلب: `Domain | IP | Port | StoppedAt | Error` مع إمكانية نسخ التقرير (`Copy report`) لفريق الشبكات، وبواجهة داكنة عالية التباين.

---

### سابعاً: التهيئة التلقائية الصامتة لأنظمة المراقبة وإدارة الذاكرة (`Surveillance Assist, Keep Alive Mode & Memory Saver`)
- **دعم تلقائي صامت لأجهزة `Dahua / Hikvision / Uniview / Tiandy`:**
  - عند اكتشاف واجهة جهاز تسجيل أو كاميرا، يقوم المتصفح تلقائياً وبصمت بتفعيل `Keep Alive Mode` للتبويب، وتفضيل مسار `HTML5 / WebRTC / MSE`، وإلغاء تحميل ملفات الإضافات القديمة (`.cab` / `.ocx`)، وعرض `Surveillance ready` في شريط الحالة دون إزعاج المستخدم بنوافذ إجبارية (مع توفير `Open in Edge IE mode` كخيار احتياطي هادئ في قائمة `⋮ → More tools`).
- **Keep Alive Mode & Memory Saver:**
  - مفتاح `Keep Alive Mode` في شريط الأدوات يطبق على التبويب الحالي فقط لمنع تفريغه من الذاكرة وإبقاء الصوت والإشعارات و `WebRTC / WebSockets` نشطة، بينما يقوم `Memory Saver` بترشيد استهلاك الرام للتبويبات الخاملة الأخرى.

---

### ثامناً: الشريط السفلي المعتمد ومعالج التنصيب (`Locked Footer & Inno Setup Wizard`)
- **الشريط السفلي المعتمد (`Locked Footer`):**
  - يعرض النص المعتمد: `Developed by SalemTechno | 01000920353 | www.salemtechno.com` (مع وجود بيانات المطور التفصيلية `Eng. Mahmoud Salem` داخل صفحة `About`).
- **معالج التنصيب الاحترافي (`StrixBrowser.iss` — Inno Setup 6):**
  - تصميم `Wizard` منسق واحترافي يعرض داخل الـ Wizard (عند وجود نسخة سابقة) خيار التحديث المباشر (`Overwrite / Update Existing Files`) أو المسح النظيف (`Clean Install`)، مع تشغيل تلقائي مضمون بعد انتهاء التثبيت.
