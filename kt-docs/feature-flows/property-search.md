# Property search: browser criteria to property results

[Project context](../project-context.md) | [Feature flows](index.md) | [Search UI](../frontend/search/index.md)

**Confirmed:** `a6f611ae0`, 2026-09-25. No runtime requests were sent.

## Preconditions and contract

Login must have supplied user preferences/templates and geography, with server session user/entitlement context available. The browser builds `SearchField[]`; see [dynamic forms](../frontend/search/dynamic-fields-and-lookups.md).

| Browser request | Server method | Output consumed |
| --- | --- | --- |
| `POST /api/quick-search` | `SearchController.quickSearch(QuickSearchInput)` | Flat search result mapped by `SearchMapper.toQuickSearchOutput` |
| `POST /api/my-search` | `SearchController.searchDispatcher(MySearchInput)` | `PropertySearchOutput.searchResultData` plus wrapper status |
| `POST /api/search-count` with a field array | `countSearchResults` | `{count: number}`, capped at 10,000 |
| `GET /api/get-type-ahead?address=...` | `getTypeAheadAddress` | `string[]`; backend mapping itself is method-unrestricted |

Source: `phoenix/src/app/search/services/search.service.ts:42-51,113-118`; `realist/web/src/main/java/com/facl/uaf/realist/rest/controller/SearchController.java:58-135`.

Sanitized request projections:

```typescript
// Quick Search; selected suggestions can instead send empty searchFields + propertyIdentifiers
{ searchFields: [{fieldCode:"APN", operatorValues:[{operator:"IS",values:["EXAMPLE-PARCEL"]}]}],
  displayFields: ["APN"], totalRecords: 100 }
// My Search effect adds these to criteria:
{ preferenceGroup: {/* common search preference metadata */},
  searchTemplate: {/* TMPL_MY_SEARCH_DEFAULT metadata */},
  selectedGeos: [{countyName:"Example County",fips:"00000",stateCode:"XX"}],
  searchFields: [], range:{}, exportResults:false, searchRequestType:"..." }
// Shared result projection:
{ propertySummaryList: [{propertyIdentifier:{/* structured property identity */},
                         propertyData:{/* field-code/value map */}}],
  totalRecords: 1, propSuggestion: false }
```

The examples show shape, not recommended live input; the server does not simply trust Quick Search’s displayed `totalRecords`/`displayFields` as its authoritative limit/field set.

## Execution sequence

```mermaid
sequenceDiagram
  actor User
  participant Form as Quick Search or My Search
  participant Effect as SearchEffects
  participant HTTP as Browser SearchService
  participant C as SearchController
  participant S as Server SearchService
  participant A as PropertySearchAction
  participant D as PropertySearchDelegate
  participant Provider as SmartSearchBD external boundary
  User->>Form: Submit valid criteria
  Form->>Effect: QuickSearch or MySearch
  Effect->>HTTP: Request with mode-specific metadata
  HTTP->>C: POST quick-search or my-search
  C->>C: Expand APN fields; My Search normalizes lender
  C->>S: quickSearch or mySearch
  S->>S: Preferences, geography, display fields, limits
  S->>A: Prepared PropertySearchInput
  A->>D: User/session context plus search input
  D->>D: Address parsing and suggestion geography restrictions
  D->>Provider: searchProperty
  Provider-->>D: SearchResultData
  opt First attempt returns null or zero records
    D->>D: Parse cloned input with fallback enabled
    D->>Provider: searchProperty again
    Provider-->>D: SearchResultData
  end
  D-->>A: Results or suggestions
  A-->>S: PropertySearchOutput
  S-->>C: Sorted result wrapper
  C-->>HTTP: Quick flat result or My Search wrapper
  HTTP-->>Effect: Result
  alt Single result and auto-open preference
    Effect->>User: Open property report
  else Limit reached
    Effect->>User: Limit dialog
  else Suggestions
    Effect->>User: Choose suggested properties
  else Ordinary results
    Effect->>User: Grid and map
  end
```

This is an in-process controller/service/action/delegate chain until the IFC boundary, not a chain of independently deployed UAF services. `ServiceHandler` supplies templates/preferences in server preparation; it is not inserted between every call.

## Normalization and preparation

1. `SearchController.updateApnSearchFields` adds missing APN/alternate/unformatted fields when APN or ALL_APN appears. Existing companion fields suppress duplicate additions. My Search and count additionally strip a lender display suffix `Label (Code)` only when `" ("` exists; a stray closing parenthesis is not enough.
2. `SearchService.prepareSearchFields` maps browser fields to SmartSearch fields and expands ALL_APN. The IFC setter is spelled `setOperaterValues`, unlike browser `operatorValues`.
3. Quick Search loads `TMPL_QUICK_SEARCH`, common search configuration, and provisioned geography on the server. My Search uses the effect-supplied preferences/template/geographies. Display fields come from grid/card templates and search-template leaf fields; My Search adds premium sell score.
4. Non-export limits come from `PREF_MAX_RECORDS_TO_SEARCH`; export preparation uses 3,000. Pending-record flags/month counts are separately applied. `PropertySearchTransformer` adds price-per-square-foot preferences and recursively extracts display fields.
5. Valid export ranges use a one-based inclusive user range converted to Java `subList(from-1,to)`; successful nonempty results are default-sorted.

