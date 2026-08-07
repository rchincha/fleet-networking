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
- Document the relationship between this product/architecture artifact and
  the implementation work in PR #373 without claiming one supersedes the
  other.

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
- "also add a user stories section also"
- "add the above information to the RFC anticipating fleet networking
  questions around multi-region deployments."
- "also add a early competitive section about GKE (gcp) and EKE (aws)"
- "PR #373 should also have information about the comptetive landscape"
- "remove this line \"Supersedes The AFD architecture draft in PR #373,
  especially docs/first-party/001-afd-global-load-balancing.md\""
- "update the doc accordingly and title."
- "are there other \"PRD and Architecture RFC\" in Microsoft as a common or
  uncommon practice?"
- "The CRDs, k8s object examples, and explanation for the following has been
  captured in that detail in this doc? Else include that in the Appendix and
  add links from the main doc body: the Kubernetes API model and its
  relationship to Gateway API and Multi-Cluster Services (MCS) API"

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
- [x] **Task 4.2: Clarify the relationship to the prior proposals.**
  - Treat PR #373 and its proposals as implementation evidence while this
    document captures the broader product and architecture discussion.
  - Do not claim that either artifact supersedes the other.
  - Success criteria: reviewers understand the role of each artifact.
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
- [x] **Task 4.6: Add cross-persona user stories.**
  - Cover external and first-party application owners, platform operators,
    security teams, and support engineers.
  - Include public AFD, private AFD + PLS, ATM, multi-region failover,
    migration, brownfield readiness, and operational status.
  - Success criteria: each major product scenario is expressed as a user
    outcome with reviewable acceptance expectations.
- [x] **Task 4.7: Expand the multi-region Fleet deployment model.**
  - Explain the separation between Fleet placement and global-ingress traffic
    policy for member clusters distributed across Azure regions.
  - Cover endpoint eligibility, AFD and ATM behavior, active-active,
    active-passive, regional evacuation, and safe cluster addition/removal.
  - Success criteria: fleet-networking reviewers can trace the complete
    lifecycle from application placement through global traffic readiness.
- [x] **Task 4.8: Add an early competitive landscape.**
  - Compare the relevant multi-cluster ingress approaches in GKE and Amazon
    EKS using current first-party vendor documentation.
  - Identify product lessons without claiming exact feature parity.
  - Success criteria: reviewers understand how the proposed Fleet experience
    relates to established GCP and AWS approaches.
- [x] **Task 4.9: Add competitive context to PR #373.**
  - Add a concise version of the competitive landscape to the first-party
    architecture proposal on the PR #373 branch.
  - Clearly state that the cross-team RFC remains the governing architecture
    and that PR #373 is implementation evidence.
  - Success criteria: reviewers of either PR see the relevant GKE and Amazon
    EKS context without interpreting the implementation PR as final design.
- [x] **Task 4.10: Remove the RFC supersession claim.**
  - Remove the metadata statement that the RFC supersedes the architecture
    draft in PR #373.
  - Success criteria: the RFC does not claim replacement of PR #373.
- [x] **Task 4.11: Reframe the artifact as a PRD and architecture RFC.**
  - Retitle the document and metadata.
  - Convert the opening summary into an executive summary and add an explicit
    problem statement before the decision request.
  - Success criteria: executive stakeholders can understand the problem,
    proposal, scope, initial milestone, and required decisions from the
    opening sections.
- [x] **Task 4.12: Add a detailed Kubernetes API appendix.**
  - Document the candidate Gateway API, MCS, and Azure/Fleet policy object
    model, including object placement and ownership.
  - Add complete illustrative manifests, reconciliation mapping, status
    expectations, and unresolved API decisions.
  - Link the appendix from the decision request and API direction sections.
  - Success criteria: reviewers can understand the full candidate Kubernetes
    API without interpreting the examples as an approved schema.

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

- Create a new cross-team PRD and architecture RFC rather than rewriting the
  first-party proposal in place.
- Keep the PRD/architecture RFC and PR #373 complementary; neither artifact
  supersedes the other.
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

- Added a standalone cross-team PRD and architecture RFC at
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
- Added twelve architecture-level user stories with acceptance outcomes for
  application owners, platform operators, security owners, brownfield cluster
  operators, and support engineers.
- Added an early competitive landscape comparing the proposed AKS Fleet
  experience with GKE fleets and multi-cluster Gateway/Ingress, and with the
  assembled Amazon EKS, ALB, Route 53, Global Accelerator, and VPC Lattice
  model.
- Expanded the multi-region section to separate Fleet placement from global
  traffic policy, define endpoint eligibility, compare AFD and ATM behavior,
  and specify traffic-safe member-cluster addition and removal ordering.
- Removed the RFC metadata line that claimed it superseded the architecture
  draft in PR #373.
- Updated PR #373's first-party proposal with the evidence-based GKE and
  Amazon EKS competitive landscape in commit `b69a33d`.
- Retitled the document to
  `PRD and Architecture RFC: Global Ingress for AKS Fleet`.
- Reworked the opening as an executive summary and added a dedicated problem
  statement covering the current state, customer/platform pain points, and
  consequences of leaving the problem unresolved.
- Added Appendix A with the detailed candidate Kubernetes API model,
  including Gateway API and MCS responsibilities, object placement, complete
  private/public manifests, candidate policy CRD schemas, shared-gateway
  variation, reconciliation mapping, status, namespace rules, ATM
  coexistence, and unresolved decisions.
- Linked the appendix from the executive decision request and API direction
  sections.
- Refined `FleetBackendPolicy` to target one Gateway and ServiceImport pair so
  connectivity and SKU validation remain deterministic when a ServiceImport is
  reused.
- Added an `AzureFrontDoorCertificate` candidate CRD because AFD cannot bind a
  Kubernetes TLS Secret directly and customer certificates require Azure Key
  Vault.

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
- Official GKE Multi Cluster Ingress, multi-cluster Gateway, Gateway API, and
  Cloud Armor documentation.
- Official Amazon EKS Load Balancer Controller, Global Accelerator, Route 53,
  VPC Lattice, and AWS WAF documentation.
