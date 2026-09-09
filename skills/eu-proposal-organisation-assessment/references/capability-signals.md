# Capability signals

Maps wording in a proposal duty to the capability it implies. Load this file when deriving implied capabilities; a stated capability needs no signal.

## Signal table

| Signal in the duty text | Implied capability |
|---|---|
| Low-level, real-time, firmware, embedded, on-device, resource-constrained | Systems programming in a low-level language such as C or C++, and embedded toolchains |
| REST, API, endpoint, web service, portal, dashboard, user interface | API design, web service and front-end development |
| Ingestion, ETL, streaming, time series, data lake, historian | Data engineering, pipeline and database design |
| Training, prediction, forecasting, anomaly detection, classification | Machine learning, feature engineering and model validation |
| Deployment, containers, scalability, cloud hosting, edge-cloud | Cloud and container operations |
| Digital twin, simulation, co-simulation, solver | Numerical modelling and simulation engineering |
| Ontology, semantic model, knowledge graph, RDF, SPARQL, vocabulary | Semantic data modelling |
| A named standard, for example IEC CIM, IEC 61850, OGC, INSPIRE or IFC | Working knowledge of that standard and its data model |
| Conformance, interoperability testing, profile, mapping between formats | Standards conformance testing and data mapping |
| Authentication, access control, privacy by design, threat assessment | Security engineering and data protection practice |
| Life-cycle assessment, carbon footprint, environmental footprint | Life-cycle assessment practice, inventory data and impact methods |
| Sampling, sensors in the field, surveys, inventories, monitoring plan | Field monitoring design and instrument handling |
| Habitat, species, water body, soil, air quality, emissions accounting | The matching environmental science discipline |
| Directive, permit, compliance, reporting obligation, procurement rule | Regulatory and policy analysis for the named instrument |
| Co-design, workshops with a named group, training, capacity building, replication | Stakeholder engagement and facilitation methods |
| Business model, market analysis, exploitation route, cost-benefit | Techno-economic analysis |

## Using the table

- Match the signal inside the target organisation's own duty. Project-level narrative is not a signal.
- The table licenses a capability class. Name a product only when the proposal names it.
- A generic verb such as “contribute to” or “support” is not a signal. Find the concrete duty first.
- Absent signals mean absent capabilities. Do not treat the table as a checklist to fill.
- Add a row only when a proposal shows a signal this table misses.

## Anti-patterns

| Anti-pattern | Example to reject |
|---|---|
| Sector or identity inference | “It is a technology centre, so it needs software architects.” |
| Partner leakage | Listing Fortran because Partner B builds the calculation engine in Fortran. |
| Capacity-section mining | Listing semantic web expertise from the partner description when no duty requires it. |
| Product over-specification | Turning “low-level module” into “Rust” or “C++17”. |
| Effort inference | Reading 24 person-months as evidence of senior expertise. |
| Routine-duty inference | Reading dissemination work-package membership as a need for communications design. |
| Consortium stack attribution | “The platform runs on Kubernetes, so the organisation needs Kubernetes.” |
| Task restatement | Writing “Task 3.2 implementation” instead of naming transferable expertise. |
| Ladder duplication | Listing software engineering, backend development and Python as three rows for one duty. |
| Generic padding | Adding project management, report writing or English with no named duty. |

Name expertise a reader could recruit for. One discipline-level row may sit alongside a specific row when they describe different levels, as with software engineering and C or C++. Three levels for one duty is padding.
