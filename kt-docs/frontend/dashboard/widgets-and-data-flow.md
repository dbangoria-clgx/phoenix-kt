# Dashboard widgets and data flow

[Dashboard](index.md) | [Frontend](../index.md) | [Project context](../../project-context.md)

**Research:** 2026-09-25; `RP-10188` / `a6f611ae0654e2bf62deec0a7d5c1d333348d6be`. Source-confirmed behavior unless explicitly marked **Inferred** or **Unknown**. Tests and live requests were not executed.

## All movable widget registry entries

`DRAG_AND_DROP_ITEMS` is a component registry, **not** the list of enabled widgets. Runtime `dashboardWidgets` supplies names, visibility and order; the frontend selects entries where `dynamic && visible`, sorts them and looks up this registry. There are exactly five entries in the inspected revision.

| Preference key / title | Component and nominal grid size | Data source and visible behavior |
| --- | --- | --- |
| `PREF_PROPERTY_MARKET_INSIGHTS` / Property Market Insights | `HorizontalScrollableCardComponent`, 4 columns × 2 rows | Direct `getFeed('RSS_CL_INTELLIGENCE')`. Shows article image, publication date, title, truncated text; Read More opens article URL, See More opens feed home URL. This is editorial content, not the county-statistics response. |
| `PREF_INMAN_FEED` / Industry News | `HorizontalScrollableCardComponent`, 4 × 2 | Direct `getFeed('RSS_NEWS_FEED')`. Backend resolves the user's selected news source. Title becomes `Industry News (<rssLabel>)`; the historical preference key does not mean Inman is always the selected source. Runtime `newsFeedWidgetEnabled` can exclude the widget. |
| `PREF_SAVED_PROPERTIES` / My Saved Properties | `HorizontalScrollableCardComponent`, 4 × 2, `isSavedProperties: true` | `selectSavedProperties`, sliced to the first **10** without dashboard-side sorting, plus `selectCardsAttributes` for configured property fields. Uses shared `rlst-property-item` in the new theme. Cards navigate to reports. Empty state explains starring properties in card view. |
| `PREF_LAST_VIEWED_PROPERTIES_WIDGET` / Last Viewed Properties | `LastViewedPropertiesComponent`, 8 × 2 | Direct `getLastViewedProperties()` on initialization. Table renders viewed date, APN, owner, address, city, county/state; clicking a row opens a report. If this is the final widget starting a new row, the parent widens it to 12 columns. No client-side slice is applied here. |
| `PREF_MY_SAVED_SEARCHES` / My Saved Searches | `SavedSearchComponent`, 4 × 2 | Dispatches `GetMySearchData`; combines the default My Search template and stored custom templates, then sorts by descending `modifiedDate`. Opens an existing template or the create-search flow. `getFirstNSearchTemplates()` is called **without** an `n`, so its name does not impose a fixed dashboard limit. |

Registry and wiring evidence:

- `phoenix\src\app\dashboard\constants\dashboard.constants.ts:14-59` — all entries.
- `phoenix\src\app\dashboard\components\dashboard\dashboard.component.ts:202-240` — positioning and `setWidgetDataSource`.
- `phoenix\src\app\dashboard\services\dashboard-service.ts:59-65` — filtering.
- `phoenix\src\app\shared\components\horizontal-scrollable-card\horizontal-scrollable-card.component.ts:45-57,78-98` and `phoenix\src\app\shared\components\horizontal-scrollable-card\horizontal-scrollable-card.component.html:12-73` — response mapping, links, image fallback, empty favorites.
- `phoenix\src\app\dashboard\components\last-viewed-properties\last-viewed-properties.component.ts:22-27` and `phoenix\src\app\dashboard\components\last-viewed-properties\last-viewed-properties.component.html:14-45` — recent-property table.
- `phoenix\src\app\dashboard\components\saved-search\saved-search.component.ts:21-34`; `phoenix\src\app\store\user\user.selector.ts:621-624` — template selection.

### Two NgRx-backed widgets, three direct-feed/history widgets

Saved searches are fetched through `UserEffects.getMySearchData$`, `UserService.getMySearchData`, then `GetMySearchDataSuccess`, which merges template data into user state. The widget emits an empty list until both the default and custom-template values are truthy.

