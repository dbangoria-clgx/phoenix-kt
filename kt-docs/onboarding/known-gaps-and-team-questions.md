# Known Gaps, Documentation Discrepancies, and Team Questions

[Project context](../project-context.md) | [Onboarding](index.md) | [Completion status](../completion-status.md)

## How to interpret this register

A documented external boundary is not a missing local implementation explanation. Conversely, an available local path that has not been traced must remain incomplete in the progress ledger. This page records evidence limits and owner questions; [completion status](../completion-status.md) records documentation work.

No running environment, network service, provider response, production database or CI execution was contacted for this documentation task.

## External evidence questions

| ID | Unknown | Why it matters | Evidence needed / likely owner |
| --- | --- | --- | --- |
| EXT-IFC | Implementation behind dependency-provided property/preference/user-access interfaces | Local calls do not establish downstream algorithms, transport details or schemas | Owning service repositories or approved interface contracts; property/UAF service teams |
| EXT-ENTITLEMENTS | Effective MLS/customer/user report and feature configuration | Code expresses conditions, not today's enabled customer population | Sanitized configuration/entitlement contract; product and access/configuration owners |
| EXT-COVERAGE | Actual report/provider coverage and freshness by property/geography | Successful authentication or purchase does not guarantee data | Provider coverage/version documentation; report/data owners |
| EXT-PLATFORM | Full production foundation, routing, scaling, release approval and rollback | A development manifest and external shared-library invocation are incomplete operational evidence | Approved shared-pipeline/configuration documentation; platform/release owners |
| EXT-CONFIG | Effective external config values and complete source precedence per environment | Local defaults alone do not define a deployed application | Sanitized environment contract and bootstrap design; platform/configuration owners |
| EXT-COMMERCE | External payment/order guarantees, reconciliation and provider-side retry/idempotency rules | Local client code cannot prove external transaction semantics | Store/payment API contract; commerce owners |
| EXT-DATA | Live schema/migration state, retention and production data volumes | Source migrations are intended changes, not proof of every deployment's database state | Sanitized migration/schema evidence; application/database owners |
| EXT-IDENTITY | Complete identity-provider configuration and operational authority assignment | Chain definitions alone do not prove deployed cookie/IdP/authority behavior | Approved identity integration contract; identity/security owners |
| EXT-SHARED-READER | Location and behavior of the recipient-facing shared-report reader and lead-capture execution path | Creation, email validation, schema and cleanup do not establish public reader authorization, rendering or expiry responses | Reader source/deployment/API contract; sharing/product/platform owners; see [sharing evidence boundary](../feature-flows/shared-report-links.md) |

These are **Unknown** external facts, not instructions to connect to systems or reveal configuration secrets.

## Source/documentation discrepancies to understand

| Older claim or common assumption | Current evidence and safer interpretation | Owning chapter |
| --- | --- | --- |
| All UAF modules are services | Gradle libraries participate in one web host; external implementations are distinct | [Backend](../backend/application-architecture.md) |
| Every component uses NgRx | Dashboard and other paths include direct service calls | [State/API flow](../frontend/state-management-and-api-flow.md) |
| Every request uses ServiceHandler | Commerce and selected controller/action paths use other chains | [Backend architecture](../backend/application-architecture.md) |
| The old project context's dependency versions are current | Root build declares Boot `3.5.15` and Cloud `2025.0.3`; the old context predates them | [Technology map](../overview/repository-and-technology-map.md) |
| README's example executable name is authoritative | Artifact naming must follow current `bootJar` configuration | [Build/deployment](../development/build-and-deployment.md) |
| A README statement proves `bootRun` defaults to local | Establish it from current task configuration or explicitly supply a profile | [Local setup](../development/local-setup.md) |
| Profiles are simply local/preprod/prod everywhere | Security and bootstrap code use specific, differing profile sets | [Configuration](../cross-cutting/configuration-and-feature-flags.md) |
| Jenkins uses a pod, so manifests are Kubernetes Deployments | The checked-in application manifest uses Cloud Foundry/Kf-style structure | [Build/deployment](../development/build-and-deployment.md) |
| An initialization migration describes the current schema | Later migrations alter/drop objects; read the history cumulatively | [Database](../cross-cutting/database-and-migrations.md) |
| A frontend Gradle test task proves tests execute | Inspect the task body; configured/disabled/legacy tooling must be distinguished | [Testing](../development/testing-and-quality.md) |
| H2-based tests prove PostgreSQL migration behavior | Database-specific SQL, JSONB and trigger behavior require separate evidence | [Database](../cross-cutting/database-and-migrations.md) |
| `uaf-faresmodel` has active model code because it is included | Verify source presence and consumers; inclusion is a narrower fact | [Module status](../backend/modules/uaf-faresmodel.md) |
| `uaf-support` is an application module | It is outside the included Gradle project list; artifact roles require separate interpretation | [Support status](../backend/modules/uaf-support-status.md) |

