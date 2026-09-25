# Project Glossary

[Project context](../project-context.md) | [Business overview](business-overview.md) | [Repository map](repository-and-technology-map.md)

This glossary explains terms for navigation and discussion. Standard technical meanings are distinguished from repository-specific behavior. No expansion is invented for an internal name whose origin is not established in source.

## Business and property terms

| Term | Meaning in this guide | Read more |
| --- | --- | --- |
| MLS | Multiple Listing Service; the application uses an MLS/group context in user access and configuration | [Authentication](../cross-cutting/authentication-and-authorization.md) |
| APN | Assessor's Parcel Number; a property-related identifier normalized in search paths | [Search flow](../feature-flows/property-search.md) |
| FIPS | Federal Information Processing Standards code; in these property requests, a geographic/county identifier paired with property identity | [Search flow](../feature-flows/property-search.md) |
| CLIP | Property identifier/lookup terminology used by the CLIP integration; its formal internal expansion is not established here | [External integrations](../cross-cutting/external-integrations.md) |
| Property identifier | Structured identity used by report/search/favorite operations, not simply a display address | [Reports](../frontend/reports/report-catalogue.md) |
| Quick Search | A search entry path built from runtime template/field information | [Quick Search and My Search](../frontend/search/quick-search-and-my-search.md) |
| My Search | Configurable search criteria/template workflow; not a synonym for favorite properties | [Search](../frontend/search/index.md) |
| Saved search | A persisted reusable search-template definition | [Saved items](../feature-flows/saved-searches-and-favorites.md) |
| Favorite / saved property | A stored property identity in the user's saved-property collection | [Saved items](../feature-flows/saved-searches-and-favorites.md) |
| Comparable / comp | A property used for comparative research; report criteria and selection determine the actual set | [Report catalogue](../frontend/reports/report-catalogue.md) |
| Neighborhood profile | An area-context report family, distinct from the individual property's details | [Report catalogue](../frontend/reports/report-catalogue.md) |
| HPI | Home Price Index in the application's terminology; exact series/coverage remains a provider contract | [Property Intelligence](../frontend/property-intelligence/index.md) |
| Entitlement | Whether a user/account is permitted a capability; separate from a route existing or provider data being available | [Report availability](../frontend/reports/availability-and-access-rules.md) |
| Availability | Whether a report/action can be supplied for the current context; may combine distinct feature, user, data and commercial conditions | [Report availability](../frontend/reports/availability-and-access-rules.md) |
| Report credits / usage | Application mechanisms for tracking eligible report consumption; not interchangeable with store checkout | [Commerce and credits](../feature-flows/cart-checkout-and-report-credits.md) |
| Direct-to-Agent | A named commercial/access flow with its own frontend integration; do not infer that it is ordinary cart checkout | [Commerce frontend](../frontend/commerce-and-sharing.md) |
| Shared report snapshot | Material captured for the sharing workflow; its lifetime and recipient access differ from an authenticated interactive report | [Sharing](../feature-flows/shared-report-links.md) |

## Application and data concepts

| Term | Meaning | Why it matters |
| --- | --- | --- |
| Phoenix | Main Angular application in `phoenix\src` | Distinguish it from the workspace and separate help app |
| User Guide | Separate Angular application in `phoenix\projects\user-guide` | Has its own bootstrap, routes and asset build |
| Template | Runtime metadata used by search/report/preferences; not necessarily an Angular HTML template | Identify the kind of template before tracing it |
| DataElement / field metadata | Data describing fields, operators, rendering or values in supported flows | The visible form is not fully hardcoded |
| Render identifier | Metadata used to select a UI control/widget representation | A new search field can involve metadata and renderer support |
| Preference | Stored user/group configuration accessed through preference operations | Can affect UI, geography and report/search defaults |
| Feature flag | A condition controlling exposure/behavior | Not equivalent to user authentication or authorization |
| NgRx slice | A registered part of application store state | An effects folder does not automatically create a slice |
| Effect | Reactive handling of dispatched actions and side effects | Some calls bypass effects; inspect actual usage |
| Selector | A function deriving UI-facing values from state | Useful for tracing how server data becomes rendered results |
| DTO | Data Transfer Object used at a boundary | A DTO is not necessarily a database entity |
| Entity | A persistence-mapped object | Check its table, constraints and migrations |
| Cache | A reuse mechanism with its own key, value, expiry and invalidation policy | Do not assume all Redis data shares one policy |
| Session | Server/browser interaction state, including security context in relevant flows | It is distinct from an arbitrary application cache |
| Flyway migration | Versioned database change script | Read changes in order rather than treating initialization SQL as current schema |