Favorites are normally requested by the login-success effect, not by the dashboard card. `SavedPropertiesEffects` fetches them and dispatches success/failure; the dashboard only selects the resulting list. This distinction matters when debugging an empty card after incomplete login initialization.

Evidence: `phoenix\src\app\store\user\user.effects.ts:214-222,308-319`; `phoenix\src\app\store\user\user.reducer.ts:171-191`; `phoenix\src\app\store\saved-properties\saved-properties.effects.ts:25-35`; `phoenix\src\app\saved-properties\services\saved-properties.service.ts:13-18`.

RSS and history observables are subscribed by their components/template. There is no central dashboard data action coordinating their loading. `DashboardService` is an HTTP facade here; do not insert NgRx into these direct paths.

## Fixed market panels: outside the registry

The parent posts `{ countyFipsCodes: string[] }` from `selectSelectedGeos` to `/api/dashboard/market-trends`. The response is `Section[]`: report-style sections with `templateCode`, paragraphs and/or charts. The browser selects/transforms these sections; the endpoint owns calculation/assembly.

`checkMarketTrendsData` selects tax/MarketTrends mode when `!isPinMember` **or** any selected county is not an `isListingTrendCounty`. Listing mode therefore requires membership and all selected counties to be listing-trend counties. This is a frontend display decision, not proof of backend coverage.

| Fixed panel | Response consumption and conditions |
| --- | --- |
| Market Temperature | Listing mode only. Reads `sections[0].paragraphs[0].value`, not a template-code lookup. Gauge range 0–2 with neutral 1; below/at/above 1 maps to cold/neutral/hot. Literal `N/A` renders “No Data Available.” |
| Quick Stats | `getTopGaugesSections` filters sections that have paragraphs and one of twelve recognized codes. Shared `TopGaugesComponent` renders the value and trend; `N/A` and `0%` suppress the visible trend arrow. County/color labels follow selected-county order. |
| Average Sales Price | Listing: `TMPL_REPORTS_MARKET_TRENDS_AVERAGE_SALE_PRICE`; tax: `TMPL_REPORTS_MARKET_TRENDS_CHART_AVERAGE_SALE_PRICE`. Title and explanatory text change with mode. |
| Change in Sales Activity | Listing: `TMPL_REPORTS_MARKET_TRENDS_CHART_CHANGE_IN_SALES_ACTIVITY`; tax: `TMPL_REPORTS_MARKET_TRENDS_CHART_CHANGE_IN_SALES_ACTIVITY_TAX`. Shares selected counties with the price chart. |

Quick Stats recognizes these groups:

- Listing: `TMPL_REPORTS_MARKET_TRENDS_CLOSED_SALES`, `TMPL_REPORTS_MARKET_TRENDS_PENDING_SALES`, `TMPL_REPORTS_MARKET_TRENDS_ACTIVE_LISTINGS`, `TMPL_REPORTS_MARKET_TRENDS_DAYS_TO_CONTRACT_SALES`, `TMPL_REPORTS_MARKET_TRENDS_MONTH_INVENTORY`, `TMPL_REPORTS_MARKET_TRENDS_SALE_PRICE_TO_LIST_PRICE`.
- Tax: `TMPL_REPORTS_MARKET_TRENDS_CLOSED_SALES_TAX`, `TMPL_REPORTS_NEW_CONSTRUCTION_SALES_COUNT_TAX`, `TMPL_REPORTS_RESALES_COUNT_TAX`, `TMPL_REPORTS_NEW_CONSTRUCTION_SALES_PRICE_TAX`, `TMPL_REPORTS_RESALES_PRICE_TAX`, `TMPL_REPORTS_SALE_PRICE_PER_SQFT`.

The parent derives tooltip timing from the header-details paragraph whose `htmlText` is `Value`. Tooltip prose describes a 90-day comparison and aggregation/averaging; those descriptions are UI copy, not a frontend recomputation of raw transactions.

`DashboardChartComponent` selects charts by template code, uses the first matching chart, constructs county/state legends (asterisk for nondisclosure), and removes the first county label/data position before passing the chart to `rlst-vbar-chart`. The chart is scrollable with `xMax=11`; mobile state is supplied from the user selector. Both chart panels provide Select County, not report navigation. Nondisclosure selections add “Estimated” notes.