Initial source anchors: `build.gradle:5-19`; `settings.gradle:19-30`; `realist\web\build.gradle:7-28`; `Jenkinsfile:61-64,88-111`; `manifests\dev-usw1-kf.yml:1-21`; `phoenix\build.gradle:83-101`. The linked chapters own detailed evidence and qualifications.

## Questions for a live KT handoff

1. Which environments and sanitized accounts should a newcomer use, and which features depend on unavailable provider access?
2. Who owns the commercial meanings of report credits, purchases, unlocks and usage limits?
3. Where is the approved source of truth for feature flags, report availability and MLS-specific behavior?
4. Which team owns each unavailable IFC implementation and provider incident path?
5. What is the supported local setup contract, including remote dependencies that cannot be replaced by local PostgreSQL/Redis?
6. Which delivery/rollback steps live in shared pipeline libraries rather than this repository?
7. What compatibility rules govern serialized session/cache data during upgrades?
8. Why does the source-empty or weakly connected included project remain in the build, and which support artifacts are still used manually?
9. Which application actually serves the generated `/shared/report/<id>` link, and where are its reader authorization, expiry and lead-capture contracts documented?

Record answers with references and dates. Do not overwrite a verified source fact with an unsourced recollection.

## Source-backed behavior requiring careful explanation

These are current-code observations, not changes made by this documentation task:

| Area | Observation and consequence | Evidence / discussion owner |
| --- | --- | --- |
| HPI labels and arithmetic | The inspected annual/monthly-change lists use the same adjacent-record index calculation; the UI's year-over-year label does not establish a 12-month comparison | [PI analytics](../frontend/property-intelligence/analytics-and-data-flow.md), especially `MarketTrendUtils.java:437-511`; analytics/product owner must clarify intended series semantics |
| PI comparison charts | First-series labels can be reused for subsequent geography series without a date-key join; different provider coverage/order needs careful interpretation | [PI analytics](../frontend/property-intelligence/analytics-and-data-flow.md); analytics/data owner |
| Token lookup failure | An effect can emit `null` on an HTTP error and then call `.map`; an empty lookup and a handled network failure are not equivalent | [Dynamic fields](../frontend/search/dynamic-fields-and-lookups.md); frontend search owner |
| Saved template update | Backend update returns 204 despite a browser response type declaring a template-code response | [Saved searches](../feature-flows/saved-searches-and-favorites.md); search/preferences owner |
| Favorite save feedback | A success ribbon can open at dispatch time; it is not evidence that preference persistence succeeded | [Saved searches and favorites](../feature-flows/saved-searches-and-favorites.md); frontend saved-properties owner |
| Dashboard persistence | Widget updates change the store optimistically, while the inspected save path has no dashboard rollback action | [Dashboard personalization](../frontend/dashboard/configuration-and-personalization.md); dashboard/preferences owner |
| Logout and HTTP composition | A `storeClear` function exists without root meta-reducer registration; shared/map HTTP providers overlap | [State/API flow](../frontend/state-management-and-api-flow.md); frontend platform owner |
| Browser deep links | Client navigation to `/support` and a server request for that path have different destinations; route and server forwarding lists also differ | [Bootstrap/routing](../frontend/bootstrap-and-routing.md); frontend/web-host owner |
| Report/PDF admission | Menu, route guard, ordinary report handler, PDF preparation and file-serving paths do not implement one universal gate; usage timing differs by family | [Report availability](../frontend/reports/availability-and-access-rules.md) and [PDF differences](../feature-flows/pdf-generation-and-download.md#important-enforcement-differences); reports/access owner |
| Customized report identity | Neighborhood Profile customized-output handling passes the Neighbors type on its success path | [PDF flow](../feature-flows/pdf-generation-and-download.md); reports frontend owner; runtime combined-output impact was not established |

The linked pages contain exact source paths, conditions and qualifications. Do not broaden these observations into unverified production incidents.

## Scope of the current documentation

The set targets all requested areas and important cross-layer flows. It does not claim that every source line, external implementation, production setting, or possible provider response has been exhaustively inspected. Exact remaining local work, if any, must be visible in [completion status](../completion-status.md), not hidden by this scope statement.
