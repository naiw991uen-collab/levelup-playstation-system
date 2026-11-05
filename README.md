# LEVEL UP · الفرونت اند الثابت لسيستم محل البلايستيشن

صفحات HTML ثابتة (عربي، RTL) لسيستم إدارة محل بلايستيشن: غرف بتلعب سنجل أو ملتي بسعر بالساعة، ومشروبات على الجلسة،
وحساب في الآخر. الصفحات هتتحول لـ Views في **ASP.NET Core MVC (.NET 9)**.

- المرجع للشكل والنصوص: الماكيت اللي العميل وافق عليه (`PlayStation-Demo-Pro.html`).
- Bootstrap 5 RTL + `css/style.css` (الـ tokens والثيم الداكن والفاتح منقولين من الماكيت).
- الخطوط: IBM Plex Sans Arabic للنص، و Readex Pro للعناوين والأرقام.
- البيانات اللي في الصفحات تجريبية وثابتة، ومتسقة مع بعضها في كل الصفحات.
  "دلوقتي" في البيانات هو الخميس 5 نوفمبر 2026 الساعة 4:20 م، ويوم العمل بيبدأ 6 الصبح.

## شجرة الملفات

```
PlayStationFrontend/
├── index.html                  (بتودّي على Account/Login.html)
├── README.md
├── Account/Login.html
├── Dashboard/Single.html       (لوحة السنجل + فورم تعديل سعر الساعة)
├── Dashboard/Multi.html        (لوحة الملتي + فورم تعديل سعر الساعة)
├── Device/Index.html           (التابات + كروت كل الغرف)
├── Session/Index.html          (الجلسات المنتهية + الفلاتر)
├── Drink/Index.html            (فورم إضافة مشروب + الجدول)
├── Room/Index.html             (فورم إضافة غرفة + الجدول)
├── css/bootstrap.rtl.min.css
├── css/style.css
├── js/bootstrap.bundle.min.js
├── js/jquery.min.js
├── js/jquery.validate.min.js
├── js/jquery.validate.unobtrusive.min.js
├── js/theme.js
├── js/timer.js
├── images/logo.svg
└── images/favicon.svg
```

## إزاي تفتح الصفحات

افتح `index.html` في المتصفح مباشرة (دبل كليك)، ومش محتاج سيرفر. هيوديك على صفحة الدخول.
كل اللينكات والفورمز ليها `href` أو `action` نسبي، فتقدر تتنقل بين الصفحات وهي ثابتة.
الفورمز `post` بترجع لنفس الصفحة أو للصفحة اللي بعدها، والـ Login بيودّي على لوحة السنجل.

الخطوط بتتحمل من Google Fonts. من غير إنترنت الصفحات بتشتغل عادي، بس بخط النظام.

> **وقت التسطيب عند العميل:** الخطين (IBM Plex Sans Arabic و Readex Pro) لازم يتنزلوا جوه `wwwroot/fonts`
> ويتعرّفوا بـ `@font-face` في `style.css` بدل الـ `@import` بتاع Google Fonts، عشان الجهاز ممكن يبقى من غير نت.

## الصفحات والفورمز

كل الفورمز `method="post"` إلا فلتر الجلسات (`get`). الحقول مكتوبة بنفس أسماء الـ Properties المتوقعة في الـ ViewModel.
قيم النمط في كل مكان `Single` و `Multi`.

| الصفحة | Controller / Action (GET) | الفورم ← Action | الحقول (`name`) |
|---|---|---|---|
| `index.html` | `Home/Index` (Redirect لـ `Dashboard/Single`، والـ Authorization بتودّي اللي مش عامل دخول على `Account/Login`) | — | — |
| `Account/Login.html` | `Account/Login` | دخول ← `Account/Login` | `UserName`, `Password`, `RememberMe` |
| السايدبار (كل الصفحات) | — | خروج ← `Account/Logout` | — |
| `Dashboard/Single.html` | `Dashboard/Single` | سعر الساعة ← `Dashboard/UpdateRate` | `Mode` (hidden = `Single`), `HourlyRate` |
| `Dashboard/Multi.html` | `Dashboard/Multi` | سعر الساعة ← `Dashboard/UpdateRate` | `Mode` (hidden = `Multi`), `HourlyRate` |
| `Device/Index.html` | `Device/Index?filter=All\|Running\|Idle\|Single\|Multi` | بدء ← `Device/Start` | `RoomId`, `Mode` (الـ select مربوط بالفورم بـ `form="start-{id}"`) |
| | | إضافة مشروب ← `Device/AddDrink` | `RoomId`, `DrinkId` |
| | | شيل مشروب ← `Device/RemoveDrink` | `RoomId`, `DrinkId` |
| | | تحويل ← `Device/SwitchMode` | `RoomId`, `Mode` (النمط الجديد) |
| | | إيقاف ← `Device/Stop` | `RoomId` |
| | | رجوع للعب ← `Device/Resume` | `RoomId` |
| | | تم التحصيل · إنهاء الجلسة ← `Device/Close` | `RoomId` |
| `Session/Index.html` | `Session/Index` | الفلتر (GET) ← `Session/Index` | `Date` (yyyy-MM-dd، يوم العمل)، `Mode` (`All\|Single\|Multi\|Mixed`)، `RoomId` (فاضي = كل الغرف) |
| `Drink/Index.html` | `Drink/Index?editId={id}` | إضافة ← `Drink/Add` | `Name`, `Price` |
| | | حفظ السعر (صف التعديل) ← `Drink/UpdatePrice` | `Id`, `Price` |
| | | مش موجود حالياً / رجّعه متوفر ← `Drink/ToggleAvailable` | `Id` |
| `Room/Index.html` | `Room/Index?editId={id}` | إضافة ← `Room/Add` | `Name`, `DefaultMode` |
| | | حفظ الاسم (صف التعديل) ← `Room/UpdateName` | `Id`, `Name` |
| | | حفظ النمط الافتراضي (في كل صف) ← `Room/UpdateDefaultMode` | `Id`, `DefaultMode` |
| | | تعطيل / تفعيل الغرفة ← `Room/ToggleActive` | `Id` |