Evidence:

- `phoenix\src\app\dashboard\components\dashboard\dashboard.component.ts:85-109,169-199,270-276`.
- `phoenix\src\app\dashboard\components\dashboard\dashboard.component.html:28-77`.
- `phoenix\src\app\dashboard\constants\dashboard.constants.ts:61-72` — explanatory copy.
- `phoenix\src\app\dashboard\components\market-temperature\market-temperature.component.ts:24-41`; `phoenix\src\app\dashboard\constants\market-temperature.constants.ts:3-20`; `phoenix\src\app\dashboard\components\market-temperature\market-temperature.component.html:8-20`.
- `phoenix\src\app\dashboard\components\dashboard-charts\dashboard-chart.component.ts:36-91` and `phoenix\src\app\dashboard\components\dashboard-charts\dashboard-chart.component.html:11-14`.
- `phoenix\src\app\reports\components\market-trends-report\top-gauges\top-gauges.component.html:16-54`.

There is no Dashboard “More Stats” navigation: the shared gauges show that link only when `isPropertyIntelligence` is true, whereas Dashboard sets `isDashboard`.

## Frontend requests matched to backend handlers

Paths below are browser API paths; request shapes contain field names only. An unrestricted backend `@RequestMapping` does **not** mean “GET-only,” even where this caller sends GET. Handler responsibilities are intentionally brief; backend chapters own their internals.

| Frontend request and source | Backend handler | Responsibility |
| --- | --- | --- |
| POST `/api/rssfeed/get-feed`, `{ "feed-name": "RSS_CL_INTELLIGENCE" }` or `"RSS_NEWS_FEED"`; `DashboardService.getFeed` | `DashboardController.getRssFeed` | Resolve/fetch a feed and return items/site metadata. Null/empty items or caught failure become HTTP 400. |
| GET `/api/dashboard/get-viewed-properties`; `getLastViewedProperties` | `DashboardController.getLastViewedPdrs` | Return recent property summaries. Caught failures become HTTP 400. |
| POST `/api/dashboard/market-trends`, `{ countyFipsCodes: [...] }`; `getMarketTrends` | `DashboardController.getDashboardMarketTrendsReport` | Return dashboard market sections for counties; no local catch in this handler. |
| GET `/api/getSavedMySearches`; `UserService.getMySearchData` through `UserEffects` | `LoginAllData.getSavedMySearchesAndAllSearchableFields` | Return saved My Search templates and searchable-field metadata in a login-response-shaped object. |
| GET `/api/get-favorites`; `SavedPropertiesService.getSavedProperties` through its effect | `SearchController.getFavorites` | Return favorite-property summaries. |
| GET `/api/get-type-ahead?address=...`; `SearchService.searchAddress` | `SearchController.getTypeAheadAddress` | Return address suggestions. |
| GET `/api/property-intelligence/geo-data`; `PropertyIntelligenceService.getGeoData`, dispatched when geography is missing | `PropertyIntelligenceController.getGeoData` | Return geography metadata used by county selection. |
| GET `/api/afterLoginGetData`; `UserService.getLoginData`, dispatched if county-limit metadata is missing | `LoginAllData.getDataAfterLogin` | Return post-login metadata. This is not a dedicated dashboard reload. |
| POST `/api/update-widgets`, `DashboardWidget[]`; `DashboardService.updateWidgets` | `PreferenceController.updateWidgets` | Update matching widget preferences. |
| PUT `/api/update-rss-news-feed?rssFeedName=...`, null body; `DashboardService.saveRSSFeed` | `PreferenceController.updateRssFeed` | Save selected industry-news preference. Frontend string is actually relative `api/update-rss-news-feed`; resolution assumes the application's base URL. |
| POST `/api/preference/save-regions`, `{ changeRegionRequiredPrefGroups, myRegionSelections }`; `PreferenceService.saveRegions` | `PreferenceController.updatePreferenceData(SaveRegionInput)` | Persist shared selected-region preferences and return map bounds. |

Frontend evidence:

