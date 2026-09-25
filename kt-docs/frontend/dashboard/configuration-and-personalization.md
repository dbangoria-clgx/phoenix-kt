# Dashboard configuration and personalization

[Dashboard](index.md) | [Frontend](../index.md) | [Project context](../../project-context.md)

**Research:** 2026-09-25; `RP-10188` / `a6f611ae0654e2bf62deec0a7d5c1d333348d6be`. Source inspection, not a deployed-configuration audit. No configuration values, credentials or real user payloads are reproduced.

## What is configurable?

Users can hide/show movable widgets, reorder them, select industry-news sources, and change the counties driving market panels. These are different state/persistence paths. The fixed statistics sections are not freely draggable; the management dialog treats `dynamic: false` widgets as locked.

### Runtime inputs versus frontend definitions

| Input | Source/consumer | Meaning |
| --- | --- | --- |
| `dashBoardEnabled` | Login/user state → `selectIsDashboardEnabled` | Controls default main-application login destination and Dashboard navigation visibility, not the existence of its route. |
| `dashboardWidgets` | Login/user state → `selectDashboardWidgets` | Runtime entries carrying `name`, `label`, `dynamic`, `visible`, `displayOrder`. Names must match the frontend registry for movable entries. |
| `newsFeedWidgetEnabled` | User state → dashboard and manager | Excludes `PREF_INMAN_FEED` and the manager's News options tab when false. |
| `rssNewsFeeds` | User state → radio options and title lookup | List of `{ rssFeed, rssLabel, rssUrl }`; the browser saves the identifier, while the backend resolves feed content. |
| `userSelectedRssNewsFeed` | User state → `selectSelectedRSSFeed` | Current industry-news selection; controls option selection and dynamic widget title. |
| Selected geography / `isPinMember` | User selectors → market panels | Chooses counties and listing-versus-tax display mode. County metadata also includes nondisclosure/listing eligibility. |
| Card field preferences | `selectCardsAttributes` → saved property cards | Controls which configured property attributes the shared card renders. |
| Component registry and layout constants | Frontend TypeScript | Defines supported dynamic components and dimensions; it is not supplied as executable code by preferences. |

Evidence:

- `phoenix\src\app\dashboard\models\dashboard.ts:1-13` — widget and RSS contracts.
- `phoenix\src\app\store\user\user.reducer.ts:58-103` — login response into user state.
- `phoenix\src\app\store\user\user.selector.ts:83-90,181-206,626-638` — selectors.
- `phoenix\src\app\dashboard\components\dashboard\dashboard.component.ts:146-158,192-195,231-239,280-284`.
- `phoenix\src\app\dashboard\components\manage-widget\manage-widget.component.ts:36-56`.
- `phoenix\src\app\dashboard\constants\dashboard.constants.ts:14-59`.

**Confirmed:** these are runtime user/configuration inputs, not merely Angular build-environment switches. **Unknown:** their effective values for any deployed MLS group or user.

## How preferences become widgets

At the backend boundary, `LoginService.processWidgetManagement` reads preference group `PG_RL_DASHBOARD_WIDGET_MANAGEMENT`. It converts each three-part value into a `DashboardWidget`: `D` means dynamic (otherwise static), `1` means visible, and the third part becomes display order. Preference code becomes `name`; preference label becomes `label`. `PREF_TWITTER_FEED` is explicitly excluded.

Source: `realist\web\src\main\java\com\facl\uaf\realist\rest\service\LoginService.java:697-732`.

The frontend separately uses `DRAG_AND_DROP_ITEMS[name]` to select the component. Thus a backend preference's existence alone does not implement a new widget. Static entries may appear locked in the manager, while actual fixed panels remain explicitly written in `dashboard.component.html`.

**Important distinction:** this page documents the exchanged preference contract, not the preference system's storage/provider internals. See [preferences backend](../../backend/modules/uaf-preference.md).

## Visibility, order and persistence

### Hide and show

The ellipsis menu offers Turn off widget, Drag widget, and Manage Widgets. Turn off dispatches `HideUnhideDashboardWidget(key)`. Its effect reads the full widget array, copies it and calls `DashboardService.updateWidgetListForHide`:

- Toggles `visible`.
- Sets `displayOrder` to **99**.
- Tracks show/hide analytics.
- Dispatches `UpdateDashboardWidgets` with the updated array.

The manager instead holds a shallow-copied widget array, only toggles entries where `dynamic` is true, and saves it when the Visible widgets tab is active. Cancel/close dispatches no widget save. Because hidden/shown items get order 99, showing one does not restore its former position.

Evidence: `phoenix\src\app\shared\components\widget-actions\widget-actions.component.html:1-8`; `phoenix\src\app\shared\components\widget-actions\widget-actions.component.ts:50-62`; `phoenix\src\app\dashboard\services\dashboard-service.ts:41-65`; `phoenix\src\app\store\user\user.effects.ts:93-110`; `phoenix\src\app\dashboard\components\manage-widget\manage-widget.component.ts:49-73`.

### Reorder

1. `getSortedDynamicAndVisibleWidgets` filters and sorts the runtime list.
2. `DashboardComponent.getDragAndDropItems` packs items left-to-right across twelve columns, advancing `y` by two when a row fills.
3. A final Last Viewed Properties widget at column zero is widened from eight to twelve columns.
4. Drag widget emits through `WidgetActionsService.dragEnabled`; the container enables Gridster dragging.
5. Gridster's item-change callback sorts positions by `y`, then `x`, and assigns `displayOrder = index + 6`.
6. The parent debounces events by **500 ms**, combines reordered dynamic widgets with retained static/hidden widgets, and dispatches `UpdateDashboardWidgets`.

**Persisted value:** widget preference order/visibility, **not** pixel positions, Gridster `x`/`y`, sizes or a local-storage layout. Returning to the page reconstructs coordinates using the packing algorithm.

Evidence: `phoenix\src\app\dashboard\components\dashboard\dashboard.component.ts:136-143,202-228`; `phoenix\src\app\shared\components\drag-and-drop-container\drag-and-drop-container.component.ts:40-49,83-94`; `phoenix\src\app\shared\components\drag-and-drop-container\drag-and-drop.constants.ts:7-16`.

### Save semantics

`UserReducer` handles `UPDATE_DASHBOARD_WIDGETS` immediately by replacing `user.dashboardWidgets`. In parallel, `UserEffects.updateDashboardWidgets$` sends POST `/api/update-widgets` under the shared spinner wrapper with `{ dispatch: false }`. There is no success/failure layout action and no rollback in this path.

The matched `PreferenceController.updateWidgets` hands the array to the preference updater. The contract writes matching preference codes as `D/S,1/0,displayOrder`; omitted entries are not explicitly deleted by the inspected matcher. If the expected preference group is missing or not singular, the inspected updater logs instead of performing that update. A visually changed layout therefore does not prove durable persistence.

Evidence:

- `phoenix\src\app\store\user\user.reducer.ts:473-480`.
- `phoenix\src\app\store\user\user.effects.ts:159-165`.
- `phoenix\src\app\dashboard\services\dashboard-service.ts:37-38`.
- `realist\web\src\main\java\com\facl\uaf\realist\rest\controller\PreferenceController.java:409-411`.
- `realist\web\src\main\java\com\facl\uaf\realist\action\ServiceHandler.java:1757-1802` — exchanged preference format and matching/no-group branches only.

```mermaid
sequenceDiagram
    participant U as User
    participant G as Gridster container
    participant D as DashboardComponent
    participant S as NgRx reducer
    participant E as UserEffects
    participant P as PreferenceController
    U->>G: Drag widget
    G->>G: Sort y/x; set displayOrder index + 6
    G-->>D: updatedDragAndDropItems
    D->>D: Debounce 500 ms; retain static/hidden entries
    D->>S: UpdateDashboardWidgets
    S-->>D: New widget array immediately
    Note over S,D: Optimistic re-render; not a save acknowledgement
    D->>E: Same UpdateDashboardWidgets action
    E->>P: POST /api/update-widgets
    alt Request completes normally
        P-->>E: Empty response
        Note over E: No success action
    else Request fails
        P-->>E: HTTP error
        Note over S,E: No dashboard rollback action
    end
```

