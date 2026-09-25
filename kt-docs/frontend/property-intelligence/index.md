# Property Intelligence

[Project context](../../project-context.md) | [Frontend](../index.md)

**Confirmed baseline:** `RP-10188`, `a6f611ae0`, 2026-09-25.

Property Intelligence (PI) compares geographic housing statistics rather than returning individual matching properties. Users choose state, metro, county, or ZIP geography, then inspect tax-market, MLS-listing, rental, historical home-price-index, or forecast charts.

Start with [features and navigation](features-and-navigation.md), then [analytics and data flow](analytics-and-data-flow.md).

## What not to confuse

- PI’s named “My Search” comparisons are geographic filter preferences, **not** the property-search templates described in [saved searches](../../feature-flows/saved-searches-and-favorites.md).
- The PI Market Trends page is not simply the property-level [Market Trends report](../reports/report-catalogue.md).
- **HPI** means Home Price Index; forecast series are distinct from historical HPI.
- **CBSA** means Core-Based Statistical Area, presented as Metro in the filter UI.
- Browser chart requests are REST. GraphQL/DGS is used by an **outbound Java analytics client**, not by these Angular components as an inbound application GraphQL API.

## Source reading order

1. `phoenix/src/app/property-intelligence/property-intelligence-routing.module.ts:11-46` — six child pages.
2. `phoenix/src/app/property-intelligence/components/property-intelligence-filters/property-intelligence-filters.component.ts:50-85,143-280` — geography and named comparisons.
3. `phoenix/src/app/property-intelligence/services/property-intelligence.service.ts:21-62` — REST contracts.
4. `realist/web/src/main/java/com/facl/uaf/realist/rest/controller/PropertyIntelligenceController.java:32-83` — matching server mappings.
5. `realist/web/src/main/java/com/facl/uaf/realist/rest/service/PropertyIntelligenceService.java:342-664` — response assembly.
6. `uaf-common/action/src/main/java/com/facl/uaf/common/shared/service/MarketTrendsService.java:125-240` — external data boundary.

**Limits of evidence:** no runtime verification, provider calls, builds, or tests were performed. Effective flags/PIN membership, dataset coverage, and provider statistical definitions are external facts. Source-level no-data assumptions and HPI label/calculation discrepancies are explicitly recorded in the detailed pages.

**Integration note:** report, backend, and cross-cutting links follow the prompt's planned tree; some targets remain pending with their respective owners. The three PI pages are complete as source-researched documentation, not a runtime certification.