- `phoenix\src\app\dashboard\services\dashboard-service.ts:21-38`.
- `phoenix\src\app\login\services\user.service.ts:30-43`.
- `phoenix\src\app\saved-properties\services\saved-properties.service.ts:13-18`.
- `phoenix\src\app\search\services\search.service.ts:113-118`.
- `phoenix\src\app\property-intelligence\services\property-intelligence.service.ts:41-42`.
- `phoenix\src\app\search\services\preference.service.ts:28-29`; `phoenix\src\app\store\preferences\preferences.effects.ts:55-84`.

Backend evidence (repository-relative, exact owning files):

- `realist\web\src\main\java\com\facl\uaf\realist\rest\controller\DashboardController.java:23-65` — class prefix `/api`, three mappings; history mapping is unrestricted.
- `realist\web\src\main\java\com\facl\uaf\realist\rest\controller\LoginAllData.java:131-139` — both unrestricted mappings.
- `realist\web\src\main\java\com\facl\uaf\realist\rest\controller\SearchController.java:125-132` — unrestricted favorites/type-ahead mappings.
- `realist\web\src\main\java\com\facl\uaf\realist\rest\controller\PropertyIntelligenceController.java:32,81-83` — class prefix and GET geography mapping.
- `realist\web\src\main\java\com\facl\uaf\realist\rest\controller\PreferenceController.java:93-98,409-416` — unrestricted region/widget mappings and PUT news mapping.

The industry-news selector is resolved server-side: `realist\web\src\main\java\com\facl\uaf\realist\rest\service\DashboardService.java:243-260` — `getRssFeed`. The browser does not fetch arbitrary RSS URLs from the `rssUrl` field itself. Recent-property summaries originate through the backend search/history service (`realist\web\src\main\java\com\facl\uaf\realist\rest\service\DashboardService.java:185-198` — `getLastViewedProperties`), not browser history.

## Representative loading sequence

```mermaid
sequenceDiagram
    participant U as User
    participant D as DashboardComponent
    participant S as NgRx store
    participant A as Angular DashboardService
    participant C as DashboardController
    participant V as Market panels
    U->>D: Enter /dashboard
    D->>S: RemoveCurrentPropertyIndex
    opt Missing geography or county-limit metadata
        D->>S: GetGeoData and/or GetLoginData
        Note over S: Existing effects populate metadata
    end
    S-->>D: Selected counties and latest isPinMember
    D->>D: Choose listing/tax mode
    alt Serialized counties differ from previous selection
        D->>A: getMarketTrends(countyFipsCodes)
        A->>C: POST /api/dashboard/market-trends
        C-->>A: Section[]
        A-->>D: Section[]
        D->>D: Set sections, labels and tooltip
        D->>V: Bind sections and counties; detectChanges
    else Same serialized counties
        Note over D: No new market request
    end
```

This sequence traces the **direct-service** statistics path, not a reports effect. Feed/history widgets and saved-item effects load independently. The metadata readiness check does not block the statistics subscription; an initial empty selected-county list can still trigger a request.

Evidence: `phoenix\src\app\dashboard\components\dashboard\dashboard.component.ts:73-109,291-305`; matched endpoint above.

## Loading, empty, unavailable and error behavior

