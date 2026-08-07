# AFD Global Ingress RFC

## Requirements

- Create a cross-team design document involving the Fleet Manager,
  fleet-networking, and AKS teams.
- Reframe Azure Front Door (AFD) + WAF as a complete L7 alternative to the
  existing Azure Traffic Manager (ATM) path, not only as a first-party
  compliance feature.
- Cover external customers and first-party customers, including first-party
  workloads that require Private Link Service (PLS).
- Cover the full architecture: multi-region, multi-cluster ingress, traffic
  management, health and failure modes, networking implications, security,
  brownfield and greenfield adoption, operations, ownership, and rollout.
- Keep the first implementation milestone focused on first-party PLS while
  ensuring the architecture does not preclude the broader product.
- Create the RFC at `docs/design/afd-global-ingress-rfc.md`.
- Make the RFC supersede
  `docs/first-party/001-afd-global-load-balancing.md`.

## Additional comments from user

- "fleet-networking team is pushing back to start with a design doc first
  involving fleet manager team, fleet networking team and aks."
- "the scope of afd+waf should be to be a full alternative to atm so both
  external customers and first-party customers who require private link
  (which was the initial focus)."
- "Also consider multi-region, multi-cluster ingress, failure modes,
  networking implications, brownfield vs greenfield, etc."
- "Consider the full picture but initial focus can be first-party PLS."
- User selected: create a new stakeholder RFC that supersedes Proposal 001.
- User selected: place it at `docs/design/afd-global-ingress-rfc.md`.
- "use mermaid diagrams for graphics"
- "this doc will be exported as a word doc eventually to share"
- "Can you update the RFC with the above table? Origin requirement |
  Global ingress | Tier | WAF"

## Plan

### Phase 1: Establish Current State and Constraints

- [x] **Task 1.1: Document the existing ATM product contract.**
  - Review ATM APIs, controllers, customer workflow, routing semantics,
    health model, DNS behavior, and limitations.
  - Success criteria: the RFC compares AFD against the actual Fleet ATM
    surface rather than a generic Azure Traffic Manager description.
- [x] **Task 1.2: Extract reusable findings from the current AFD proposals.**
  - Reuse validated material on WAF, Private Link, identities, AKS Service
    annotations, and controller boundaries.
  - Separate implementation assumptions from durable requirements.
  - Success criteria: useful prior work is retained without treating the POC
    implementation as an approved architecture.
- [x] **Task 1.3: Verify Azure platform capabilities and constraints.**
  - Use current Microsoft documentation for AFD profiles, endpoints, routes,
    origin groups, health probes, WAF, Private Link origins, custom domains,
    certificates, quotas, and regional/network behavior.
  - Success criteria: platform claims in the RFC have authoritative references.

### Phase 2: Define the Product and Architecture

- [x] **Task 2.1: Define goals, non-goals, personas, and scenarios.**
  - Include external customer internet ingress, first-party public ingress with
    private origins, and coexistence with L4/non-HTTP workloads.
  - Success criteria: readers can distinguish the full target product from the
    first implementation milestone.
- [x] **Task 2.2: Define the end-to-end architecture and ownership boundaries.**
  - Describe Fleet Manager, fleet-networking, AKS/cloud-provider, AFD/WAF,
    DNS/certificate, and customer responsibilities.
  - Include hub and member control planes, Azure resource ownership, identity,
    tenancy, and lifecycle.
  - Success criteria: each resource and reconciliation boundary has one
    accountable owner.
- [x] **Task 2.3: Define the multi-cluster and multi-region traffic model.**
  - Cover origin grouping, priority/weight, active-active and active-passive
    patterns, regional evacuation, health probes, connection draining, and
    DNS/custom-domain behavior.
  - Success criteria: the RFC explains steady state and failover behavior for
    at least two regions and multiple clusters per region.
- [x] **Task 2.4: Define the API direction without prematurely locking schemas.**
  - Evaluate Fleet-specific CRDs versus Gateway API/MCS API integration and
    identify compatibility requirements with ServiceExport/ServiceImport.
  - Record proposed abstractions, alternatives, and open decisions for
    cross-team review.
  - Success criteria: the RFC enables API discussion while avoiding an
    implementation-first commitment.

### Phase 3: Address Safety, Operations, and Adoption

- [x] **Task 3.1: Build a failure-mode and recovery matrix.**
  - Cover AFD, WAF, Private Link, PLS, ILB, cluster, region, controller,
    identity, DNS, certificate, configuration, quota, and Azure API failures.
  - Include detection, customer impact, automated response, and operator action.
  - Success criteria: expected behavior is explicit for control-plane and
    data-plane failures.
