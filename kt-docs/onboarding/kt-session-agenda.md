# 90-Minute KT Session Agenda

[Project context](../project-context.md) | [Onboarding](index.md) | [First-week plan](first-week-learning-plan.md)

## Goal and preparation

At the end of the session, the newcomer should be able to locate a feature, explain its major boundaries and name the next source files to read. This is a proposed teaching agenda, not a claim about the team's existing ceremony.

The presenter should review the [completion checkpoint](../completion-status.md) and [known gaps](known-gaps-and-team-questions.md), open the repository at the documented revision, and prepare only sanitized examples. The session can be conducted entirely through source and diagrams; a live environment is not required.

## Agenda

| Minutes | Topic | Material and activity | Expected understanding |
| --- | --- | --- | --- |
| 0-10 | Product and terminology | [Business overview](../overview/business-overview.md), [glossary](../overview/glossary.md); narrate a property-research journey | User goals and differences among searches, properties and reports |
| 10-20 | Architecture and repository | [System architecture](../overview/system-architecture.md), [repository map](../overview/repository-and-technology-map.md) | Browser apps, one web host, libraries, local state and providers |
| 20-35 | Frontend navigation | Walk [Dashboard](../frontend/dashboard/index.md), [Search](../frontend/search/index.md), [Property Intelligence](../frontend/property-intelligence/index.md) and [state](../frontend/state-management-and-api-flow.md) | Routes, runtime configuration, NgRx and direct-service exceptions |
| 35-55 | One complete search | Follow the [property-search sequence](../feature-flows/property-search.md) into [backend modules](../backend/index.md); contrast saved templates/favorites | How a user action becomes a contract and returns as UI data |
| 55-70 | Reports and commercial access | [Catalogue](../frontend/reports/report-catalogue.md), [availability matrix](../frontend/reports/availability-and-access-rules.md), [PDF](../feature-flows/pdf-generation-and-download.md), [credits](../feature-flows/cart-checkout-and-report-credits.md) | Type, data coverage, entitlement and purchase/usage are separate |
| 70-80 | Development and troubleshooting | [Setup](../development/local-setup.md), [delivery](../development/build-and-deployment.md), [troubleshooting](../development/troubleshooting.md) | Prerequisites, actual artifact boundaries and debugging entry points |
| 80-90 | Teach-back and next steps | Use questions below; choose day-one reading from [first week](first-week-learning-plan.md) | Identify misunderstandings and unanswered team questions |

Total: **90 minutes**.

## Teach-back questions

1. A user can see a report in navigation but cannot retrieve it. Which distinct conditions would you investigate?
2. Which source proves a module is included in the build? Does that prove it has active code or its own deployment?
3. Where does a search field's control type and lookup behavior come from?
4. Why can a dashboard request bypass the usual action/effect path?
5. What is saved when a user saves a search, compared with a favorite property?
6. Why might an export re-fetch data instead of serializing the displayed grid?
7. What happens between preparing a PDF link and receiving PDF bytes?
8. Which local stores persist application state, and where does external property data enter?
9. How would you distinguish inbound SAML login from outbound OAuth API access?
10. If the documentation run stops halfway, which file gives the next task?

Answers should cite the relevant chapter/source rather than rely on memorized labels.

## Optional live demonstration boundary

Only if the team supplies an authorized development environment, a presenter may demonstrate a safe login, search and read-only report. This documentation task does not authorize executing those actions, purchasing content, sending email, creating share links, changing data or testing against production.

## Follow-up ownership

Record unresolved product, identity, provider and platform questions through the [known-gaps register](known-gaps-and-team-questions.md). Do not replace a missing answer with a plausible architectural assumption.
