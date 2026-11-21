# 00 — Scope Document

**المشروع:** Warehouse Management System (WMS)
**الإصدار:** 0.1 — Phase 0 (Scoping)
**تاريخ الإنشاء:** 2026-11-21
**الحالة:** Draft — قيد المراجعة

---

## 1. Project Purpose

يهدف هذا المشروع إلى بناء نظام متكامل لإدارة المستودعات (**Warehouse Management System — WMS**) مصمم خصيصاً لاحتياجات بيئة العمل الميدانية في المستودعات الكبيرة. يعمل النظام على أجهزة Android ويعتمد على Supabase Cloud كـ backend، ويُوزَّع عبر Google Play.

**المشكلة التي يحلها النظام:**

| المشكلة الحالية | الحل الذي يقدمه النظام |
|---|---|
| إدارة الموظفين الميدانيين يدوياً أو بأدوات مشتتة | قاعدة بيانات موحدة للقوى العاملة وإدارة المهام |
| غياب تتبع الإنتاجية الفردية والجماعية | لوحات تحليلية للإنتاجية مرتبطة بكل موظف ومهمة |
| صعوبة الرقابة على حركة المخزون داخل المستودع | وحدة Inventory تتبع الكميات والمواقع والحركات |
| التواصل الورقي بين المشرفين والعمال | Tasks & Assignments رقمية مع حالة آنية |

> [!IMPORTANT]
> هذا النظام **داخلي تشغيلي** بالكامل. ليس منصة تجارة إلكترونية ولا واجهة عملاء. الجمهور المستهدف هو مديرو المستودعات والمشرفون والموظفون الميدانيون.

---

## 2. In-Scope Features (ما هو داخل النطاق)

### 2.1 Workforce Management (إدارة القوى العاملة)

- **Employee Directory:** بيانات كل موظف (الاسم، الرقم الوظيفي، القسم، الدور، معلومات الاتصال)
- **Role & Permission Management:** تعريف الأدوار (Admin, Supervisor, Worker) وصلاحيات كل دور
- **Shift Scheduling:** جدولة الوردايات وتعيين الموظفين على شيفتات محددة
- **Manual Attendance Logging:** تسجيل الحضور والانصراف يدوياً عبر التطبيق (بدون بيومترك — راجع القسم 3)
- **Leave & Absence Tracking:** تتبع الإجازات والغيابات وأنواعها

### 2.2 Inventory Management (إدارة المخزون)

- **Item Catalog:** قائمة بجميع الأصناف (SKU, Name, Unit, Category)
- **Stock Levels:** رصيد كل صنف في كل موقع داخل المستودع
- **Inbound / Outbound Transactions:** تسجيل استلام البضائع وإصدارها
- **Internal Transfers:** نقل الأصناف بين المواقع الداخلية (Zones / Racks)
- **Stock Adjustment:** ضبط الكميات بعد الجرد مع توثيق السبب
- **Low-Stock Alerts:** تنبيهات عند وصول رصيد صنف إلى الحد الأدنى المحدد

> [!NOTE]
> في Phase 1، لا يُنفَّذ Serial Number Tracking أو Batch/Lot Tracking. تُدار الكميات بمستوى SKU فقط. راجع القسم 3.

### 2.3 Task Management (إدارة المهام)

- **Task Creation:** إنشاء مهمة مع العنوان، الوصف، الأولوية، والموعد النهائي
- **Task Assignment:** تعيين مهمة لموظف واحد أو مجموعة
- **Task Status Workflow:** `Pending → In Progress → Done → Verified`
- **Task Categories:** تصنيف المهام (Picking, Packing, Receiving, Housekeeping, إلخ)
- **Attachments & Notes:** إرفاق صور أو ملاحظات نصية على المهمة
- **Task History & Audit Log:** سجل كامل بكل تغيير على المهمة

### 2.4 Productivity & Reporting (الإنتاجية والتقارير)

- **Individual Productivity Metrics:** عدد المهام المنجزة، متوسط وقت الإنجاز لكل موظف
- **Team & Shift Reports:** ملخص إنتاجية الوردية أو الفريق
- **Inventory Movement Reports:** تقرير بحركات المخزون خلال فترة زمنية
- **Attendance Summary:** ملخص حضور وغياب خلال فترة
- **Dashboard:** لوحة تحكم رئيسية تعرض KPIs الأساسية للمشرفين والمديرين
- **Data Export:** تصدير التقارير بصيغة CSV أو PDF