ملاحظات للـ ViewModels:
- مفيش حذف في أي مكان. المشروب بيبقى "مش موجود حالياً"، والغرفة بتتعطل. مينفعش تعطّل غرفة فيها جلسة شغالة أو مستنية الحساب،
  والزرار بيبقى `disabled` وجنبه "فيها جلسة شغالة".
- الجلسة بتتكون من أجزاء (Segments)، وكل جزء ليه نمط وسعر ساعة ووقت بداية ونهاية. التحويل بيقفل الجزء الحالي ويبدأ جزء جديد.
  سعر اللعب بيتحسب لكل جزء بالدقيقة (تقريب لفوق): `round(ceil(ثواني / 60) × سعر الساعة / 60)`.
- اللوحات بتحسب وقت النمط بتاعها بس من كل جزء، والمشروبات بتتحسب على النمط اللي الجلسة بدأت بيه.
- رسايل الـ validation موجودة كـ `data-val-*` على الحقول، والأماكن بتاعتها `asp-validation-for`.
- رسايل بعد العمليات (TempData) متكتوبة كـ comments جوه كل صفحة بالنص المطلوب.

## خطوات التحويل لـ MVC

1. **الملفات الثابتة لـ `wwwroot`**: `css/` و `js/` و `images/` تتنقل زي ما هي لـ `wwwroot/css` و `wwwroot/js` و `wwwroot/images`،
   والمسارات تبقى `~/css/style.css` وهكذا.
2. **`Views/Shared/_Layout.cshtml`**: الـ `<head>` كله يتنقل هنا: الـ meta، و `<title>@ViewData["Title"] · LEVEL UP</title>`،
   و favicon، و Bootstrap و `style.css`، و `theme.js` (لازم يفضل في الـ head من غير defer).
   السايدبار يتنقل كمان (من أول `<nav class="sidebar">` لحد نهايته، ومعلّم بـ comment في كل صفحة)،
   والسكريبتات اللي في آخر الـ body (jquery و validate و bootstrap) و `@RenderSection("Scripts", required: false)`.
   - اللينك المتعلّم في السايدبار (`on` و `aria-current="page"`، و `on multi` للوحة الملتي) بيتحدد على حسب الـ Controller والـ Action الحاليين.
   - عدد "N شغال" جنب "الأجهزة" بييجي من السيرفر، ومبيظهرش لو صفر.
   - اسم المستخدم وأول حرف منه في `.who` بييجوا من `User.Identity.Name`.
   - صفحة الدخول مفيهاش سايدبار، فتستخدم layout تاني أو `Layout = null`.
3. **المحتوى المتغير**: اللي جوه `<main class="main">` بس هو اللي يروح لكل View.
4. **`asp-*`**: شيل `href` و `action` الثابتين وسيب `asp-controller` و `asp-action` و `asp-route-*`، والـ Tag Helpers هتولّد المسارات.
   ضيف `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` في `_ViewImports.cshtml`.
   حقول الفورمز ممكن تفضل HTML عادي بنفس الـ `name`، أو تتحول لـ `asp-for`.
