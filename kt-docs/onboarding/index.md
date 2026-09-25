# Onboarding and KT

[Project context](../project-context.md) | [Glossary](../overview/glossary.md) | [Completion status](../completion-status.md)

## Start with a mental model

Learn the user's goal before the implementation. First distinguish the browser applications, one backend host, in-process libraries, local application state and external property/provider systems. Then follow one user action all the way through.

| Resource | Use it for |
| --- | --- |
| [90-minute KT agenda](kt-session-agenda.md) | Running a focused handoff session |
| [First-week learning plan](first-week-learning-plan.md) | Turning the initial overview into practical familiarity |
| [Code-reading path](code-reading-path.md) | Reading important files in dependency order |
| [Known gaps and team questions](known-gaps-and-team-questions.md) | Getting evidence that source alone cannot provide |
| [Local setup](../development/local-setup.md) | Understanding prerequisites and defined commands |
| [Making common changes](../development/making-common-changes.md) | Locating the appropriate frontend/backend/data layers |

## What a newcomer should be able to explain

- Why Dashboard, Search and Property Intelligence are different areas.
- Why two users may see different fields, navigation or reports.
- Why saved searches and favorites use different models.
- Why a UAF Gradle module is not automatically a microservice.
- Why report existence, permission, provider coverage and purchase/credits are separate.
- How one browser request is mapped into backend code and an external boundary.
- Where local PostgreSQL and Redis fit without assuming they own all property data.
- Which facts remain unknown and which team can provide them.

## Suggested learning exercise

Without running services, choose Quick Search and create a personal verbal trace from `QuickSearchComponent.search` to the response shown in the grid. Compare your trace with the [search walkthrough](../feature-flows/property-search.md). Then repeat for the [PDF flow](../feature-flows/pdf-generation-and-download.md), noting why it has a preparation and a download phase.

This is a reading exercise, not an instruction to execute production requests or modify source.

## Evidence and scope

This learning plan is a recommended order, not a description of an existing team onboarding policy. Technical explanations are sourced by the linked chapters. Production access, ownership assignments and commercial semantics need the [team questions](known-gaps-and-team-questions.md).
