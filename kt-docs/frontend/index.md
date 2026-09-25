# Frontend: browser application and User Guide

[Project context](../project-context.md)

**Research baseline:** 2026-09-25, `RP-10188`, revision `a6f611ae0`. Source inspection only; no builds, tests, installations, services or external requests were run. **Confirmed** describes this revision, not a deployed environment.

## Start with the user's task

Phoenix helps a real-estate user find properties, inspect property information, compare market conditions, create reports and resume saved work. Its authenticated shell links those activities; the initial landing page and available controls vary with login-supplied configuration. A separate Angular application supplies the User Guide.

Do not equate a screen, a lazy module, an NgRx state slice and a backend service. Dashboard is a lazy screen with direct service calls and shared state, but no `dashboard` reducer. Search is eagerly imported. Report components can be dynamically imported inside a lazy feature. The User Guide has its own bootstrap and no main-application store registration.

Evidence: `phoenix\src\main.ts:22-24`; `phoenix\src\app\app.module.ts:24-35`; `phoenix\src\app\app-routing.module.ts:38-78`; `phoenix\src\app\app-state.module.ts:43-65`. Detailed evidence for the guide and dynamic reports is in their linked chapters.

## Reading map

| Chapter | What you will learn |
| --- | --- |
| [Bootstrap and routing](bootstrap-and-routing.md) | Root shell, eager/lazy boundaries, guards, refresh, runtime configuration and navigation |
| [State management and API flow](state-management-and-api-flow.md) | Nine actual reducers, fifteen root effect families, HTTP providers, cancellation, storage and direct-call exceptions |
| [Shared components and UI patterns](shared-components-and-ui-patterns.md) | Shared modules, form controls, templates, change detection, cleanup, styling and accessibility limitations |
| [Feature-to-code map](feature-map.md) | Where each user-facing capability starts; settings and branding behavior; links to authoritative contracts |
| [User Guide application](user-guide-application.md) | Separate application, content routes, main-app help entry points and packaging boundary |
| [Dashboard](dashboard/index.md) | Fixed county panels, five widget registry entries, data sources, personalization and persistence |
| [Search](search/index.md) | Quick Search/My Search, metadata-driven controls, grid/map and saved items |
| [Property Intelligence](property-intelligence/index.md) | Standalone analytics navigation, geography, filters and charts |
| [Reports](reports/index.md) | Report catalogue, availability, rendering and actions |
| [Commerce and sharing](commerce-and-sharing.md) | Orders/cart, Direct-to-Agent and shared-report browser flows |

For a newcomer, read bootstrap → state/API → Dashboard → Search → Reports. For a small UI change, start with the feature map and shared patterns, then follow the page's source citations.

## Architecture corrections worth remembering

- `storeClear` **exists but is not registered** as a meta-reducer; it is not evidence that logout resets all state. `UserReducer` independently sets its nested `user` to `null` on logout success.
- Effects are not one-to-one with reducers. `PreferencesEffects`, export, labels and purchase effects do not each imply a root slice.
- Not all HTTP originates in effects. Dashboard's market sections and recent history are direct component/service paths.
- HTTP setup is spread across shared and map modules. A registered class interceptor alone does not establish that every injector's client uses it.
- Browser integrations are not all server-proxied: Mixpanel calls its browser SDK and Pendo is a browser global.

Evidence: `phoenix\src\app\app-state.module.ts:67-76,105-149`; `phoenix\src\app\store\user\user.reducer.ts:214-218`; `phoenix\src\app\shared\services\mixpanel.service.ts:40-65`; `phoenix\src\app\store\user\user.effects.ts:588-603`. The state/API and Dashboard chapters explain the exceptions and provider caveat.

## Boundaries and related reading

Frontend guards guide navigation; backend security still owns authenticated requests and authorization. Runtime flags and navigation menus are not proof of report entitlement or data coverage. Read [authentication and authorization](../cross-cutting/authentication-and-authorization.md), [configuration and feature flags](../cross-cutting/configuration-and-feature-flags.md), and [backend API map](../backend/api-and-feature-map.md).

For end-to-end stories use [login/session](../feature-flows/login-and-session-lifecycle.md), [property search](../feature-flows/property-search.md), and [saved searches/favorites](../feature-flows/saved-searches-and-favorites.md). Commands and test execution belong to [development](../development/index.md), not this source-only review.

## Known gaps

The chapters distinguish source-confirmed behavior from inferred risks and unknown deployed behavior. Effective MLS (Multiple Listing Service) configuration, third-party data coverage, dependency-injector behavior in a running bundle, accessibility compliance and current test results remain unverified. Documentation work status is maintained separately in [completion status](../completion-status.md).

When changing shared behavior, account for the unregistered logout meta-reducer, overlapping HTTP providers, direct-service/SDK paths, optimistic preference updates and client/server `/support` discrepancy. Each is explained in its owning chapter; none is a claim that a production failure was reproduced.