- [x] **Task 3.2: Document networking and security implications.**
  - Cover VNet/subnet prerequisites, PLS NAT IP capacity, private endpoint
    approval, NSGs/UDRs/firewalls, source IP and headers, TLS boundaries,
    WAF policy ownership, egress, DNS, and public-origin versus private-origin
    modes.
  - Success criteria: AKS and networking reviewers can identify prerequisite
    platform work and security boundaries.
- [x] **Task 3.3: Define brownfield and greenfield journeys.**
  - Include new deployments, migration from ATM, migration from independently
    managed AFD, rollback, coexistence, and avoidance of double advertisement.
  - Success criteria: customers have safe adoption and rollback paths with no
    forced flag day.
- [x] **Task 3.4: Define observability, SLOs, scale, quotas, and cost concerns.**
  - Include controller metrics/events, Azure resource health, probe visibility,
    auditability, reconciliation latency, capacity, API throttling, and cost
    attribution.
  - Success criteria: operational readiness requirements are reviewable before
    implementation.

### Phase 4: Produce the Cross-Team RFC

- [x] **Task 4.1: Write `docs/design/afd-global-ingress-rfc.md`.**
  - Use a decision-oriented structure with status, stakeholders, summary,
    requirements, architecture, scenarios, alternatives, risks, rollout, and
    open questions.
  - Clearly label the first-party PLS milestone as Phase 1 of a broader design.
  - Success criteria: the document is useful as the primary artifact for Fleet
    Manager, fleet-networking, and AKS design review.
- [x] **Task 4.2: Mark the relationship to the prior proposals.**
  - State that the new RFC supersedes Proposal 001 for architecture direction.
  - Treat Proposal 002 and the POC as implementation evidence, not approved
    product design.
  - Success criteria: reviewers know which document governs future decisions.
- [x] **Task 4.3: Review the Markdown and update this breadcrumb.**
  - Check local links, headings, tables, terminology, and completeness against
    every plan task.
  - Success criteria: the RFC is internally consistent and the breadcrumb
    records sources, decisions, and changes.
- [x] **Task 4.4: Add export-friendly Mermaid diagrams.**
  - Use Mermaid for architecture, traffic flow, readiness, failure, and
    migration graphics.
  - Keep labels and layouts simple enough to render legibly to SVG or PNG
    before conversion to Microsoft Word.
  - Success criteria: all conceptual graphics use Mermaid and the RFC records
    the Word-export rendering requirement.
- [x] **Task 4.5: Add the origin-connectivity decision matrix.**
  - Compare private HTTP(S), public HTTP(S), public non-HTTP, and private
    non-HTTP origin requirements across AFD, ATM, tier, and WAF support.
  - Clarify that origin connectivity is independent of AKS private-cluster
    control-plane configuration.
  - Success criteria: reviewers can select the appropriate global ingress
    option without conflating cluster privacy with origin reachability.

### Checklist

- [x] Phase 1 / Task 1.1: Document the current ATM contract.
- [x] Phase 1 / Task 1.2: Extract durable findings from prior AFD work.
- [x] Phase 1 / Task 1.3: Verify Azure platform constraints.
- [x] Phase 2 / Task 2.1: Define personas and product scenarios.
- [x] Phase 2 / Task 2.2: Define architecture and ownership.
- [x] Phase 2 / Task 2.3: Define multi-region and multi-cluster traffic.
- [x] Phase 2 / Task 2.4: Define API direction and alternatives.
- [x] Phase 3 / Task 3.1: Build the failure-mode matrix.
- [x] Phase 3 / Task 3.2: Cover networking and security.
- [x] Phase 3 / Task 3.3: Cover brownfield and greenfield adoption.
- [x] Phase 3 / Task 3.4: Cover operations, scale, quotas, and cost.
- [x] Phase 4 / Task 4.1: Write the RFC.
- [x] Phase 4 / Task 4.2: Supersede the prior architecture proposal.
- [x] Phase 4 / Task 4.3: Review and finalize documentation.
- [x] Phase 4 / Task 4.4: Add export-friendly Mermaid diagrams.

### Success Criteria

- One standalone RFC presents AFD + WAF as the strategic Fleet L7 global
  ingress option for external and first-party customers.
- The design clearly distinguishes target architecture from the first-party
  PLS initial milestone.
- Fleet Manager, fleet-networking, and AKS ownership boundaries and open
  decisions are explicit.
