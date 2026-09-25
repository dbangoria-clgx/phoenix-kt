# Realist Phoenix: Complete KT Documentation Generation Prompt

## Role and Objective

Act as a senior solution architect, full-stack engineer, repository researcher, and technical onboarding specialist.

Deeply investigate the current Realist Phoenix repository and create a complete, source-backed knowledge-transfer documentation set for a new developer who has no prior knowledge of this project.

Explain the business purpose, architecture, frontend areas, backend modules, features, reports, data, integrations, and execution flows through clear explanations, source references, and Mermaid diagrams.

Execute this prompt: research the implementation and write the documentation. Do not stop at an outline, proposed file tree, or generic framework explanation.

## Deliverables and Scope

- Write the generated documentation under `kt-docs\`, not `docs\`.
- Use `kt-docs\project-context.md` as the main entry point and navigation hub.
- Use `kt-docs\completion-status.md` as a separate persistent progress and resumption checkpoint.
- Split detailed content into appropriately scoped, linked Markdown documents. Do not put everything in one large file.
- Preserve `kt-docs\kt-generation-prompt.md`; it is the instruction artifact, not a generated chapter.
- If generated documents already exist, read their progress and content before updating them. Preserve useful, still-valid work rather than blindly replacing it.
- Create or update only the generated documentation under `kt-docs\`. Do not modify application source, configuration, tests, or unrelated files.
- Do not install dependencies, execute builds or tests, start services, contact external systems, or deploy anything for this documentation task.
- Describe existing development commands from their definitions. Do not imply they were executed or succeeded.

## Audience and Teaching Approach

Assume the reader understands basic programming but does not know this product, repository, business vocabulary, architecture, or internal services.

Teach in this order:

1. What the product does and why users need it.
2. The overall system and its boundaries.
3. Where responsibilities live in the repository.
4. Frontend and backend architecture.
5. Concrete user actions traced through the system.
6. Local development, troubleshooting, and common changes.

Explain business behavior before listing classes. Define acronyms on first use; do not invent expansions for internal terms such as UAF or FARES when their meanings are not established.

Distinguish business features, source directories, Gradle modules, runtime processes, local libraries, and external services. Describe actual implementation, including exceptions, rather than an idealized architecture.

## Research and Evidence Rules

1. Use current source, routes, imports, build files, dependency declarations, configuration consumers, entities, and migrations as primary evidence.
2. Treat README files, existing documentation, and `_bmad-output\project-context.md` as research leads, not unquestionable current truth.
3. Cite material explanations using repository-relative paths, symbols, and line numbers. Record the investigated revision or research date.
4. Label meaningful uncertainty as **Confirmed**, **Inferred**, or **Unknown**. Explain the evidence behind inferences.
5. Trace frontend callers and backend handlers before claiming an end-to-end connection.
6. Stop at external implementation boundaries. Do not invent downstream databases, API paths, transports, deployment topology, or vendor internals.
7. Do not assume every frontend request follows NgRx or every backend operation passes through `ServiceHandler`.
8. Do not describe each `uaf-*` directory as an independently deployed microservice.
9. Establish active/build/runtime status from evidence, not names or leftover build output.
10. Exclude generated, vendor, dependency, and build directories from primary investigation unless needed to explain a specific boundary.
11. Report declared dependency versions separately from resolved or installed versions.
12. Read migrations cumulatively; an object in an early migration may have been changed or removed later.
13. Record documentation contradictions and unreviewed areas explicitly. Do not call representative research exhaustive.
14. Never include credentials, private keys, tokens, cipher blobs, real customer information, payment details, or sensitive configuration values.
15. Describe configuration key names and sanitized request/response structures. Do not inspect user-home credential files or keystore/private-key contents.
16. Keep current behavior separate from recommendations. This task is documentation, not implementation or a speculative redesign.
17. If using parallel researchers, assign non-overlapping areas, require source-backed findings, and persist useful results in the documentation/checkpoint. Do not rely on transient agent state to resume.

## Repository Research Starting Points

Start with these areas, then follow real dependencies, callers, configuration references, and data transformations:

- `settings.gradle`, root `build.gradle`, module `build.gradle` files, and the Gradle wrapper configuration.
- Root and frontend README files, `Jenkinsfile`, `manifests\`, and existing project documentation.
- `phoenix\package.json`, `phoenix\angular.json`, and `phoenix\proxy.config.mjs`.
- `phoenix\src\main.ts`.
- `phoenix\src\app\app.module.ts`.
- `phoenix\src\app\app-routing.module.ts`.
- `phoenix\src\app\app-state.module.ts`.
- `phoenix\src\app\store\` and the actual feature directories.
- `phoenix\projects\user-guide\`.
- `realist\web\src\main\java\com\facl\uaf\realist\`.
- `realist\web\src\main\resources\`, including configuration declarations and database migrations.
- Every `uaf-*\action` module's source and build definition.
- Relevant existing test source and configuration, without executing tests.

## Documentation Structure

Use this structure as the starting point. Add or split pages when actual complexity warrants it. Do not create empty placeholders or imply a candidate feature is active without evidence.

```text
kt-docs/
|-- kt-generation-prompt.md
|-- project-context.md
|-- completion-status.md
|-- overview/
|   |-- business-overview.md
|   |-- system-architecture.md
|   |-- repository-and-technology-map.md
|   `-- glossary.md
|-- frontend/
|   |-- index.md
|   |-- bootstrap-and-routing.md
|   |-- state-management-and-api-flow.md
|   |-- shared-components-and-ui-patterns.md
|   |-- feature-map.md
|   |-- user-guide-application.md
|   |-- dashboard/
|   |   |-- index.md
|   |   |-- widgets-and-data-flow.md
|   |   `-- configuration-and-personalization.md
|   |-- search/
|   |   |-- index.md
|   |   |-- quick-search-and-my-search.md
|   |   |-- dynamic-fields-and-lookups.md
|   |   `-- results-grid-map-and-actions.md
|   |-- property-intelligence/
|   |   |-- index.md
|   |   |-- features-and-navigation.md
|   |   `-- analytics-and-data-flow.md
|   `-- reports/
|       |-- index.md
|       |-- report-catalogue.md
|       |-- availability-and-access-rules.md
|       `-- report-rendering-and-actions.md
|-- backend/
|   |-- index.md
|   |-- application-architecture.md
|   |-- module-dependencies.md
|   |-- api-and-feature-map.md
|   `-- modules/
|       |-- realist-web.md
|       |-- uaf-common.md
|       |-- uaf-useraccess.md
|       |-- uaf-preference.md
|       |-- uaf-propertysearch.md
|       |-- uaf-map.md
|       |-- uaf-reports.md
|       |-- uaf-activity.md
|       |-- uaf-faresmodel.md
|       `-- uaf-support-status.md
|-- feature-flows/
|   |-- index.md
|   |-- login-and-session-lifecycle.md
|   |-- property-search.md
|   |-- saved-searches-and-favorites.md
|   |-- property-details-and-reports.md
|   |-- pdf-generation-and-download.md
|   |-- exports-and-mailing-labels.md
|   |-- maps-and-property-lookup.md
|   |-- cart-checkout-and-report-credits.md
|   `-- shared-report-links.md
|-- cross-cutting/
|   |-- index.md
|   |-- authentication-and-authorization.md
|   |-- database-and-migrations.md
|   |-- redis-caching-and-sessions.md
|   |-- external-integrations.md
|   `-- configuration-and-feature-flags.md
|-- development/
|   |-- index.md
|   |-- local-setup.md
|   |-- build-and-deployment.md
|   |-- testing-and-quality.md
|   |-- troubleshooting.md
|   `-- making-common-changes.md
`-- onboarding/
    |-- index.md
    |-- kt-session-agenda.md
    |-- first-week-learning-plan.md
    |-- code-reading-path.md
    `-- known-gaps-and-team-questions.md
```

