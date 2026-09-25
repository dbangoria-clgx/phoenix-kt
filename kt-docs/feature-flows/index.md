# End-to-End Feature Flows

[Project context](../project-context.md) | [Frontend](../frontend/index.md) | [Backend feature map](../backend/api-and-feature-map.md)

## How to use this section

Choose the user action you want to explain. Each walkthrough follows its concrete frontend trigger, HTTP contract, server processing, data or provider boundary, response, and visible result. Read the actual branch conditions; a generic "controller -> service -> database" model is not sufficient here.

| User action | Walkthrough | Critical distinction |
| --- | --- | --- |
| Enter the application, restore or end a session | [Login and session lifecycle](login-and-session-lifecycle.md) | Incoming login, runtime user hydration and outgoing API credentials are different |
| Search for properties | [Property search](property-search.md) | Dynamic templates, normalization and retries precede/shape results |
| Save and revisit work | [Saved searches and favorites](saved-searches-and-favorites.md) | Templates are not saved property collections |
| Inspect property/report information | [Property details and reports](property-details-and-reports.md) | Provider data can be transformed through templates/XML before rendering |
| Download a PDF | [PDF generation and download](pdf-generation-and-download.md) | Preparation/cache/link and later download/rendering are separate requests |
| Export or prepare mailing output | [Exports and mailing labels](exports-and-mailing-labels.md) | Data may be re-fetched and transformed rather than copied from the visible grid |
| Explore a map and identify a property | [Maps and property lookup](maps-and-property-lookup.md) | Browser imagery and server JSON/property operations have different paths |
| Purchase content or consume report credits | [Cart, checkout and credits](cart-checkout-and-report-credits.md) | Remote active cart, local saved items and report accounting are distinct |
| Create and email a shared report link | [Shared report links](shared-report-links.md) | Creation/email/cleanup are traced; a recipient reader and its access enforcement were not found locally |

## Questions to ask at every boundary

1. Which exact component, effect or direct-service call starts the operation?
2. Which method/path does the browser send, and which mapping/filter accepts it?
3. Which input values come from the browser, user session or server configuration?
4. What is normalized, enriched, cached, persisted or counted?
5. Does the code execute locally or cross into an external implementation?
6. What does the user see for success, empty results, denied access, limits and failure?

## Reading the diagrams

Sequence diagrams describe source-backed steps, not measured network timing. Alternate paths are shown only where code establishes them. An external participant is a stopping boundary for local evidence, not a license to invent its internal workflow.

Use [report availability](../frontend/reports/availability-and-access-rules.md) for per-report conditions and [external integrations](../cross-cutting/external-integrations.md) for shared provider contracts.

## Source navigation

The [API/feature map](../backend/api-and-feature-map.md) connects browser operations to controllers and module owners. Each walkthrough owns its detailed source references and uncertainty statements so those explanations are not duplicated in module pages.