### 2.5 Notifications & Alerts

- **Push Notifications:** إشعارات عبر Firebase Cloud Messaging (FCM) لتنبيهات المهام والمخزون
- **In-App Notifications:** مركز إشعارات داخل التطبيق

### 2.6 Authentication & Security

- **Email/Password Auth:** تسجيل دخول بالبريد الإلكتروني وكلمة المرور عبر Supabase Auth
- **Role-Based Access Control (RBAC):** كل مستخدم يرى ويفعل فقط ما يسمح به دوره
- **Session Management:** انتهاء الجلسة تلقائياً بعد فترة خمول
- **Audit Trail:** تسجيل من فعل ماذا ومتى على كل سجل حساس

### 2.7 Integration مع Al Yaseer (خارجي — راجع القسم 4)

- استقبال بيانات من نظام Al Yaseer الخارجي (ERP أو مصدر مرجعي)
- مزامنة بيانات الموظفين و/أو المخزون بالقدر الذي تسمح به واجهة Al Yaseer

---

## 3. Out-of-Scope (ما هو خارج النطاق — قرارات نهائية)

> [!CAUTION]
> هذه القرارات نهائية للـ Phase الأول. أي طلب لإضافة أي من هذه البنود يستلزم قرار تغيير رسمي (Change Request) موثقاً.

| البند | السبب |
|---|---|
| **HRMS** (Payroll, Recruitment, Contracts, Insurance, Benefits) | نطاق مختلف تماماً؛ يتطلب نظاماً مستقلاً معتمداً قانونياً |
| **Biometric Attendance** (بصمة، Face Recognition) | يستلزم أجهزة متخصصة وتكامل Hardware خارج نطاق المشروع الحالي |
| **Serial Number / Batch / Lot Tracking** | يزيد تعقيد نموذج البيانات بشكل كبير؛ مؤجل لـ Phase لاحق |
| **Multi-tenant Architecture** | النظام يخدم مستودعاً واحداً (أو مجموعة واحدة) في Phase 1 |
| **Customer-Facing Portal** | النظام داخلي تشغيلي فقط، لا واجهة للعملاء |
| **E-Commerce / Sales Order Management** | خارج نطاق إدارة المستودع الداخلي |
| **Financial Accounting / General Ledger** | يُدار بأنظمة مالية متخصصة |
| **Vehicle / Fleet Management** | ليس من ضمن احتياجات المستودع المباشرة |
| **Quality Management System (QMS)** | مؤجل لمرحلة لاحقة بعد استقرار النواة |
| **Offline-First / Full Offline Mode** | النظام يفترض اتصالاً بالإنترنت؛ قد يُبحث في مراحل لاحقة |

---

## 4. System Boundaries

### 4.1 ما يتحكم فيه النظام (Internal)

```
┌─────────────────────────────────────────────────────────┐
│                  WMS Application                        │
│                                                         │
│  ┌────────────┐  ┌───────────┐  ┌──────────────────┐   │
│  │ Workforce  │  │ Inventory │  │  Task Management │   │
│  │ Management │  │ Module    │  │  & Productivity  │   │
│  └────────────┘  └───────────┘  └──────────────────┘   │
│                                                         │
│  ┌─────────────────────────────────────────────────┐   │
│  │          Supabase Backend (Auth, DB, Storage)   │   │
│  └─────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────┘
```

**ما يملكه النظام بالكامل:**
- قاعدة البيانات (Supabase PostgreSQL)
- المصادقة والصلاحيات (Supabase Auth + RLS)
- منطق العمل (Business Logic) المحدد في هذه الوثيقة
- واجهة المستخدم (Android App)

### 4.2 ما يعتمد على أنظمة خارجية (External Dependencies)

| النظام الخارجي | طبيعة الاعتماد | اتجاه البيانات | ملاحظات |
|---|---|---|---|
| **Al Yaseer** (ERP / مرجعي) | مصدر بيانات للموظفين و/أو المخزون الأساسي | Al Yaseer → WMS | تفاصيل التكامل غير محددة حتى الآن — راجع القسم 9 |
| **Firebase Cloud Messaging (FCM)** | إرسال Push Notifications | WMS → FCM → Device | Google Service |
| **Google Play** | توزيع تحديثات التطبيق | WMS → Google Play → Users | —  |
| **Supabase Cloud** | Hosting, Auth, DB, Storage, Realtime | ثنائي الاتجاه | Managed Service |

