# Ordered Code-Reading Path

[Project context](../project-context.md) | [Onboarding](index.md) | [Repository map](../overview/repository-and-technology-map.md)

## How to read

Read a coherent request path, not every file alphabetically. Start with a chapter, open its cited symbols, and follow the actual caller/callee relationship. Line numbers below describe the research baseline; symbols are the durable navigation anchor.

## Pass 1: Establish structure

| Order | Source | Why read it |
| --- | --- | --- |
| 1 | `settings.gradle:19-30` | See included Gradle projects; distinguish a folder from a build unit |
| 2 | `build.gradle:5-19,49-59,98-101` | Learn shared framework declarations, Java compatibility and artifact placement |
| 3 | `realist\web\build.gradle:7-73,97-103` | Establish executable packaging and frontend/library inclusion |
| 4 | `realist\web\src\main\java\com\facl\uaf\realist\Application.java:11-31` | Find startup, component scanning and entity/repository boundaries |
| 5 | `phoenix\angular.json:6-24,208-254` and `phoenix\package.json:4-18` | Distinguish the main app, help app and defined commands |

Companion: [system architecture](../overview/system-architecture.md).

## Pass 2: Understand the browser

| Order | Source | Question answered |
| --- | --- | --- |
| 6 | `phoenix\src\main.ts:8-24`, `phoenix\src\app\app.module.ts:24-44` | How is the application bootstrapped? |
| 7 | `phoenix\src\app\app-routing.module.ts:14-118` | Which pages load eagerly/lazily and which guards participate? |
| 8 | `phoenix\src\app\app-state.module.ts:43-75,105-149` | Which slices/effects are actually registered? |
| 9 | `phoenix\src\app\store\user\user.model.ts:243-287`, `user.reducer.ts:58-145` | What runtime data shapes the UI? |
| 10 | `phoenix\src\app\dashboard\constants\dashboard.constants.ts:14-58` | How are dashboard widget keys mapped to components? |
| 11 | `phoenix\src\app\search\components\quick-search\quick-search.component.ts:60-133` and its HTML | How does metadata become a submitted search? |

Companions: [frontend](../frontend/index.md), [Dashboard](../frontend/dashboard/index.md), [Search](../frontend/search/index.md).

## Pass 3: Follow search into the backend

1. `phoenix\src\app\store\search\search.effects.ts:170-224,265-326`: request and result handling.
2. `phoenix\src\app\search\services\search.service.ts:42-93`: concrete HTTP contracts.
3. `realist\web\src\main\java\com\facl\uaf\realist\rest\controller\SearchController.java:58-119,137-223`: accepted input, mappings and normalization.
4. `realist\web\src\main\java\com\facl\uaf\realist\service\SearchService.java:127-185,385-463`: template/preferences/input preparation and local branches.
5. `uaf-propertysearch\action\src\main\java\com\facl\uaf\propertysearch\action\PropertySearchAction.java:222-257`: action-level responsibility.
6. `uaf-propertysearch\action\src\main\java\com\facl\uaf\propertysearch\action\delegate\PropertySearchDelegate.java:307-343,621-690,920-1005`: external contract, geography and fallback branches.
7. Return to `search-board.reducer.ts:358-374` and `search-board.selector.ts:91-119` under `phoenix\src\app\store\search-board`: how the result becomes displayed state.

Companion: [search sequence](../feature-flows/property-search.md). Stop at unavailable `SmartSearchBD` implementation rather than inventing its SQL.

## Pass 4: Contrast reports, PDF and commerce

| Area | Sources to open | What changes compared with search |
| --- | --- | --- |
| Report navigation/access | `phoenix\src\app\reports\reports-router.module.ts:9-149`; `phoenix\src\app\shared\guards\reports.guard.ts:19-39` | A route and frontend access decision do not prove data availability |
| Property report | Web `rest\controller\ReportController.java:169-176`; `rest\service\report\PropertyDetailsService.java:128-201` | Preferences/templates, report XML, conversion and enrichment |
| Shared facade | Web `action\ServiceHandler.java:1001-1032` | A facade appears on this path; it is not universal |
| Report action | `uaf-reports\action\src\main\java\com\facl\uaf\report\action\ReportAction.java:531-543,943-966` | External property detail becomes report representation |
| PDF | Web `rest\controller\PdfReportController.java:114-134`; `service\reports\pdf\ReportPdfService.java:72-100`; `service\reports\pdf\PdfClient.java:19-34` | Preparation/cache and later rendering/download are distinct |
| Commerce | Web `rest\controller\StoreController.java:337-355,478-487,681-740`; `store\StoreApiClient.java:57-111` | Direct client integration, remote cart/orders and local saved state |

Here, "Web" means `realist\web\src\main\java\com\facl\uaf\realist`. Detailed paths and additional branches are in [reports](../frontend/reports/index.md), [PDF](../feature-flows/pdf-generation-and-download.md), and [commerce](../feature-flows/cart-checkout-and-report-credits.md).

## Pass 5: Cross-cutting behavior

Read the security/configuration chapters before large configuration files. Open only relevant declarations and consumers; do not copy sensitive values.

- [Authentication](../cross-cutting/authentication-and-authorization.md): chain profiles, filters/providers, session and entry variants.
- [Configuration](../cross-cutting/configuration-and-feature-flags.md): startup/bootstrap, sources, flags and unknown precedence.
- [Database](../cross-cutting/database-and-migrations.md): migrations together with their entity/repository consumers.
- [Redis](../cross-cutting/redis-caching-and-sessions.md): distinguish each serialization/key/expiry responsibility.
- [External integrations](../cross-cutting/external-integrations.md): outbound GraphQL, IFC, commerce, mapping and rendering boundaries.
- [Build/deployment](../development/build-and-deployment.md): actual artifact and external pipeline handoff.

## Reading discipline

For each file, answer: who calls it, what input is trusted, what changes, what leaves the process, and what happens on an alternate outcome? A class name alone is not enough. When revisiting after a revision change, use the [completion checkpoint](../completion-status.md) to identify findings that need revalidation.