The reducer/effect arrows represent two consumers of the same dispatch, not a second dispatch by the component. Backend preference internals are deliberately omitted. The spinner wrapper uses `switchMap` and finalization; it is not an acknowledgement/rollback mechanism (`phoenix\src\app\shared\services\spinner-overlay.service.ts:38-50`).

## News-source selection

The historical key `PREF_INMAN_FEED` is reused for configurable industry news. There are two entry points:

- Widget menu radios dispatch `SaveRSSFeed` as the form control changes.
- Manage Widgets → News options dispatches `SaveRSSFeed` only on Save changes.

The manager saves **only the active tab**: Visible widgets dispatches `UpdateDashboardWidgets`; News options dispatches `SaveRSSFeed`. Editing both tabs then saving is not a combined transaction.

`saveRSSFeed$` sends PUT using `DashboardService.saveRSSFeed`, then dispatches `SaveRSSFeedSuccess`. The reducer changes `userSelectedRssNewsFeed`; the dashboard changes the industry-news item/title and clones the item array. The cold `getFeed('RSS_NEWS_FEED')` observable is subscribed when the dynamic card is created; the server resolves the newly saved preference.

Evidence: `phoenix\src\app\shared\components\widget-actions\widget-actions.component.ts:36-47`; `phoenix\src\app\dashboard\components\manage-widget\manage-widget.component.ts:65-78`; `phoenix\src\app\store\user\user.effects.ts:81-89`; `phoenix\src\app\store\user\user.reducer.ts:491-498`; `phoenix\src\app\dashboard\components\dashboard\dashboard.component.ts:122-133,280-284`; `phoenix\src\app\dashboard\services\dashboard-service.ts:21-26`.

Backend boundary: `realist\web\src\main\java\com\facl\uaf\realist\rest\controller\PreferenceController.java:414-416` — `updateRssFeed`; `realist\web\src\main\java\com\facl\uaf\realist\action\ServiceHandler.java:1806-1818` identifies `PG_RL_INSIGHTS_DASHBOARD_NEWS_FEED` / `PREF_RL_INSIGHTS_DASHBOARD_NEWSFEED_KEY`; `realist\web\src\main\java\com\facl\uaf\realist\rest\service\DashboardService.java:243-260` resolves the selected source on subsequent feed requests.

**Inferred implementation sensitivity:** news refresh relies on item replacement/dynamic-view recreation rather than an explicit refresh action. The container only assigns inputs during `createDynamicComponents`; the template uses an object-identity `ngFor` without `trackBy`. A future change to preserve component instances would need to update their inputs/refetch behavior deliberately.

**Confirmed error-path gap:** the RSS effect contains `catchError(() => void(0))`, not an observable fallback or a failure action. Do not describe it as successful recovery. No runtime test was performed for this path. The widget-title lookup can also produce `Industry News (undefined)` when the selection has no matching label.

## County selection is shared personalization

The dashboard calls `ensureGeoDataLoaded` on initialization. It dispatches `GetGeoData` if counties or states are absent, and `GetLoginData` if the maximum county-selection count is null/undefined.

Dispatch does not guarantee a new geography request: `UserEffects.getGeoData$` skips the HTTP call when `selectGeoData` already has a truthy value (`phoenix\src\app\store\user\user.effects.ts:294-305`). Inspect both the selected geography and cached metadata when diagnosing readiness.

Select County waits for nonempty entitled states/provisioned counties and a defined maximum, then opens shared `ChangeRegionComponent` with `showFilter: true`. Saving dispatches `SaveRegionsAction(selectedRegions)`. `PreferencesEffects` sends both the required preference groups and region selections; success:

- Displays “Counties have been updated.”
- Dispatches `SaveRegionsSuccessAction`, updating selected provisioned geographies.
- Refreshes search lookups and result counts.
- Consequently causes the dashboard selected-geography subscription to request new market sections.

