# `uaf-activity:action` — activity wrappers, not all usage tracking

[Backend index](../index.md) · [Project context](../../project-context.md) · [Common](uaf-common.md) · [User access](uaf-useraccess.md) · [Reports](uaf-reports.md)

Source baseline: **`a6f611ae0`, branch `RP-10188`**. This is static source verification, not a successful-build or deployed-runtime claim. No builds, tests, dependency resolution, or remote calls were performed.

## Purpose and the important qualification

The local classes prepare feature-usage and PDF activity records and forward them toward an external `ActivityBD` contract. This is useful business vocabulary: recording usage is not the same operation as authenticating a user or proving purchase authorization. The implementation here has `void` methods and does not return a purchase decision. Evidence: `uaf-activity/action/src/main/java/com/facl/uaf/activity/action/ActivityAction.java:44-77`; `uaf-activity/action/src/main/java/com/facl/uaf/activity/action/delegate/ActivityDelegate.java:39-99`.

**Confirmed:** the module is included, contains source, is a host dependency, and its `ActivityAction` is discoverable by the host's package scan. **Not confirmed:** a current host request actually invokes these wrapper methods. Targeted searches of the host and the other included UAF modules found no `ActivityAction`/`ActivityDelegate` callers. Therefore describe it as **packaged/discoverable, with invocation unverified**, not as the central production activity pipeline. Evidence: `settings.gradle:21-29`; `realist/web/build.gradle:97-103`; `realist/web/src/main/java/com/facl/uaf/realist/Application.java:11-21`; `uaf-activity/action/src/main/java/com/facl/uaf/activity/action/ActivityAction.java:20-26`. Caller absence is a source-search observation at the stated revision, not something an individual line can prove.

## Build and dependencies

The artifact is `uaf-activity-action`, group `com.facl.uaf.activity`, with module base version `1.20.1`. Root conventions apply Java 21, dependency management, and JAR output in each project's `build` directory. No module-local executable application is defined. Evidence: `uaf-activity/action/build.gradle:1-10`; `build.gradle:49-59,88-101`.

| Kind | Direct declaration |
|---|---|
| Project implementation | **Only `:uaf-common:action`**. |
| External IFC | `com.corelogic.service.activity:activity-ifc:${activity_ifc}`; selected property declares `8.2.60`. |
| External shared models/client support | `com.corelogic.service.common:common:${common}`; selected property declares `8.5.20`; excludes SLF4J API, commons-logging and commons-lang. |
| Serialization/API | Jackson JAX-RS JSON provider `${jacksonVersion}` (property `2.21.5`), Jakarta annotation API `1.3.5`, JAXB API `2.3.3`. |
| Annotation processing | `lombok-mapstruct-binding:0.2.0`. |

Evidence for all declarations: `uaf-activity/action/build.gradle:12-25`; version properties were read selectively: `gradle.properties:26-28,48-48`. These are **declared**, not resolved versions; inherited root dependencies and BOMs also matter (`build.gradle:49-85,180-237`). The Gradle slice is `realist:web → uaf-activity:action → uaf-common:action`; it is not proof of a method-call chain (`realist/web/build.gradle:97-103`).

## Read these classes in order

| Source/package | Responsibility |
|---|---|
| `com.facl.uaf.activity.action.ActivityAction` | Spring service extending common `BaseAction`; reads passport context for feature tracking and constructs PDF records. |
| `com.facl.uaf.activity.action.delegate.ActivityDelegate` | Plain Java wrapper, with a separately initialized external `ActivityBD` client; overloads select `writeUsageActivity` or `post`. |
| `ActivityActions.xml` | Defines `activityActionBean`, assigning `service.tier.activity` through a setter; resource presence does not demonstrate an active import. |
| Common `ActivityConfiguration` | A **different** wiring path: creates a Spring `ActivityBD` bean directly with `RestClientFactory.clientOf`. |

