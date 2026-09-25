# First-Week Learning Plan

[Project context](../project-context.md) | [Onboarding](index.md) | [Code-reading path](code-reading-path.md)

## Working agreement

This is a recommended learning plan. Exercises are source-reading and diagram-tracing activities; they do not require code edits, service startup, external API calls, purchases or database changes.

Use the [glossary](../overview/glossary.md) and [known gaps](known-gaps-and-team-questions.md) instead of guessing unfamiliar terminology or deployment behavior.

## Day 1: Product and boundaries

Read the [business overview](../overview/business-overview.md), [architecture](../overview/system-architecture.md), [repository map](../overview/repository-and-technology-map.md), and [setup prerequisites](../development/local-setup.md).

**Exercise:** draw a small architecture map from memory, then identify which boxes are browser apps, one Java process, library modules, local stores, and external providers. Find the build evidence for each classification.

**Outcome:** explain what the product does and why starting a local database is not equivalent to having all provider-backed features available.

**Questions:** Where are the two Angular entry points? Which task packages their assets? Which current configuration facts differ from older documentation?

## Day 2: Frontend feature structure

Read [routing](../frontend/bootstrap-and-routing.md), [state/API flow](../frontend/state-management-and-api-flow.md), [Dashboard](../frontend/dashboard/index.md), and [dynamic fields](../frontend/search/dynamic-fields-and-lookups.md).

**Exercise:** trace one Dashboard widget from its registration to its request and template. Then choose a search control and identify how metadata determines its renderer, values and validation.

**Outcome:** distinguish route loading, user configuration, state slices, effect families and direct-service paths.

**Questions:** Is each effects directory a state slice? What cleanup/change-detection pattern does the chosen component actually use? Which values arrive at runtime rather than from `environment.ts`?

## Day 3: Backend and one cross-layer request

Read [backend architecture](../backend/application-architecture.md), [dependencies](../backend/module-dependencies.md), [search module](../backend/modules/uaf-propertysearch.md), [search flow](../feature-flows/property-search.md), and [saved items](../feature-flows/saved-searches-and-favorites.md).

**Exercise:** identify the exact frontend HTTP method/path, backend mapping, input DTO, transformations, delegate contract and output mapping for one search branch. Locate an alternate/empty-result branch.

**Outcome:** explain actual call chains without inserting nonexistent layers or external implementation details.

**Questions:** Where is APN normalization performed? Where does local code end? How does a saved search differ from a list of favorites? Which module has a similarly named delegate in another package?

## Day 4: Reports, analytics and data

Read [Property Intelligence](../frontend/property-intelligence/index.md), [report catalogue](../frontend/reports/report-catalogue.md), [availability](../frontend/reports/availability-and-access-rules.md), [PDF](../feature-flows/pdf-generation-and-download.md), [commerce/credits](../feature-flows/cart-checkout-and-report-credits.md), and [data ownership](../cross-cutting/database-and-migrations.md).

**Exercise:** compare two report families: identify their codes, inputs, provider/data conditions, entitlement logic, paid/credit conditions and unavailable UI. Then separate the PDF preparation request from the download request.

**Outcome:** avoid conflating route existence, permission, provider coverage and commercial access.

**Questions:** Is an analytics page the same thing as a similarly named report? Which local entities account for usage? Which relationships are actual foreign keys versus logical references?

## Day 5: Operability and a safe change plan

Read [security](../cross-cutting/authentication-and-authorization.md), [configuration](../cross-cutting/configuration-and-feature-flags.md), [Redis](../cross-cutting/redis-caching-and-sessions.md), [delivery](../development/build-and-deployment.md), [testing](../development/testing-and-quality.md), and [common changes](../development/making-common-changes.md).

**Exercise:** prepare a no-code change-impact outline for adding a supported search field or changing a report section. Name affected metadata/components/contracts/modules, relevant existing tests, possible access/data effects, and information needed from an owner.

**Outcome:** locate the right layers before implementing and state what cannot be proven without authorized runtime evidence.

**Questions:** Does the configured build task actually run frontend tests? Which profile names select the inspected security chains? Does a Jenkins build pod establish the application hosting format?

## End-of-week teach-back

Explain one full feature using the [flow index](../feature-flows/index.md). A successful explanation includes user goal, actual source chain, data ownership, one alternate/failure path, and a precise external evidence boundary.

Bring remaining questions to the [gap register](known-gaps-and-team-questions.md), not to an invented explanation.