> [!NOTE]
> النظام **لا يُرسل** بيانات إلى Al Yaseer في Phase 1. التدفق أحادي الاتجاه: Al Yaseer → WMS فقط. إن احتُيج لتدفق عكسي، يُدرج كـ Change Request.

---

## 5. Users & Stakeholders

### 5.1 المستخدمون المباشرون (System Users)

| الدور | الاسم التقني | المسؤوليات الرئيسية | مستوى الوصول |
|---|---|---|---|
| مدير المستودع | `warehouse_manager` | رؤية الصورة الكاملة، الموافقة على التقارير، إدارة الإعدادات | Full Access |
| مشرف / Supervisor | `supervisor` | إنشاء وتعيين المهام، متابعة الحضور، مراجعة الإنتاجية | Elevated Access |
| موظف ميداني / Worker | `worker` | استلام المهام، تحديث الحالة، تسجيل الحضور | Restricted Access |
| مسؤول النظام | `admin` | إدارة المستخدمين والأدوار والإعدادات العامة | System Admin |

### 5.2 أصحاب المصلحة (Stakeholders — لا يستخدمون النظام مباشرة)

| الجهة | الاهتمام الرئيسي |
|---|---|
| إدارة الشركة | تقارير الإنتاجية والكفاءة التشغيلية |
| قسم تقنية المعلومات | الصيانة، الأمان، التكامل مع Al Yaseer |
| فريق التطوير | بناء النظام وفق هذه الوثيقة |

---

## 6. Platforms & Deployment Target

| البُعد | القرار |
|---|---|
| **Client Platform** | Android (الإصدار الأدنى المدعوم: Android 10 / API 29) |
| **Distribution Channel** | Google Play Store (Internal Track في البداية، ثم Production) |
| **Backend** | Supabase Cloud (Managed PostgreSQL + Auth + Storage + Realtime) |
| **Database** | PostgreSQL (عبر Supabase) |
| **Storage** | Supabase Storage (للمرفقات والصور) |
| **Push Notifications** | Firebase Cloud Messaging (FCM) |
| **Framework (Mobile)** | يُحدد في وثيقة Architecture (Flutter / React Native / Native Kotlin) |

> [!IMPORTANT]
> لا يوجد حتى الآن **Web Dashboard** ضمن النطاق. كل شيء يعمل عبر تطبيق Android. إن طُلب Web، يُدرج كـ Change Request منفصل.

---

## 7. Business Timezone & Localization

| الإعداد | القيمة |
|---|---|
| **Timezone** | `Africa/Cairo` (UTC+2، أو UTC+3 خلال التوقيت الصيفي) |
| **اللغات المدعومة** | العربية (أساسي)، الإنجليزية (ثانوي) |
| **اتجاه النص** | RTL (Right-to-Left) للعربية، LTR للإنجليزية |
| **تخزين الوقت** | دائماً بـ UTC في قاعدة البيانات؛ يُعرض بـ `Africa/Cairo` في الواجهة |
| **تنسيق التاريخ** | `DD/MM/YYYY` (عربي)، مع دعم التقويم الميلادي |
| **تنسيق الأرقام** | أرقام عربية (٠١٢٣) أو لاتينية حسب إعداد المستخدم |
| **العملة** | جنيه مصري (EGP) — إن احتاج النظام لأي تقييم مالي |

> [!NOTE]
> يُخزَّن كل timestamp في قاعدة البيانات بتوقيت UTC. أي تحويل للعرض يتم على مستوى التطبيق أو Supabase Functions، وليس في الاستعلامات الخام.

---

## 8. Data Ownership & Retention

### 8.1 ملكية البيانات

- **المالك القانوني للبيانات:** الشركة المشغِّلة للمستودع (العميل).
- **مزود البنية التحتية:** Supabase يوفر التخزين فقط، ولا يملك البيانات ولا يستخدمها.
- **فريق التطوير:** لا يملك أي وصول للبيانات الإنتاجية بعد التسليم، إلا بإذن صريح وموثق.

### 8.2 سياسة الاحتفاظ بالبيانات (Retention Policy)

