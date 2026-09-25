# `uaf-faresmodel:action` — included, currently source-empty

[Backend index](../index.md) · [Project context](../../project-context.md) · [Common](uaf-common.md) · [User access](uaf-useraccess.md) · [Preferences](uaf-preference.md) · [Activity](uaf-activity.md)

Source baseline: **`a6f611ae0`, branch `RP-10188`**. No builds, tests, artifact inspection, dependency resolution or runtime calls were performed.

## What a newcomer should conclude

**Confirmed:** Gradle includes `uaf-faresmodel:action`, but the current source tree supplies no implementation for it. Do not assign it ownership of the `com.fares.*` model classes used elsewhere merely because its name resembles their package. Those inspected callers import external models while their module builds explicitly depend on `com.corelogic.service.common:common`. Evidence: `settings.gradle:21-25`; `uaf-faresmodel/action/build.gradle:1-19`; `uaf-activity/action/src/main/java/com/facl/uaf/activity/action/ActivityAction.java:13-17`; `uaf-activity/action/build.gradle:19-24`; `uaf-common/action/build.gradle:38-44`.

The source-empty statement is a **tree observation**, not an inference from an empty output directory:

- Tracked-file inspection under `uaf-faresmodel` returned only `action/build.gradle` and `release.sh`.
- A direct listing of `uaf-faresmodel/action` found `build.gradle` and an existing `build` directory, **no `src` directory**.
- The contents of `build` were deliberately not inspected or used as implementation evidence.

These observations apply to this revision/current tree; file absence cannot honestly be cited to a nonexistent source line. The positive build anchors are `settings.gradle:21-21` and `uaf-faresmodel/action/build.gradle:1-19`. A generated JAR or directory name would not establish source, business purpose or active runtime participation.

## Build contract and dependency status

| Concern | Verified declaration and interpretation |
|---|---|
| Project inclusion | `include "uaf-faresmodel:action"` — included in the build, not proof of inclusion in the host's runtime classpath (`settings.gradle:21-21`). |
| Group and JAR name | Group `com.facl.uaf.faresmodel`, archive base name `uaf-faresmodel-action` (`uaf-faresmodel/action/build.gradle:1-5`). |
| Base version | Module declares `1.20.1`; root version assignment uses the project version property when supplied, otherwise the base version plus `-SNAPSHOT` (`uaf-faresmodel/action/build.gradle:8-10`; `build.gradle:88-90`). |
| Java conventions | Root applies Java and dependency management to subprojects, sets source compatibility 21, and writes JAR tasks into project `build` directories (`build.gradle:49-59,88-101`). |
| Direct project implementations | **None** in the complete module dependency block (`uaf-faresmodel/action/build.gradle:13-19`). |
| Direct external implementations | `com.fasterxml.jackson.jaxrs:jackson-jaxrs-json-provider:${jacksonVersion}` and `com.fasterxml.jackson.core:jackson-databind:${jacksonVersion}` (`uaf-faresmodel/action/build.gradle:13-17`). |
| Notable exclusion | The databind declaration itself contains an exclusion for group `com.fasterxml.jackson.core`, module `jackson-databind`. Record it as written; do not interpret it as proof of the resolved graph (`uaf-faresmodel/action/build.gradle:15-17`). |
| Version evidence | Selectively inspected `jacksonVersion` declares `2.21.5`; this is a declaration, not resolved/installed evidence (`gradle.properties:48-48`). |
| Inherited dependencies | Root adds Spring web, Redis, security, validation and other platform artifacts to subprojects. Their presence does not create module-owned controllers, persistence, or runtime behavior without source (`build.gradle:180-237`). |

The host's direct project dependency list includes common, preference, property search, user access, activity, map and reports, **not faresmodel** (`realist/web/build.gradle:97-103`). A repository build-definition search found no incoming `implementation project(":uaf-faresmodel:action")` edge. **Inferred:** this is an included source-empty build unit without an established application feature role. **Unknown:** whether historical publication or an external build consumer still needs its artifact; neither removal nor repurposing is justified by this documentation alone.

For the complete verified dependency graph, use [module dependencies](../module-dependencies.md). Do not add an outgoing edge to common or an incoming edge from the host for this module.

## What cannot be taught as implementation

| Requested module concern | Current evidence-based answer |
|---|---|
| Business features | No module-owned feature can be established from the source-empty tree. A meaning for “FARES” is not inferred. |
| Packages, classes and methods | No current source packages/classes/methods to enumerate. |
| Host callers / API entry points | No source symbol exists here to trace; no direct host dependency is declared in `realist/web/build.gradle:97-103`. |
| Inputs, outputs and transformations | None established locally. Jackson dependencies alone do not define a DTO or serialization contract (`uaf-faresmodel/action/build.gradle:13-19`). |
| Local versus remote IFC boundary | No IFC dependency or client factory is declared in this module's build block; no source operation establishes a remote boundary (`uaf-faresmodel/action/build.gradle:13-19`). |
| Database, cache and session | No local source interactions to inspect. Inherited Redis artifacts are not evidence of module-owned cache/session behavior (`build.gradle:180-217`). |
| Error/branch behavior | No source branch or error handler to document. Do not invent a fallback model-loading flow. |

These are deliberate “not established” answers, not missing placeholders. The wider model/session/client responsibilities are visible in [common](uaf-common.md), [user access](uaf-useraccess.md), and [preferences](uaf-preference.md); see their cited implementations rather than attributing external `com.fares` packages to this empty project.

## Debugging and extension guidance

When a `com.fares.*` class is missing at runtime, begin with the importing source and its declared external `common`/IFC dependencies, not this directory's name. For example, activity imports `PassportUserInfo` and `PassportActivityInfo`, while its build explicitly declares service `common` and `activity-ifc` (`uaf-activity/action/src/main/java/com/facl/uaf/activity/action/ActivityAction.java:13-17`; `uaf-activity/action/build.gradle:19-24`). Resolution and packaging are separate questions that this source-only investigation did not execute.

Before putting a new model here, establish intended ownership and explicit consuming-project dependencies; Gradle inclusion alone does not connect it to `realist:web` (`settings.gradle:21-29`; `realist/web/build.gradle:97-103`). This is extension guidance, not a recommendation to migrate current external contracts.

For actual user journeys read [login/session lifecycle](../../feature-flows/login-and-session-lifecycle.md), [saved searches/favorites](../../feature-flows/saved-searches-and-favorites.md), and [frontend state/API flow](../../frontend/state-management-and-api-flow.md). Integration and data ownership belong to [external integrations](../../cross-cutting/external-integrations.md) and [database/migrations](../../cross-cutting/database-and-migrations.md). Those links are navigation to owners, **not evidence that faresmodel participates in those flows**.

## Precise remaining gaps

- Historical intent and external publication consumers are unknown; the tracked release-script filename was observed, but its content was not used to infer runtime status.
- Whether Gradle would emit a particular empty/resource-only artifact has not been tested. Only its configured archive name is confirmed.
- No source or runtime role has been recovered from old build outputs. Revisit this chapter if real source, custom source-set wiring, or a consuming dependency is added.
