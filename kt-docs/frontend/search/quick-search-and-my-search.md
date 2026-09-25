# Quick Search and My Search

[Project context](../../project-context.md) | [Search](index.md)

**Confirmed against:** `a6f611ae0`, 2026-09-25.

## What the two modes mean

Quick Search is the smaller, configured starting form, not a hard-coded universal set of address fields. My Search lets users choose/customize a form, reopen saved criteria, combine repeated operators, and include map geometry or premium-layer criteria.

| Concern | Quick Search | My Search |
| --- | --- | --- |
| Metadata | `TMPL_QUICK_SEARCH` | Selected template; initial/default metadata includes `TMPL_MY_SEARCH_DEFAULT` |
| Local controls | Each field is a `FormArray` of operator/value groups | Same basic structure, with special handling for multi-values, dates, and map shapes |
| Submit action | `QuickSearch` | `MySearch` |
| HTTP | `POST /api/quick-search` | `POST /api/my-search` |
| Response consumed | `SearchResultData` | Wrapper containing `searchResultData` |
| Retention | `SaveValuesOfQuickSearch`; successful execution schedules local recent-search persistence | `SaveValuesOfMySearch`; explicit save persists a template |

Evidence: `phoenix/src/app/search/components/quick-search/quick-search.component.ts:60-165`; `phoenix/src/app/search/components/my-search/my-search.component.ts:126-169,214-359`; `phoenix/src/app/store/search/search.effects.ts:103-232`.

## Quick Search lifecycle

`ngOnInit` combines the runtime template with previously saved form values, builds a field-code metadata map, and avoids resetting an already-created control on each store emission. Dashboard navigation can supply `history.state.address`; the component consumes that state and performs the search after its count subscription fires.

`search()` clears “show selected,” extracts nonempty criteria, dispatches the request with `DISPLAY_FIELDS` and `TOTAL_RECORDS`, resets the current report index, clears retained My Search values, and retains Quick Search values. Owner input in `Last, First` form becomes `Last First` for execution, but not when `getSearchFields(true)` saves it.

Clear removes values while retaining operators. Multi-select lookup controls become empty arrays; ordinary controls become empty strings. Component destruction saves values back to NgRx; that alone is not server persistence.

Evidence: `quick-search.component.ts:48-59,110-214,248-304` under `phoenix/src/app/search/components/quick-search/`.

## My Search lifecycle

`getDataFromTemplate` combines selected-template metadata, current controls, and drawn shapes. It:

- Expands lookup selections into separate operator/value entries and removes display decoration from lookup tokens.
- Formats `Date` values as `yyyyMMdd`.
- Expands `ALL_APN` into APN, alternate APN, and unformatted APN criteria.
- Replaces a template MAP criterion with current drawn shapes, using `operator: "POLYGON"`.
- Adds a selected premium criterion; consecutive premium `BETWEEN` buckets can be merged before execution.

`search()` dispatches criteria, not the whole selected template. The effect separately supplies **the default My Search template**, common search preferences, selected geographies, range/export defaults, and request-purpose metadata. This distinction matters when debugging a saved template: the selected template determines browser fields, while the effect’s `searchTemplate` is selected with `TMPL_MY_SEARCH_DEFAULT`.

`openSearch` rebuilds only for a changed render key, explicit clear, or changed field-code structure. The key includes template code, label, type, default operators, and operator values. Form rebuilds terminate the old form subscription. Immediate validity/empty-state streams support OnPush button updates; count extraction is debounced.

Evidence: `phoenix/src/app/search/components/my-search/my-search.component.ts:214-359,510-607,725-855,888-967,1001-1047`; `phoenix/src/app/store/search/search.effects.ts:103-122`.

## Geography, premium access, and counting

Selected counties are separate state, not an address-string inference in the browser. My Search clears premium selection when multiple counties are selected. Premium controls require search preference value not equal to `N` and map-layer `hasAccess`; zoom and single-county restrictions are additional conditions, not substitutes for server authorization.

Form changes dispatch `GetSearchResultsCount`; the effect can clear the count rather than send insufficient fields. Count requests return `{count: number}` and do not load grid rows. The server caps the displayed count at 10,000; this differs from the result limit and export limit.

Evidence: `my-search.component.ts:105-194,543-605`; `phoenix/src/app/store/search/search.effects.ts:247-263`; `realist/web/src/main/java/com/facl/uaf/realist/rest/controller/SearchController.java:108-119`.

## Outcomes and debugging

Both effects warn on an empty property list. My Search malformed/error results dispatch `SearchFailure`; Quick Search errors show the same generic ribbon but filter out a falsy emission rather than dispatching that action. Both spinner-wrapped requests accept the cancel-My-Search action. Successful Quick Search writes recent searches under browser `localStorage.recentSearches`.

Result processing prioritizes export requests, then the auto-open-single-property preference, then list-length limit handling, then suggestions/ordinary success. See [results behavior](results-grid-map-and-actions.md) rather than assuming every successful HTTP request immediately populates the grid.

Useful breakpoint order:

1. `getSearchFields` / `getDataFromTemplate`: check raw and submitted values.
2. `SearchEffects.executeQuickSearch$` / `executeMySearch$`: check attached template/geography.
3. Browser `SearchService`: verify the actual payload, not just the component model.
4. [Server search sequence](../../feature-flows/property-search.md): inspect normalization, retries, and provider output.

For save/reopen semantics, use [saved searches and favorites](../../feature-flows/saved-searches-and-favorites.md). **Unknown:** deployed field lists and premium entitlement values; they cannot be established from these component definitions.