An overview page may serve as a folder index; add indexes when needed for navigation. Add dedicated pages for other substantial discovered frontend or backend features rather than squeezing them into an unrelated chapter.

## Persistent Completion Tracking and Resumption

### Initialize Before Detailed Research

Create or load `kt-docs\completion-status.md` before substantial investigation.

It must record:

- Overall documentation status.
- Repository branch/revision and any relevant working-tree caveat.
- Last checkpoint date.
- Current phase and active task.
- Planned output files and meaningful research/writing tasks.
- Dependencies between tasks.
- Known blockers and their impact.
- The exact next action required to resume.

Maintain a task table with these columns:

| Task ID | Area | Output file | Status | Dependencies | Evidence/progress | Remaining work | Blocker | Next action |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |

Use stable task IDs, such as `FE-DASHBOARD`, `FE-SEARCH`, `FE-PROPERTY-INTELLIGENCE`, `BE-PROPERTYSEARCH`, `REPORT-CATALOGUE`, `REPORT-AVAILABILITY`, `FLOW-QUICK-SEARCH`, and `LINKS-REVIEW`.

Allowed statuses:

- `NOT_STARTED`: work has not begun.
- `IN_PROGRESS`: active research or documentation.
- `BLOCKED`: progress requires unavailable evidence, access, or a decision.
- `COMPLETE`: required content, evidence, diagrams, and navigation are finished.

