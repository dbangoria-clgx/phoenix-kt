# Search

[Project context](../../project-context.md) | [Frontend](../index.md)

**Research baseline:** `RP-10188`, `a6f611ae0`, 2026-09-25. Statements marked **Confirmed** describe inspected source, not a running deployment.

Search turns property criteria into a list of property records that can be inspected on a grid or map and passed to reports, exports, labels, and purchases. It is not a static address form: user templates and preferences control the available fields, operators, defaults, geography, and limits.

## Reading path

1. [Quick Search and My Search](quick-search-and-my-search.md): what users submit and what is retained.
2. [Dynamic fields and lookups](dynamic-fields-and-lookups.md): how metadata becomes controls.
3. [Results, grid, map, and actions](results-grid-map-and-actions.md): selection and downstream actions.
4. [End-to-end property search](../../feature-flows/property-search.md): browser request through SmartSearch.
5. [Saved searches versus favorites](../../feature-flows/saved-searches-and-favorites.md) and [map lookup](../../feature-flows/maps-and-property-lookup.md).

## Vocabulary and boundaries

- **APN**: assessor parcel number; not a globally unique property identifier. County/FIPS context matters.
- **FIPS**: geographic code used here to identify a county. **MLS** means multiple listing service; MLS/user provisioning can change available preferences and features.
- **Template**: ordered field definitions and, for a saved search, criterion values. It is not a saved result set.
- **Property identifier**: structured identity carried with a result; favorites persist identifiers rather than templates.
- **SmartSearch**: external IFC client boundary behind the in-process property-search action/delegate. Do not infer its database or deployment from the Java package name.

## First source stops

| Responsibility | Source |
| --- | --- |
| Quick form/request | `phoenix/src/app/search/components/quick-search/quick-search.component.ts:60-165` |
| My Search template/criteria | `phoenix/src/app/search/components/my-search/my-search.component.ts:214-359` |
| Search orchestration and result branches | `phoenix/src/app/store/search/search.effects.ts:103-445` |
| Browser HTTP methods | `phoenix/src/app/search/services/search.service.ts:42-93` |
| Controller normalization | `realist/web/src/main/java/com/facl/uaf/realist/rest/controller/SearchController.java:58-224` |
| Server search preparation | `realist/web/src/main/java/com/facl/uaf/realist/service/SearchService.java:127-429` |

Module internals belong to [uaf-propertysearch](../../backend/modules/uaf-propertysearch.md); shared access/configuration belongs to [configuration and flags](../../cross-cutting/configuration-and-feature-flags.md). Report access is explained separately in [report availability](../reports/availability-and-access-rules.md).

**Unknown:** effective deployed templates, MLS entitlement values, provider matching/ranking, and actual property coverage. No builds, tests, network calls, or runtime UI verification were performed for these pages.

**Documentation scope:** these pages own search UI behavior; backend module, report, export and cross-cutting details live in the linked authoritative chapters. Work state and future resumption are maintained separately in [completion status](../../completion-status.md).