Evidence: `uaf-activity/action/src/main/java/com/facl/uaf/activity/action/ActivityAction.java:20-43`; `uaf-activity/action/src/main/java/com/facl/uaf/activity/action/delegate/ActivityDelegate.java:20-43,54-79`; `uaf-activity/action/src/main/resources/com/facl/uaf/activity/ActivityActions.xml:10-14`; `uaf-common/action/src/main/java/com/facl/uaf/common/shared/configuration/ActivityConfiguration.java:9-16`.

## Inputs, transformations, and outputs

### Feature activity

`trackActivity(FeatureCode)` defaults to billing record type `"BL"`. The overload accepting a record type first sets up its delegate, then obtains `PassportUserInfo` through `BaseAction`. It creates `PassportActivityInfo` with record type, current timestamp, and success status `"0"`. It also creates `WritePassportActivity` and assigns the feature's product code, **but never passes that wrapper onward**: the actual call receives only activity and user objects. Do not document the product code as transmitted by this method. Both overloads return void. Evidence: `uaf-activity/action/src/main/java/com/facl/uaf/activity/action/ActivityAction.java:44-64`; `uaf-common/action/src/main/java/com/facl/uaf/common/shared/util/UAFConstants.java:20-26`.

`ActivityDelegate.trackActivity(activity,user)` calls `activityBD.writeUsageActivity(user,activity)`. Its two additional overloads call `post(user)` and `post(user,productCodeInfo)`. These are separate contracts, not a single generic event bus. Evidence: `uaf-activity/action/src/main/java/com/facl/uaf/activity/action/delegate/ActivityDelegate.java:39-79`.

### PDF activity

`sendActivity(passport,reportType,header,quantity)` parses the string quantity as an integer and builds a record with status `"0"`, vendor `"CPL-PDF-SERVER"`, sort value `"NONE"` and pipe-delimited keyword values. The delegate copies only app code, login ID, user ID and session ID into a new passport object, sets its record type to `"VT"`, then calls `writeUsageActivity`. The returned passport object is assigned but not used. This is the actual transformation, not evidence of how a remote PDF service interprets it. Evidence: `uaf-activity/action/src/main/java/com/facl/uaf/activity/action/ActivityAction.java:66-77`; `uaf-activity/action/src/main/java/com/facl/uaf/activity/action/delegate/ActivityDelegate.java:81-98`.

## Local versus external boundary — and the verified bypass

`ActivityDelegate.setupActivityDelegate()` would create an external `ActivityBD` proxy from `serviceTierActivityUrl`. However, `ActivityAction.setupActivityAction()` instead calls `RestClientFactory.clientOf(ActivityDelegate.class, ...)`, passing the local wrapper class. These are **two distinct construction paths**; the source does not show the action locally constructing the wrapper and invoking its setup method. The behavior of the external factory for that concrete class is unknown here. Evidence: `uaf-activity/action/src/main/java/com/facl/uaf/activity/action/ActivityAction.java:28-43`; `uaf-activity/action/src/main/java/com/facl/uaf/activity/action/delegate/ActivityDelegate.java:24-37`.

A verified activity-service consumer exists **outside this module**: reports' `EcommerceDelegate` injects common's `ActivityBD` bean directly, calls `checkEcommerceReportRerunAccess`, and calls `purgeEcommerceExpiredRecords`. The reuse check returns true only for success status and a nonblank transaction ID; failures fall through to false. Do not route this code through `ActivityAction` in a diagram merely because both contain “activity.” Evidence: `uaf-common/action/src/main/java/com/facl/uaf/common/shared/configuration/ActivityConfiguration.java:9-16`; `uaf-reports/action/src/main/java/com/facl/uaf/report/action/delegate/EcommerceDelegate.java:31-97,100-114`.