| Area | Confirmed frontend behavior |
| --- | --- |
| Initial market load | Temperature, charts and Quick Stats body are gated on truthy `sections`; Quick Stats header remains visible. No dedicated panel loading flag/skeleton or local request error callback. |
| Market reload | Previous sections are not cleared before a new county request. The old chart content can remain until the response. `mergeMap` does not cancel older requests. |
| Missing market data | Temperature recognizes exactly `N/A`. Gauges suppress `N/A`/`0%` trends. There is no parent-wide “market data unavailable” view. The template assumes usable first section/paragraph for temperature; empty/malformed section arrays are not fully guarded. |
| RSS | No local loading, empty-feed or error panel. Card subscription expects `Feed.items`; failed requests are not mapped to a fallback array here. Image failures use the Cotality logo; that fallback does not handle feed-request failures. |
| Saved properties | Empty list displays “There are no saved properties yet” and instructions. This is not a distinct loading/error view; underlying fetch failure has an NgRx failure action but the card selects only the list. |
| Last Viewed Properties | Table headings remain with zero rows while no array is emitted or for an empty array. No explicit no-history/error text or retry control. |
| Saved searches | Empty list until default and saved templates are available. Fetch errors are caught and filtered in the effect; widget has no error banner. “Create new search” remains in the template. |
| Personalization writes | Widget/news save effects use the shared spinner wrapper; widget layout updates optimistically. See [persistence semantics](configuration-and-personalization.md#visibility-order-and-persistence). |

Evidence: component/template citations above; `phoenix\src\app\store\user\user.effects.ts:81-89,159-165,308-319`; `phoenix\src\app\store\saved-properties\saved-properties.effects.ts:25-34`; `phoenix\src\app\shared\services\spinner-overlay.service.ts:38-50`. Absence of a **local** fallback does not rule out application-wide HTTP/session handling.

**Inferred risks requiring runtime verification:** a slower old county response may overwrite newer data; after a market error the subscription can terminate because there is no local recovery. An empty section array is truthy and can reach positional template accesses. The chart's shallow copy followed by `shift()` mutates nested response arrays (`dashboard-chart.component.ts:53-59,82-85`); repeated transformation of reused objects deserves care.

## Navigation handoffs

### Address to Quick Search

After three characters, `valueChanges` assigns the address-suggestion observable. This dashboard subscription has no explicit debounce. `SearchService.searchAddress` reuses results in `sessionStorage` keyed by the entered string or calls type-ahead. Enter and suggestion clicks call `openSearch`, which selects Quick Search and navigates to `/search` with `{ isDashboardSearch: true, address }`.

`QuickSearchComponent` consumes/clears that history state and invokes search after search fields are ready. This is transient navigation state, not a saved search.

Evidence: `phoenix\src\app\dashboard\components\dashboard\dashboard.component.ts:160-162,242-249`; `phoenix\src\app\dashboard\components\dashboard\dashboard.component.html:10-19`; `phoenix\src\app\search\services\search.service.ts:113-118`; `phoenix\src\app\search\components\quick-search\quick-search.component.ts:48-58,269-274`.

### Saved template to My Search

`SavedSearchComponent.search` passes `{ isDashboard: true, template, createSearch: false }`; `add` sets `createSearch: true`. `SearchComponent` opens the My Search tab from router navigation extras. `MySearchComponent` then opens the template or creation dialog and clears consumed history state.

Evidence: `phoenix\src\app\dashboard\components\saved-search\saved-search.component.ts:37-53`; `phoenix\src\app\search\components\search\search.component.ts:170-174`; `phoenix\src\app\search\components\my-search\my-search.component.ts:196-210`.

### Saved/recent property to reports

Last-viewed rows call `NavigationService.openSingleReport(item)` directly. Saved-property cards set `isNewTheme=true`, so clicking their outer section calls the same service. It dispatches `SetProperties([item])`, derives a property ID, and navigates to `/reports` with `{ propertyId, properties }` in state. The dashboard does not render the report or guarantee availability of every related report.

Evidence: `phoenix\src\app\dashboard\components\last-viewed-properties\last-viewed-properties.component.ts:26-27`; `phoenix\src\app\shared\components\horizontal-scrollable-card\horizontal-scrollable-card.component.html:41-46`; `phoenix\src\app\shared\components\property-item\property-item.component.html:1-5`; `phoenix\src\app\shared\components\property-item\property-item.component.ts:76-89`; `phoenix\src\app\search\services\navigation.service.ts:49-52,116-122`.

## Boundaries and related reading

**Unknown:** effective RSS providers, data freshness, county entitlement/coverage, external calculation contracts and deployed failure behavior. These pages intentionally stop at locally matched handlers. The [representative test review](index.md#representative-tests-and-known-gaps) does not establish successful execution.

- [Configuration and personalization](configuration-and-personalization.md).
- [Search](../search/index.md), [reports](../reports/index.md) and [report availability](../reports/availability-and-access-rules.md).
- [Saved searches and favorites](../../feature-flows/saved-searches-and-favorites.md).
- [Web backend](../../backend/modules/realist-web.md), [property-search module](../../backend/modules/uaf-propertysearch.md), [preferences module](../../backend/modules/uaf-preference.md).
- [Property Intelligence analytics](../property-intelligence/analytics-and-data-flow.md) for the separate analytics feature.

Related chapters own their module and cross-layer details; this page owns Dashboard widget contracts and visible behavior. Documentation work state is recorded in [completion status](../../completion-status.md).