| نوع البيانات | مدة الاحتفاظ | ملاحظات |
|---|---|---|
| سجلات المهام (Tasks) | 24 شهراً من تاريخ الإغلاق | قابل للتعديل بقرار إداري |
| سجلات الحضور والانصراف | 36 شهراً | متطلب تشغيلي وقانوني محتمل |
| حركات المخزون | 36 شهراً | للمراجعة والتدقيق |
| Audit Logs | 24 شهراً | للأمان والمراجعة |
| بيانات الموظفين المغادرين | 12 شهراً بعد المغادرة | ثم حذف أو إخفاء هوية |
| Push Notification Logs | 90 يوماً | —  |

### 8.3 حذف البيانات

- **Soft Delete أولاً:** كل حذف هو `is_deleted = true` مع `deleted_at` timestamp.
- **Hard Delete:** يتم فقط بعد انتهاء فترة الاحتفاظ المحددة، عبر Scheduled Job موثق.
- **حق النسيان:** في حالة طلب حذف بيانات موظف، يتبع الفريق إجراءً موثقاً خلال 30 يوماً.

### 8.4 النسخ الاحتياطي (Backup)

- Supabase يوفر نسخاً احتياطياً يومياً تلقائياً (Daily Backups) ضمن خطة الاشتراك.
- يُنصح بتفعيل **Point-in-Time Recovery (PITR)** في البيئة الإنتاجية.

> [!CAUTION]
> لا توجد حتى الآن سياسة نسخ احتياطي إضافية (Off-site backup) خارج Supabase. يُرفع هذا كـ Risk في وثيقة المخاطر.

---

## 9. Known Unknowns (ما لا نعرفه بعد)

هذه نقاط مفتوحة تحتاج إلى قرارات أو معلومات إضافية قبل أو خلال مراحل التنفيذ:

| # | المجهول | الأثر المحتمل | الإجراء المطلوب |
|---|---|---|---|
| 1 | **Al Yaseer Integration Details:** ما هي الـ API المتاحة؟ REST أم Database Link أم ملفات CSV؟ | يؤثر على تصميم Data Ingestion Layer بالكامل | اجتماع تقني مع فريق Al Yaseer |
| 2 | **Al Yaseer Data Model:** ما هي حقول الموظف والمخزون في Al Yaseer وكيف تُعرَّف؟ | يؤثر على تصميم جداول قاعدة البيانات | الحصول على Data Dictionary من Al Yaseer |
| 3 | **Sync Frequency مع Al Yaseer:** Real-time، دورية (كل ساعة)، أم يدوية؟ | يؤثر على Architecture التكامل والتكلفة | قرار من صاحب المشروع |
| 4 | **عدد المستخدمين المتزامنين:** ما الحجم المتوقع؟ | يؤثر على خطة Supabase المختارة والأداء | تقدير من إدارة المستودع |
| 5 | **حجم المخزون:** كم عدد SKUs؟ كم عدد الحركات اليومية؟ | يؤثر على أداء الاستعلامات وخطة التخزين | بيانات من إدارة المستودع |
| 6 | **Mobile Framework النهائي** | Flutter vs. React Native vs. Kotlin Native — القرار لم يُحسم بعد | يُحسم في وثيقة Architecture (Phase 0) |
| 7 | **نموذج Offline Partial Support:** هل يحتاج التطبيق لعرض بعض البيانات بدون اتصال؟ | يؤثر على التصميم بشكل جذري | قرار من صاحب المشروع قبل Phase 1 |
| 8 | **متطلبات الامتثال القانوني:** هل هناك قوانين محلية لحماية البيانات تنطبق؟ | يؤثر على سياسة الاحتفاظ والحذف | مراجعة قانونية |

> [!WARNING]
> البنود 1 و2 و3 (Al Yaseer) هي الأعلى خطورة. أي تأخر في الحصول على هذه المعلومات سيؤخر تصميم قاعدة البيانات وبدء التطوير الفعلي.

---

## Appendix: Document Control

| الحقل | القيمة |
|---|---|
| **رقم الإصدار** | 0.1 |
| **تاريخ الإنشاء** | 2026-11-21 |
| **الحالة** | Draft |
| **المؤلف** | scope-writer (Swarm Agent) |
| **المراجع** | coordinator |
| **التالي:** | وثيقة Architecture — `01-architecture-blueprint.md` |

---

*نهاية الوثيقة — `docs/phase-0/00-scope-document.md`*