Track meaningful completion, not merely whether a file exists. For large areas, separate research, writing, diagrams, and cross-linking into subtasks. Keep related documentation and diagram coverage visible.

### Checkpoint Rules

Update the progress document:

1. Before starting a task.
2. After a meaningful research or documentation milestone.
3. Before switching to another area.
4. Immediately when blocked.
5. Before ending the run.

Do not defer all checkpoint updates until the end.

For partial work, preserve files/symbols examined, confirmed findings and references, the partial output location, unanswered questions, and the next exact file/symbol/call chain to inspect. Record enough durable context for another session to continue without repeating the investigation.

Use written findings and stable paths, not conversation memory, temporary files, or transient agent IDs.

### Blocker Handling

When blocked:

1. Mark the affected task `BLOCKED`.
2. Explain the precise blocker and what it prevents.
3. Identify the evidence, access, or decision needed.
4. Preserve completed work and references.
5. Continue independent tasks whose dependencies are satisfied.

Do not silently skip required sections or label blocked work complete. Distinguish a correctly documented external boundary from missing research in locally available code.

### Resume Protocol

At the beginning of every run:

1. Read `completion-status.md` if present.
2. Inspect the partial documents and checkpoint references.
3. Check whether the revision or relevant source has changed.
4. Reassess affected findings and reopen invalidated tasks where necessary.
5. Resume the first actionable incomplete task with satisfied dependencies.
6. Preserve completed, still-valid documentation.

Maintain a final **Resume Here** section with the active task ID, last completed step, exact next action, relevant source/output paths, and outstanding blockers.

## Main Project Context

Keep `kt-docs\project-context.md` concise and useful as the entry point. Include:

1. Purpose, audience, research date/revision, and scope limitations.
2. A five-minute explanation of the product and system.
3. One readable high-level Mermaid architecture diagram.
4. Frontend, backend, data, and integration responsibility summaries.
5. A navigation table: section, what the reader will learn, and starting page.
6. Reading paths for a complete newcomer, frontend developer, backend developer, and someone tracing a feature end to end.
7. Links to setup, glossary, KT agenda, known gaps, and completion status.
8. A clickable contents list where useful.

Do not duplicate detailed module and feature explanations here.

## Required Technical Coverage

### 1. Business Overview, Architecture, and Repository Map

Explain the product's purpose, supported user types, access differences, and principal journeys: search, maps, property details, reports, saved items, exports, and purchases.

Define domain terms such as MLS, APN, FIPS, CLIP, templates, preferences, entitlements, and report credits when they appear. Explain which responsibilities belong to the application and which belong to providers.

Explain:

- The main Phoenix Angular application and separate User Guide application.
- The Spring Boot application host and frontend packaging.
- In-process UAF libraries versus external services.
- Property-data, preference, identity, mapping, commerce, PDF, and analytics boundaries.
- PostgreSQL and the distinct Redis responsibilities.
- Development topology versus packaged/deployed topology.
- The relationship between Gradle, npm, Angular builds, and executable artifacts.

Provide an annotated repository tree, a responsibility/entry-point table, and a technology map based on current declarations.

Include a user-journey flowchart, system-context diagram, and runtime-boundary diagram. Distinguish in-process calls, HTTP calls, browser redirects, and storage access.

### 2. Frontend Architecture and Shared Patterns

Document bootstrap, root shell, routing, eager/lazy loading, report component loading, guards, resolvers, navigation, and the separate User Guide application.

Inventory all substantial feature areas, including login, dashboard, search, maps/grids, saved items, reports, Property Intelligence, exports/labels/postcards, settings, orders/cart, Direct-to-Agent, sharing, branding, and help where implemented.