## Backend and integration terms

| Term | Meaning in this repository | Read more |
| --- | --- | --- |
| UAF | Internal namespace/module family; formal expansion is not established here | [Backend](../backend/index.md) |
| FARES | Name appearing in project/artifact/package context; formal expansion and historical ownership are not established here | [FARES model status](../backend/modules/uaf-faresmodel.md) |
| IFC | Naming convention in dependency/interface artifacts; formal expansion is not assumed | [External integrations](../cross-cutting/external-integrations.md) |
| Action | An application/library orchestration class in the UAF modules | [Module responsibilities](../backend/application-architecture.md) |
| Delegate | A class forwarding/preparing work for an integration contract | [Property search module](../backend/modules/uaf-propertysearch.md) |
| ServiceHandler | Shared facade used by selected preference/report/template paths, not every backend request | [Backend architecture](../backend/application-architecture.md) |
| SmartSearchBD | Dependency-provided property-search/detail contract called by delegates | [Search flow](../feature-flows/property-search.md) |
| Report XML | An intermediate representation in established report-generation paths | [Property reports](../feature-flows/property-details-and-reports.md) |
| PDF preparation | Gathering/caching report inputs and creating the later download reference | [PDF flow](../feature-flows/pdf-generation-and-download.md) |
| PDF rendering | Turning prepared content into PDF bytes, including an external rendering boundary where implemented | [PDF flow](../feature-flows/pdf-generation-and-download.md) |
| DGS / GraphQL client | Tooling and calls for outbound structured data queries in the inspected integrations | [External integrations](../cross-cutting/external-integrations.md) |
| WMS | Web Map Service imagery/overlay convention; browser image loading differs from an Angular JSON API request | [Maps](../feature-flows/maps-and-property-lookup.md) |

## Security and delivery vocabulary

| Term | Meaning and distinction |
| --- | --- |
| Authentication | Establishing who the caller is |
| Authorization | Determining whether that caller may perform an operation |
| SSO | Single sign-on; an entry mechanism, not a guarantee of all report access |
| SAML | Security Assertion Markup Language used by supported federated login paths |
| OAuth client credentials | An application obtaining credentials for outbound API calls; not proof of browser OAuth login |
| JWE | JSON Web Encryption; inspect the direct-link contract rather than assuming all links use ordinary session authentication |
| CSRF | Cross-Site Request Forgery; source-specific protection choices must be read in security configuration |
| Spring profile | A configuration/bean-selection label; actual profile names matter |
| `bootJar` | Spring Boot executable-JAR packaging task |
| Gradle project | A build unit, not necessarily a separately deployed application |
| Jenkins build pod | Infrastructure executing the delivery pipeline, not automatically the application's hosting model |
| Cloud Foundry/Kf-style manifest | The checked-in application deployment-contract format; full platform internals remain external |

## Evidence and limits

Repository-specific usages are sourced in the linked authoritative chapters. Initial anchors include `phoenix\src\app\store\user\user.model.ts:243-287`, `realist\web\src\main\java\com\facl\uaf\realist\rest\controller\SearchController.java:58-119,137-223`, `realist\web\src\main\java\com\facl\uaf\realist\rest\mapper\SearchFieldsMapper.java:11-19`, `realist\web\src\main\java\com\facl\uaf\realist\action\ServiceHandler.java:112-166`, and `settings.gradle:19-30`. HPI wording: `phoenix\src\app\property-intelligence\constants\property-intelligence.constants.ts:311-329`.

Standard acronym definitions do not establish a provider's commercial semantics. Ask the owning team about unresolved internal names rather than expanding them speculatively.