Remote paths, server-side persistence, billing rules, transport details beyond the client factory, delivery guarantees and retry policy are **Unknown**: this repository supplies calls to IFC contracts, not the service implementation. See [external integrations](../../cross-cutting/external-integrations.md), [cart/credits flow](../../feature-flows/cart-checkout-and-report-credits.md), and [reports](uaf-reports.md) rather than assuming this small wrapper owns those features.

## Session, data, and cache

Feature activity uses common's `BaseAction.getPassportUserInfo()`: it obtains the current session and entitlement wrapper, returning null when unavailable. PDF activity takes its passport explicitly. The activity classes have no local repository/cache writes or transactional retry loop; their state consists of URL/client fields and per-call DTOs. This does **not** prove the external tier stores no data. Evidence: `uaf-common/action/src/main/java/com/facl/uaf/common/shared/action/BaseAction.java:48-84`; `uaf-activity/action/src/main/java/com/facl/uaf/activity/action/ActivityAction.java:26-77`; `uaf-activity/action/src/main/java/com/facl/uaf/activity/action/delegate/ActivityDelegate.java:24-103`. Session infrastructure belongs to [Redis caching and sessions](../../cross-cutting/redis-caching-and-sessions.md).

## Failure branches and debugging guidance

These are source observations, not exercised failures:

1. **URL setter defect:** `setServiceTierActivityUrl(String serviceTierActivityUrll)` assigns the field to itself using the differently spelled name. Even the XML setter path would not populate the field correctly. Check this before attributing a failed factory call to the remote service. Evidence: `uaf-activity/action/src/main/java/com/facl/uaf/activity/action/ActivityAction.java:28-40`; `uaf-activity/action/src/main/resources/com/facl/uaf/activity/ActivityActions.xml:10-14`.
2. **Initialization is not automatic in the plain delegate:** setup populates `activityBD`, but the tracking methods do not call setup. There is no service annotation or injection on that field in this class. A manually created delegate must not be assumed ready. Evidence: `uaf-activity/action/src/main/java/com/facl/uaf/activity/action/delegate/ActivityDelegate.java:20-43`.
3. **Asymmetric errors:** the three tracking overloads catch only `ActivityException`, log, and return no failure result. Other runtime exceptions can escape. Their diagnostic expressions dereference the user, so missing passport data can also break logging. PDF delegate tracking catches `Exception`, prints the stack trace and logs; callers still receive no status. Evidence: `uaf-activity/action/src/main/java/com/facl/uaf/activity/action/delegate/ActivityDelegate.java:39-99`.
4. **Validation occurs before the PDF delegate catch:** invalid quantity fails in `Integer.parseInt` in the action; no range check prevents negative counts. Feature tracking dereferences the feature code and may obtain a null session user. Evidence: `uaf-activity/action/src/main/java/com/facl/uaf/activity/action/ActivityAction.java:49-76`; `uaf-common/action/src/main/java/com/facl/uaf/common/shared/action/BaseAction.java:68-84`.

For an extension, first establish a real caller and choose deliberately between this wrapper and the existing direct IFC bean. Then inspect initialization, passport preconditions, field transmission and error propagation before adding a new activity type. These are recommendations based on the cited branches, not changes made by this documentation.

## Remaining questions and reading path

- **Unknown:** whether any deployment explicitly imports the XML or invokes the wrappers reflectively; no such path was found in the inspected owned Java/XML source.
- **Unknown:** whether the factory supports proxying this concrete `ActivityDelegate` class as intended; external IFC internals were not inspected.
- **Confirmed boundary:** PDF-related method names here do not establish a browser-to-PDF flow. Follow [PDF generation and download](../../feature-flows/pdf-generation-and-download.md) and [frontend report actions](../../frontend/reports/report-rendering-and-actions.md) for independently traced callers.
- To distinguish the other usage path, read [user access](uaf-useraccess.md); for the shared proxy factory read [common](uaf-common.md). Do not expand UAF/FARES or infer deployment roles from their names.