Explain:

- Actual registered NgRx state slices, not merely store folder names.
- Actions, effects, reducers, selectors, and feature services.
- Direct-service and other non-NgRx paths.
- HTTP setup, interceptors, development proxying, and direct browser integrations.
- Loading, cancellation, errors, session restoration/timeout, and browser storage.
- Runtime user/configuration data versus build-time environment configuration.
- Shared components, templates, forms, inputs/outputs, RxJS cleanup, change detection, styling, and accessibility patterns using representative source.

Inspect representative component TypeScript, templates, styles, and existing tests rather than inferring component behavior from names.

Include route/feature hierarchy and state/request-flow diagrams that show important bypass paths.

### 3. Dashboard: Dedicated Frontend Section

Do not reduce Dashboard to a feature-map row.

Explain entry routes, shell, components, widget registration, widget purpose, data sources, and navigation into search or reports.

Cover saved searches, saved properties, recently viewed properties, news, and other discovered widgets. Explain layout, personalization, preference persistence, direct-service calls versus NgRx, and loading/empty/unavailable/error states.

Include a widget-composition diagram and a representative data-flow sequence.

### 4. Search: Dedicated Frontend Section

Explain Quick Search versus My Search, search templates, fields, operators, lookups, validation, geography, submission, backend processing, and result rendering.

Trace:

`Login/configuration response -> user state -> templates and field metadata -> render identifiers/operators/lookups/validators -> form controls`

Explain grid/map synchronization, selected properties, result limits, suggestions, empty/error outcomes, saving searches versus favorites, and actions such as reports, export, labels, and purchases.

Explain why fields, widgets, navigation, and report access may differ by user or MLS group.

Include dynamic-form, results-interaction, and end-to-end search diagrams.

### 5. Property Intelligence: Dedicated Frontend Section

Explain landing page/navigation, components, filters, geography, periods, charts, metrics, services, backend endpoints, and external data boundaries.

Investigate market trends, listing trends, rental trends, HPI, and HPI forecasts. Include only source-supported features and clarify their relationships.

Trace response transformations into rendered charts. Explain feature/access conditions and loading, no-data, unavailable, and error states.

Include a feature-navigation diagram and an analytics data-flow diagram.

### 6. Backend Architecture and Every Module

Explain the application entry point and actual responsibilities of controllers, services, facades, actions, delegates, clients, DTOs, mappers, and converters.

Explain `ServiceHandler` where used, but do not insert it into unrelated flows.

Provide a dedicated page for:

- `realist:web`
- `uaf-common:action`
- `uaf-useraccess:action`
- `uaf-preference:action`
- `uaf-propertysearch:action`
- `uaf-map:action`
- `uaf-reports:action`
- `uaf-activity:action`
- `uaf-faresmodel:action`

Investigate `uaf-support` separately and establish its build inclusion and possible historical/manual role without assuming runtime participation.

For every module document:

- Business purpose and technical responsibility.
- Build inclusion, source presence, and runtime relevance.
- Important packages, classes, methods, and entry points.
- Features supported and known callers.
- Direct module dependencies and external artifacts.
- Main input/output contracts and transformations.
- Local processing versus remote operations.
- Data/session/cache interactions where relevant.
- Constraints, extension/debugging points, and unknowns.

Include a verified Gradle dependency diagram. Keep dependency edges distinct from runtime call edges. An included but apparently unused/source-empty module must be described accurately, not assigned an invented purpose.

### 7. Report Catalogue, Types, and Availability

Provide a detailed report catalogue and a dedicated availability/access explanation, not just a list of names.

Investigate these candidate report families and discover additional types from source:

- Property Details.
- Comparables and Comparable Details.
- Neighbors.
- Neighborhood Profile.
- Assessor Maps.
- Zoning Maps.
- Standard Flood.
- Premium Flood.
- Foreclosure.
- Market Trends.
- Building Sketch.
- Hazard.
- Community Insights.
- Building Permits.

Verify names, codes, routes, implementation status, and whether each is a separate report, variant, or embedded section. Distinguish similarly named analytics pages from report types.

#### Report Catalogue

For each report document:

- User-facing name and purpose.
- Internal report code/type.
- Frontend route and entry component.
- Backend endpoint and owning service/module.
- Required identifiers and other inputs.
- Major visible sections, data sources, and transformations.
- Supported actions: view, PDF, print, share, purchase, or export.
- Source evidence and links to related execution flows.