Evidence: `SearchController.java:137-224`; `realist/web/src/main/java/com/facl/uaf/realist/service/SearchService.java:127-169,240-268,348-463`; `realist/web/src/main/java/com/facl/uaf/realist/util/PropertySearchTransformer.java:59-145`.

## Geography, matching, retries, and suggestions

`PropertySearchDelegate.search` adds the session MLS group and `KEYSTONE_MLS` source, rejects missing criteria/address, then calls `SmartSearchBD.searchProperty`. It normalizes MLS status/sell score and preprocesses results. An ordinary zero-result provider response gets an empty list.

Quick Search clones input before parsing. If identifiers were supplied (e.g. accepted suggestions), it groups them by FIPS and uses property-detail retrieval instead of text matching. Otherwise it parses and searches; a null/zero result causes one fallback attempt from cloned input. The fallback flag permits Google parsing under specific address conditions—it does **not** mean every retry necessarily invokes Google. Local parsing and GPL geocoding can still be selected.

My Search also parses/augments address city and ZIP, derives suggestion restrictions from entitled counties, applies spatial adjustment for non-multi-unit searches, requests MBC details, and retries a zero/null result with fallback parsing. Parsed addresses without mandatory fields return early with zero total. These are semantic retries, not a general HTTP retry/backoff policy.

Both modes trim suggestion lists to `maxSuggestRecords` (My Search excludes favorite mode from that trimming). Server `SearchService` can make an additional search when a suggestion contains exactly one property, updating geography when the auto-open preference is `Y`. The actual condition checks suggestion flag and size, not the “type 2” condition mentioned in its comment.

Evidence: `uaf-propertysearch/action/src/main/java/com/facl/uaf/propertysearch/action/delegate/PropertySearchDelegate.java:307-384,606-735,833-845,920-1024,1066-1125`; `realist/web/src/main/java/com/facl/uaf/realist/service/SearchService.java:171-238,270-289`.

## Count, errors, and visible branches

Count uses selected server-side provisioned geographies, pending-record preferences, `searchCountReq=true`, configured `maximum.records.search`, and a **direct delegate** `mySearch` call rather than `PropertySearchAction`. The browser debounces count for 2 seconds; `STARTS_WITH` requires a value length of at least two, while other operators with values pass the effect guard.

UI result handling compares returned **list length** with the current limit using `>=`; mobile uses 500, desktop uses the preference. It checks export and single-property branches first. Limit “show results” slices to the limit; refine emits no replacement results; export/labels start their own flows. Suggestion selection dispatches Quick Search with identifiers and empty criteria.

Exceptions are not identical to no data: delegate exceptions become `PropertySearchException`; `PropertySearchAction.mySearch` catches/logs and can return null; server preparation catches downstream failures and emits a generic search exception. Browser My Search dispatches failure, whereas Quick Search displays a ribbon and suppresses the failed emission.

Evidence: `SearchService.java:297-345`; `phoenix/src/app/store/search-board/search-board.model.ts:15-24`; `phoenix/src/app/store/search/search.effects.ts:128-165,183-224,247-445,573-587`; `uaf-propertysearch/action/src/main/java/com/facl/uaf/propertysearch/action/PropertySearchAction.java:222-263`.

## Debugging and boundaries

Compare four snapshots: extracted browser fields; HTTP payload; prepared `SmartSearchInData`; returned list/total/suggestion flag. Inspect APN expansion twice (controller and mapper preparation), selected versus entitled counties, and pending/export flags before attributing mismatches to provider ranking. Do not publish raw search/session logs in tickets.

Existing tests include `realist/web/src/test/java/com/facl/uaf/realist/rest/controller/SearchControllerTest.java` and `realist/web/src/test/java/com/facl/uaf/realist/service/SearchServiceUriTest.java`; focused frontend test caveats are in [dynamic forms](../frontend/search/dynamic-fields-and-lookups.md).

**Unknown:** SmartSearch internal matching, dataset freshness, deployed thresholds, and external geocoder behavior. Those implementations are outside this locally verified flow. Continue with [property-search module](../backend/modules/uaf-propertysearch.md), [results interactions](../frontend/search/results-grid-map-and-actions.md), and [export flow](exports-and-mailing-labels.md).