5. **`Views/Device/_DeviceCard.cshtml`**: كل `<article class="device-card">` في صفحة الأجهزة بيبقى Partial واحد بياخد الغرفة وحالتها.
   فيه 3 حالات بتتحدد بالـ class:
   - `idle` (فاضي): "هيلعب إيه؟" والعداد 00:00:00 و"المشروبات (قبل البدء)" وزرار "بدء سنجل/ملتي".
     الكلام مرسوم على حسب النمط الافتراضي للغرفة.
   - `running` (شغال): علامة النمط و"شغال"، والعداد وسعر اللعب، وسطر الأجزاء لو متحول، والمشروبات، و"تحويل لـ…" و"إيقاف".
   - `bill` (في انتظار الحساب): بوكس "حساب الجلسة · من … لـ …"، و"رجوع للعب" و"تم التحصيل · إنهاء الجلسة".

   قايمة المشروبات في الكارت فيها المتوفر بس. الغرف المعطلة مبتظهرش في الصفحة.
   الكروت الخمسة في الصفحة الثابتة (فاضي، شغال سنجل، شغال ملتي، شغال ومتحول، مستني الحساب) هي المرجع لكل حالة.
6. **الفلترة**: التابات في صفحة الأجهزة لينكات بـ `asp-route-filter`، والسيرفر بيفلتر الكروت ويحط `on` على التاب المختار
   (`t-running` و `t-multi` للألوان). العدد جنب كل تاب بيتحسب على كل الغرف المفعّلة.
   "شغال" بيشمل المستني الحساب، و"سنجل" و"ملتي" على حسب النمط الحالي للجلسة.
7. **صف التعديل بـ `editId`**: في المشروبات والغرف، "تعديل السعر" و"تعديل الاسم" لينكات لنفس الصفحة بـ `asp-route-editId`.
   لما `editId` ييجي، السيرفر بيرسم الصف ده بـ input وزرار "حفظ" وزرار "إلغاء" (لينك من غير `editId`)، والباقي نص عادي.
   في الصفحة الثابتة فيه صف واحد في حالة التعديل كمثال (شاي في المشروبات، غرفة 2 في الغرف)، ومن غير JavaScript.
   الـ input مربوط بفورم الحفظ بـ `form="edit-price-{id}"` أو `form="edit-name-{id}"`.
8. **الحالات الفاضية**: النصوص بتاعتها ("مفيش جلسات منتهية بالفلتر ده"، "مفيش أجهزة في القسم ده دلوقتي"، …)
   متكتوبة كـ comments مكان الجدول أو الكروت.

## الـ JavaScript

مفيش JavaScript غير الملفين دول. Bootstrap و jQuery Validation بيتحملوا للـ validation بس.

### `js/theme.js` (كل الصفحات)

- بيتحمل في `<head>` بعد الـ CSS ومن غير defer، وبيطبق الثيم المحفوظ (`localStorage` بمفتاح `levelup-theme`) على
  `<html data-theme="dark|light">` قبل ما الصفحة تترسم، عشان متحصلش ومضة بين الثيمين.
- بيربط زرار `#themeToggle`. الزرار فيه الحالتين (`.when-dark` و `.when-light`) والـ CSS بيخفي اللي مش مطلوب.
- لو الثيم اتغير من تاب تانية، بيتطبق هنا كمان.

### `js/timer.js` (صفحة الأجهزة بس)

بيعمل حاجتين بس:

**1) العداد**: على كل `.device-card` فيه `data-elapsed` ومش `data-stopped="true"`.

| الـ attribute | على | المعنى |
|---|---|---|
| `data-elapsed` | `article` | ثواني الجلسة كلها لحظة رسم الصفحة |
| `data-segment-elapsed` | `article` | ثواني الجزء الحالي (من آخر تحويل) لحظة رسم الصفحة |
| `data-base-cost` | `article` | سعر الأجزاء اللي خلصت قبل آخر تحويل (ج.م) |
| `data-rate` | `article` | سعر ساعة الجزء الحالي |
| `data-stopped="true"` | `article` | مستني الحساب: العداد والسعر ثابتين |
| `.timer` | جوه الكارت | بيتكتب فيه `data-elapsed` + الوقت من فتح الصفحة (HH:MM:SS) |
| `data-cost` | `<b>` في سطر السعر | `data-base-cost` + سعر (`data-segment-elapsed` + الوقت من فتح الصفحة) بالدقيقة |
| `data-segment-live` | span في سطر الأجزاء | مدة الجزء الحالي (H:MM) في الجلسة المتحولة |

**2) تحديث الكارت الفاضي**: event delegation على `document` لـ `change` على `.modepick select`.

| الـ attribute | على | المعنى |
|---|---|---|
| `data-label` | كل `option` | "سنجل" أو "ملتي" |
| `data-rate` | كل `option` | "40" أو "60" |
| `data-start-label` | زرار البدء | كلامه بيبقى "بدء " + `data-label` |
| `data-mode-rate` | span في سطر السعر | كلامه بيبقى `data-label` + " " + `data-rate` + " ج.م/ساعة" |

السيرفر بيرسم الكلام في الأول على حسب النمط الافتراضي للغرفة، والـ JavaScript بيغيّره بس لما الاختيار يتغير.