Provide a comparison table to help a newcomer distinguish report types.

#### Availability and Access

Explain these independent questions:

1. Is the report implemented and exposed?
2. Is its feature enabled for the environment or MLS group?
3. Is this user entitled to access it?
4. Is data available for this property/geography?
5. Does access require purchase, credits, or an unlock operation?
6. What is displayed or returned when access/data is unavailable?

Trace rules to frontend guards/navigation, runtime configuration, access maps, feature flags, backend authorization/availability handlers, provider coverage, and purchase/credit/usage/unlock responses.

Do not equate a visible menu item with authorization, a route with data availability, a frontend guard with server enforcement, or purchase with universally available property data.

Create an availability matrix:

| Report | Feature flag/MLS condition | User entitlement | Property/data condition | Purchase/credit requirement | Unavailable UI behavior | Backend enforcement | Evidence |
| --- | --- | --- | --- | --- | --- | --- | --- |

Use `Not applicable` and `Unknown` accurately. Configuration values controlled externally must not be presented as verified deployed availability.

Include an availability decision flowchart. Use separate diagrams where report families have materially different rules instead of inventing a universal process.

#### Rendering and Actions

Explain report selection, request preparation, data loading, section rendering, enrichment, PDF preparation versus download, sharing, printing, and loading/no-data/restricted/expired/error states where present.

Link to authoritative backend module and feature-flow pages rather than duplicating their implementation walkthroughs.

### 8. Feature-to-Code Map and End-to-End Walkthroughs

Provide a feature map with:

`Feature | Frontend entry point | State/service | HTTP contract | Backend entry point | Owning modules | Storage/external dependency`

Distinguish the method sent by the frontend from the broader methods accepted by an unrestricted backend mapping. Resolve composed controller paths and do not assume an unrestricted `@RequestMapping` means GET only.

Write detailed walkthroughs for:

1. Login, initial user/configuration loading, session refresh, and logout.
2. Quick Search/My Search, dynamic fields, and result rendering.
3. Saving/reopening a search versus saving favorite properties.
4. Opening property details and related reports.
5. Preparing and downloading a PDF.
6. CSV exports and mailing-label generation.
7. Map interaction and property lookup.
8. Cart, checkout, report access, credits, and usage accounting.
9. Shared report links and recipient access.

Add other substantial discovered flows when needed.

For each walkthrough include user action, preconditions, concrete symbols and paths, sanitized input/output shapes, actual processing chain, transformations, state/storage changes, external calls, visible outcome, error/empty/limit/permission branches, debugging locations, and evidence.

Use Mermaid sequence diagrams for distinct major flows. Include `alt`, `opt`, `loop`, or parallel branches only where source supports them.

Investigate these important distinctions:

- APN normalization, geography, templates, field operators, result limits, suggestions, and search retries.
- External property data through template-driven XML into report response models where implemented.
- PDF preparation/cache/link creation versus later download, local HTML preparation, and external rendering.
- Export-specific data retrieval versus merely serializing the currently displayed grid.
- Remote active cart versus local saved-for-later records.
- Commerce purchases versus report-credit/usage accounting.
- Preference update partial success versus atomic transaction assumptions.
- Saved searches as templates versus saved favorite property identifiers.

### 9. Persistence, Caching, and External Integrations

Explain locally persisted application state versus externally owned property data.

Cover entities, repositories, migration history, schema changes, local report/commerce/sharing/feature data, and persistence ownership across modules.

Explain Redis responsibilities separately: HTTP sessions, application caches, request correlation, keys/tokens where relevant, and distributed scheduling locks. Describe expiration, invalidation, session-bound data, and compatibility concerns supported by source.

Document external IFC libraries and unavailable implementations, mapping clients, PDF rendering, commerce, property/analytics sources, and other discovered integrations.

Establish whether GraphQL/DGS is an outbound client facility or an inbound API before describing it.

Provide:

- Focused Mermaid ER diagrams based on current entities and cumulative migrations.
- A data-ownership diagram.
- An integration table with purpose, caller, protocol, configuration-key names, and observed failure behavior.

Do not draw logical references as enforced foreign keys or invent an external provider's schema.

### 10. Authentication, Authorization, and Configuration

Explain:

- Incoming login variants, including SAML, Ping, and direct-link paths where present.
- Standard login versus SSO and shared/direct-link access.
- Frontend route guards versus server authentication and authorization.
- Entitlements, report availability, feature flags, and EULA behavior where implemented.
- Session persistence, refresh, timeout, restoration, and logout.
- Outbound OAuth client credentials separately from incoming user login.
- Profile-specific security behavior and relevant cookie/CSRF handling as actually configured.
- Local resources, external configuration, platform bindings, bootstrap, and configuration decryption conceptually.
- Runtime-driven UI configuration and database-driven feature flags where present.

Do not confuse configuration-decryption material with SAML signing/decryption credentials.

Include authentication/session sequence diagrams and a configuration-source diagram. Mark unresolved effective configuration or deployed behavior rather than asserting it.

### 11. Local Setup, Build, Deployment, and Engineering Workflow

Provide evidence-backed instructions for required tools, authorized artifact access, PostgreSQL, Redis, external dependencies, startup order, frontend proxying, and ports.

Show commands with explicit working directories and Windows-compatible examples. Derive commands and executable artifact names from current scripts/build declarations, not stale documentation.

Explain:

- Which features require external services even when local databases are running.
- npm/Gradle responsibilities and packaging of both Angular applications.
- Build-time versus runtime configuration and relevant packaging exclusions.
- Jenkins flow, publishing, deployment handoff, profiles, and environment differences.
- Jenkins build infrastructure separately from application hosting.
- The actual manifest format; do not label Cloud Foundry/Kf-style manifests as native Kubernetes workloads.
- External pipeline behavior, production topology, and rollback procedures only when evidence exists.

Include a Mermaid build/artifact/deployment flowchart.

Document existing test/lint tooling, source locations, scope, and caveats. Distinguish configured, disabled, legacy, and actually executed tooling. Do not infer test execution from a successful build task definition or infer PostgreSQL behavior solely from H2 tests.

Provide:

- A symptom -> likely layer -> source/logs to inspect troubleshooting table.
- Evidence-backed onboarding pitfalls.
- How to locate code for changing a search field, report, API endpoint, preference, feature flag, or database table.
- Cross-layer impact and existing relevant tests for those changes.
- Separate current behavior from any suggested improvements.

### 12. KT Delivery and Newcomer Learning

Finish the documentation set with:

- A practical 90-minute KT agenda.
- A first-week learning plan.
- An ordered code-reading path with reasons.
- Small, non-destructive learning exercises.
- Questions that assess understanding.
- A searchable glossary and feature-to-file index.
- Known documentation discrepancies.
- Unknowns, impact, and questions for the relevant team owners.

## Document Ownership, Linking, and Page Template

Maintain one authoritative explanation for each topic:

- Overview pages own the big picture.
- Frontend pages own browser-side behavior and architecture.
- Backend module pages own module responsibilities and internals.
- Feature-flow pages own complete cross-layer execution walkthroughs.
- Cross-cutting pages own shared data/security/configuration/integration concerns.
- Development pages own setup and engineering workflows.
- Onboarding pages own learning and KT delivery.
- Completion status owns work tracking and resume instructions.

Link to the authoritative explanation instead of copying it. Split by coherent responsibility rather than arbitrary length. Main context and indexes should remain concise; deeper pages may be longer where needed.

For detailed pages, use the following sections where appropriate:

1. Navigation links.
2. Purpose and business relevance.
3. Concepts a newcomer needs first.
4. Relevant directories, classes, and entry points.
5. Architecture or execution flow.
6. Mermaid diagram and explanation.
7. Inputs, outputs, dependencies, and state changes.
8. Constraints, branches, and failure behavior.
9. Debugging/change locations.
10. Source references.
11. Related documentation.
12. Unknowns requiring confirmation.

Do not force irrelevant sections into every page.

Navigation rules:

- Use relative Markdown links, not machine-specific absolute paths.
- Use forward slashes inside Markdown link destinations for portability; filesystem commands and displayed Windows paths may use backslashes.
- Every major section must have an index or overview page.
- Every detailed page must link to its parent section and main project context.
- Cross-link related frontend, backend, report catalogue, and feature-flow pages.
- Make every generated document reachable from the main context through section navigation.
- Use descriptive link labels and valid heading anchors.
- Avoid orphan documents and broken links.