Failure produces `SaveRegionsFailAction` and “Something went wrong. Try to save regions again.” The county choice is therefore not an isolated dashboard filter.

Evidence:

- `phoenix\src\app\dashboard\components\dashboard\dashboard.component.ts:252-267,291-305`.
- `phoenix\src\app\search\components\change-region\change-region.component.ts:187-192`.
- `phoenix\src\app\store\preferences\preferences.effects.ts:55-95`.
- `phoenix\src\app\store\user\user.reducer.ts:367-375`; `phoenix\src\app\store\user\user.selector.ts:181-184`.
- `phoenix\src\app\search\services\preference.service.ts:28-29`; `realist\web\src\main\java\com\facl\uaf\realist\rest\controller\PreferenceController.java:93-98`.

The manager removes `PREF_MLS_MARKET_TEMPERATURE` if not every selected county is listing-trend-enabled. The actual temperature panel has the additional membership condition described in [fixed panels](widgets-and-data-flow.md#fixed-market-panels-outside-the-registry). Those are not identical checks.

Source: `phoenix\src\app\dashboard\components\manage-widget\manage-widget.component.ts:37-49`; `phoenix\src\app\dashboard\components\dashboard\dashboard.component.ts:192-195`.

## Debugging and change checklist

| Symptom/change | Inspect before changing code |
| --- | --- |
| Dashboard link absent | `dashBoardEnabled`, shared navigation filtering and login-navigation branch; do not assume route removal |
| Widget absent | Runtime name, `dynamic`, `visible`, `newsFeedWidgetEnabled`, and exact registry key |
| Unknown/new widget causes failure | `getDragAndDropItems` has no unknown-key fallback; backend preference provisioning and frontend registration must agree |
| Reordered widget returns elsewhere | Persisted `displayOrder`, index offset 6, row-packing algorithm and special 12-column recent-widget case |
| Hidden widget reappears at end | Show/hide assigns order 99, not its prior slot |
| UI changes but next login disagrees | Optimistic reducer, HTTP outcome and preference handler; no automatic rollback |
| News label changes but content is unexpected | Selected feed identifier/label, PUT result, recreation/subscription and backend feed resolution |
| County dialog does not open | Readiness filters wait for nonempty state/county lists and maximum-selection metadata; no explicit empty-entitlement message in this opener |
| New widget implementation | Register component and dimensions, provide observable or self-loading behavior, align runtime preference name, add expected states/navigation and behavior tests in the implementation task |

## Known gaps and boundaries

- **Confirmed:** no guard for an unregistered dynamic widget name; no dedicated widget-save error/rollback action.
- **Confirmed:** direct market/news/history loads do not share one dashboard loading/error state. See the [state matrix](widgets-and-data-flow.md#loading-empty-unavailable-and-error-behavior).
- **Inferred usability risk:** after all dynamic widgets are hidden, the owned dashboard template has no standalone manager launcher; widget menus are the confirmed entry point. Other shell/theme entry points need independent verification before claiming users cannot recover.
- **Confirmed:** the manager and parent filter runtime arrays before some saves; frontend state can omit filtered entries, while the backend matcher updates only submitted matching codes. Do not characterize this as deleting all omitted preferences.
- **Unknown:** deployed default widget order/static entries, RSS options, preference-service durability/partial-failure semantics and runtime reentry behavior.
- **Test gap:** the representative dashboard/manager specs only assert creation; they do not prove preference persistence or cross-tab behavior. See [test evidence](index.md#representative-tests-and-known-gaps).
- **Integration boundary:** frontend core navigation links these dashboard chapters. Main context, backend internals and global completion tracking remain separately maintained.

## Related documentation

[Widget contracts and endpoints](widgets-and-data-flow.md) | [Frontend state/API flow](../state-management-and-api-flow.md) | [Configuration and flags](../../cross-cutting/configuration-and-feature-flags.md) | [Preferences backend](../../backend/modules/uaf-preference.md) | [Saved-search/favorites flow](../../feature-flows/saved-searches-and-favorites.md) | [Search](../search/index.md)