- Multi-region, multi-cluster, failure, networking, security, operations,
  brownfield, and greenfield concerns are addressed.
- The RFC does not claim that AFD replaces ATM for non-HTTP/HTTPS L4 workloads.
- All externally verifiable platform claims cite current authoritative sources.

## Decisions

- Create a new cross-team RFC rather than rewriting the first-party proposal in
  place.
- The RFC will supersede Proposal 001 for architecture direction.
- Place the RFC under `docs/design/` because its audience and scope extend
  beyond first-party workloads.
- Treat first-party PLS as the initial delivery milestone, not the product
  boundary.
- Keep API shapes discussion-level until the three teams agree on ownership and
  integration with Fleet and AKS surfaces.
- Use Mermaid as the source format for conceptual graphics.
- Render Mermaid diagrams to SVG or high-resolution PNG before exporting the
  RFC to Microsoft Word; do not rely on Word to interpret Mermaid source.

## Implementation Details

- Added a standalone cross-team RFC at
  `docs/design/afd-global-ingress-rfc.md`.
- Compared the current Fleet ATM contract with AFD's L7 proxy model and
  retained ATM for non-HTTP and direct-DNS scenarios.
- Recommended a provider-neutral hybrid origin attachment, with direct
  private Service + PLS as the initial provider and shared cluster gateways
  as a future provider.
- Defined layered readiness for Azure resources, domains, WAF, origins,
  Private Link approval, and health.
- Added explicit Phase 0 decisions for Azure resource subscription/tenant
  placement, security acceptance of the public AFD edge, and private-origin
  TLS issuance.
- Added eight Mermaid diagrams for data-path comparison, Azure resource
  hierarchy, control plane, public/private data plane, origin models,
  readiness, failure selection, and ATM migration.
- Documented that Mermaid must be rendered to SVG or high-resolution PNG
  before Microsoft Word export.

## Changes Made

- Added this breadcrumb before drafting the requested RFC.
- Added `docs/design/afd-global-ingress-rfc.md` as the design-review source of
  truth.
- Added product scope, architecture options, traffic semantics, failure modes,
  networking/security requirements, adoption paths, operations, ownership,
  phased delivery, risks, and open questions.
- Replaced text-only conceptual graphics with Mermaid diagrams.
- Added Word-export guidance and validated all Mermaid diagrams by rendering
  them with Mermaid CLI.
- Validated all RFC links with the repository Markdown link-check
  configuration.
- Added an origin-connectivity decision matrix that distinguishes private
  HTTP(S), public HTTP(S), public non-HTTP, and private non-HTTP scenarios.
- Clarified that AKS private-cluster control-plane configuration does not
  determine whether the application origin is public or private.

## Before/After Comparison

| Aspect | Existing Proposal 001 | New RFC target |
|---|---|---|
| Primary audience | First-party Fleet adopters | External customers, first-party customers, Fleet Manager, fleet-networking, and AKS |
| Product framing | Parallel SFI-focused AFD path | Full Fleet L7 global ingress alternative to ATM |
| Initial milestone | AFD Premium + WAF + PLS | Same, explicitly staged within a broader product |
| Architecture scope | Controller and CRD oriented | Product, platform, traffic, networking, failure, operations, and adoption oriented |
| API posture | Concrete implementation-first CRDs | Alternatives and decision points pending cross-team agreement |
| Graphics | Text-only flows | Eight Mermaid diagrams designed for static Word export |
| Publication | Repository Markdown | Markdown source with rendered SVG/PNG graphics for the shared Word document |

## References

- Existing architecture proposal:
  `docs/first-party/001-afd-global-load-balancing.md` (Draft)
- Existing implementation plan:
  `docs/first-party/002-afd-implementation-plan.md` (Draft)
- Existing readiness checklist:
  `docs/first-party/003-pre-implementation-checklist.md` (Open)
- Existing POC runbook:
  `docs/first-party/004-poc-runbook.md`
- Related breadcrumb:
  `.github/.copilot/breadcrumbs/2026-07-20-1108-afd-export-mode-mcs-parity.md`
- No files were present under `.github/.copilot/domain_knowledge/` or
  `.github/.copilot/specifications/` on this branch.
- Current Microsoft documentation for AFD routing, origins, traffic
  selection, health probes, Private Link, WAF, domains, and service limits.
- Current Microsoft documentation for AKS internal load balancers and PLS.
- Upstream MCS API, KEP-1645, and Gateway API documentation.