Examples:

```markdown
<!-- From project-context.md -->
[Frontend](frontend/index.md)
[Backend](backend/index.md)
[Completion Status](completion-status.md)

<!-- From frontend/dashboard/widgets-and-data-flow.md -->
[Project Context](../../project-context.md)
[Dashboard](index.md)

<!-- From backend/modules/uaf-propertysearch.md -->
[Project Context](../../project-context.md)
[Backend](../index.md)
[Property Search Flow](../../feature-flows/property-search.md)
```

## Mermaid and Presentation Requirements

- Use fenced `mermaid` blocks with broadly supported `flowchart`, `sequenceDiagram`, and `erDiagram` syntax. Use a state diagram only when it clarifies a source-supported lifecycle.
- Produce actual diagrams, not placeholders or ASCII-only substitutes.
- Place each diagram beside the explanation it supports.
- Prefer several focused diagrams over one oversized diagram.
- Use boundaries/subgraphs for Browser, Application, Library Modules, Storage, and External Services where useful.
- Label arrows with operations or data and distinguish runtime calls from build dependencies.
- Include a legend for inferred or unknown edges. Do not draw unsupported edges as facts.
- Keep long source paths and citations below diagrams, not inside node labels.
- Explain how to read each diagram and provide its evidence.
- Ensure diagram arrows and prose describe the same implementation.
- Do not repeat large diagrams across pages; link to their owning document.
- Use clear H1/H2/H3 headings, progressive explanations, concise tables, and sanitized examples.
- Avoid generic tutorials, unexplained class dumps, needless repetition, and promises unsupported by source.

Required diagram distribution:

| Owning area | Required diagram topics |
| --- | --- |
| Project context and overview | System overview, user journey, runtime/external boundaries |
| Frontend architecture | Routes/loading, state/request flow, direct-service exceptions |
| Dashboard | Widget composition and representative loading sequence |
| Search | Dynamic form generation, results interactions, search sequence |
| Property Intelligence | Feature navigation and analytics data flow |
| Backend | Module dependencies and processing responsibilities |
| Reports | Availability decisions and rendering/action relationships |
| Feature flows | Major end-to-end request sequences |
| Data and integrations | Focused ER diagrams and data ownership |
| Security/configuration | Login/session sequences and configuration sources |
| Build/deployment | Build, packaging, artifact, and deployment handoff |

## Execution Sequence and Completion Criteria

1. Load or initialize the progress checkpoint.
2. Establish repository structure, current revision, and source-backed system boundaries.
3. Inventory frontend features, backend modules, reports, routes, integrations, and planned documents.
4. Research independent areas efficiently and checkpoint findings.
5. Write the modular documentation with diagrams, evidence, and cross-links.
6. Trace critical user journeys across the frontend/backend boundary.
7. Reconcile conflicting documentation against source and mark unknowns.
8. Review navigation, relative links, heading anchors, Mermaid syntax, evidence, and coverage using available non-mutating means. Do not install tools solely for this step.
9. Update completion status and the exact resume checkpoint.

The documentation must enable a newcomer to:

- Explain the application's business purpose and overall architecture.
- Navigate Dashboard, Search, Property Intelligence, Reports, and other major frontend areas.
- Explain every backend module's responsibility and participation.
- Distinguish report types and the separate rules governing availability and access.
- Trace user actions across frontend, backend, storage, and external boundaries.
- Understand setup prerequisites and build/deployment boundaries.
- Find the right code for a small feature change.
- Recognize which details remain unconfirmed and where to obtain them.
- Resume unfinished documentation work from the persistent checkpoint.

A required task is complete only when its content, evidence, appropriate diagrams, and navigation are present and consistent. Do not mark a task complete simply because a document exists. Do not silently omit required areas or leave empty placeholders.

If time, context, access, or evidence prevents completion, preserve a useful partial result, mark unfinished/blocked tasks accurately, update **Resume Here**, and state the limitation. Do not claim the whole set is complete.

## Final Handoff

At the end of every run:

- Update `kt-docs\completion-status.md`.
- Report the main entry point: `kt-docs\project-context.md`.
- Report the progress checkpoint location.
- Briefly summarize the sections completed.
- Identify significant gaps, incomplete tasks, and blockers.
- If unfinished, identify the exact task and action to resume.

Do not paste the entire documentation set into the final response.
