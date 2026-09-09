# Analysis method

Use this method for Horizon Europe and LIFE proposals. Proposal structures differ, so follow the evidence rather than fixed section numbers.

## 1. Establish the organisation identity

Find the participant list and partner description first.

Record:

- legal name;
- acronym;
- consortium number, if present;
- spelling variants;
- escaped or OCR-damaged forms;
- previous names only when the proposal clearly treats them as the same entity.

Search for all confirmed forms. Do not merge similar names without evidence.

## 2. Read the complete evidence set

Inspect every relevant part of the proposal:

- project concept, methodology, objectives and expected results;
- participant roles and capacities;
- work-package and task descriptions;
- deliverables and milestones;
- dependencies and handovers between work packages;
- person-month or effort tables;
- key exploitable results, ownership and exploitation plans;
- long-term operation, maintenance or custodianship;
- risks and mitigation duties;
- management, communication and reporting tasks.

Tables may contain information that is absent from the narrative. Narrative text may narrow a broad lead label. Cross-check both.

If the document is scanned or poorly converted, use the available extraction or OCR capability. Check important task rows visually when possible. Record any remaining uncertainty.

## 3. Build an evidence map

Create one internal record for each mention that may affect the assessment.

| Field | Record |
|---|---|
| Project unit | WP, task, deliverable, milestone, result or other unit |
| Formal lead | Organisation named as lead |
| Target duty | Exact action assigned to the target organisation |
| Partner duties | Actions assigned to other organisations |
| Output | Deliverable, software, method, dataset, report or decision |
| Timing | Project-month range or stated date |
| Effort | Target person-months and relevant WP total, when available |
| Methodology anchor | Element, stage, objective, component, result or KER |
| Required capability | Technology, standard, discipline or knowledge the target's duty requires |
| Capability basis | Stated or implied, with the licensing wording when implied |
| Source | Task ID, table, section or reliable page |
| Role level | Core, supporting boundary or routine mention |
| Confidence | High, medium or limited, with reason if not high |

Use exact proposal wording only in the evidence map. Paraphrase it in the final report.

Build a separate led-output register for every deliverable and milestone whose formal lead is the target organisation, even when the related role is not otherwise core. For each item, record its type, ID, title, timing or due date, practical output, parent WP or action, and source.

## 4. Classify the role

### Core

Include a role in the main assessment when at least one of these applies:

- The target organisation leads the WP, task, deliverable or milestone.
- The proposal assigns it a specific and substantial build, definition, validation, coordination or integration duty.
- It owns or maintains a key result and the proposal states concrete work.
- It controls an important handover, acceptance decision, common specification or cross-WP dependency.
- Its effort is material and the narrative describes a matching responsibility.

A formal lead is always core. If its detailed build duty is narrow, say so clearly.

### Supporting boundary

Use this level when the organisation has a named but smaller duty. Examples include:

- contributing criteria to a framework led by another partner;
- providing feedback, data or domain review;
- supporting a pilot without operating it;
- joining exploitation or custodianship without a defined service of its own.

Summarise these roles after the core assessment. Include them in the journey only when they explain a handover or long-term outcome.

### Routine mention

Treat these as routine unless the proposal assigns a specific duty:

- “all partners” management, reporting or dissemination;
- appearance in a participant list with no described work;
- broad statements that all partners will review or contribute;
- generic attendance at workshops or meetings.

Do not list routine mentions task by task in the main report. State the exclusion rule once.

## 5. Use effort carefully

Person-months can confirm where work is concentrated. They cannot define responsibility on their own.

When full effort data exists:

1. Sum the target organisation's effort in the core WPs.
2. Compare it with the organisation's total project effort.
3. Use the result as supporting context, not as the inclusion test.

If effort tables are incomplete or distorted by conversion, do not calculate a percentage. Say that reliable effort data was unavailable.

## 6. Analyse responsibility boundaries

For each core task, answer four questions:

1. Who formally leads?
2. What concrete work belongs to the target organisation?
3. What concrete work belongs to other named partners?
4. What would overstate the target organisation's role?

Use direct boundary language:

- “The target organisation defines the interface; Partner B builds the platform.”
- “The target organisation leads the task and builds the dashboard; Partner C builds the calculation engine.”
- “The target organisation checks conformance; Partner D leads the full testing programme.”

Avoid vague phrases such as “supports implementation” when the proposal provides a more precise division.

### Conflicting or layered statements

Proposals often contain several kinds of ownership:

- formal WP leadership;
- formal task leadership;
- deliverable ownership;
- detailed implementation duties;
- result ownership or exploitation rights;
- post-project maintenance.

Do not collapse these into one claim. State each layer separately.

If the task heading says the target leads, but the body assigns most development to other partners, report both facts. Describe the target as formally accountable for the task, then name its concrete build duty.

## 7. Derive the capability profile

Identify the technologies, standards and expertise the target organisation must bring to do its stated work.

Work from the duty, not from the organisation. A capability belongs in the profile only when a duty assigned to the target requires it.

Derive capabilities from every core role. Add one from a supporting-boundary role only when its duty needs something distinctive, such as a named domain review. Ignore routine mentions.

### Evidence basis

Mark every capability with one of two bases:

- Stated: the proposal names the technology, standard, discipline or knowledge in the target's own duty, output, work package or action.
- Implied: the proposal describes work that cannot be delivered without the capability, but does not name it.

Admit an implied capability only when the stated duty could not be delivered without it. “Necessarily requires” meets the bar. “Typically involves” does not.

Record the licensing wording in the evidence map. Name the capability class, not a product: write `a low-level language such as C or C++`, not `C++17`.

Basis and confidence are separate. A named standard in a drafting placeholder is stated but of limited confidence.

### Excluded sources

Never derive a capability from:

- the organisation's sector, size, legal type or name;
- its past projects, references or track record;
- another partner's duty;
- effort figures;
- routine all-partner duties;
- what the field usually requires.

The participant capacity section may corroborate a capability that a duty already requires. It cannot create one, and it cannot turn an implied entry into a stated one.

### Boundary rule

Exclude any capability required only by another partner's duty. If Partner B builds the calculation engine in Fortran, the target does not need Fortran.

Report such capabilities separately, and only where a reader could reasonably misattribute them.

### Categories

Group capabilities under these categories. Omit a category with no entries.

| Category | Covers |
|---|---|
| Software technologies | Languages, runtimes, frameworks, platforms and tooling |
| Standards, data models and protocols | Named standards, schemas, exchange formats and interfaces |
| Engineering and scientific disciplines | Bodies of expertise the organisation must staff |
| Methods and techniques | Named procedures the organisation must execute |
| Domain and sector knowledge | The system, sector or environment the work acts on |
| Regulatory and policy knowledge | Directives, permits, reporting duties and procurement rules |

Use disciplines for expertise and methods for procedures. Record each capability once, in its closest category.

Name transferable expertise, not the work breakdown. `Task 3.2 implementation` is not a capability.

Read [capability-signals.md](capability-signals.md) before deriving implied capabilities.

## 8. Link work to the project concept

Use the proposal's own conceptual structure.

Preferred anchors are:

1. named methodology Elements or stages;
2. project objectives and expected results;
3. platform modules or technical components;
4. key exploitable results;
5. intervention-logic outputs or LIFE actions.

Do not invent “Elements” when the proposal has none. Use the closest named structure and tell the reader which structure was used.

For each core task, link only the concepts supported by the text. Distinguish a direct link from a broader WP-level association.

## 9. Construct the organisation's journey

Order the organisation's work by dependency, not only by WP number.

A useful journey usually shows:

1. inputs received from users, pilots or upstream partners;
2. rules, methods, data or components created by the organisation;
3. work received from scientific or technical partners;
4. integration into a larger system or workflow;
5. testing and validation;
6. pilot, market, policy or operational handover;
7. exploitation or maintenance, when it is a real responsibility.

At every step, say who owns the next stage. The journey should make handovers visible.

## 10. Build the Mermaid diagrams

### Journey flowchart

Use a top-down flowchart unless the journey is very short.

- Keep labels short.
- Put the target organisation's core work in one colour.
- Put shared integration points in a second colour.
- Put partner-led work in a third colour.
- Show inputs, outputs and handovers with arrows.
- Do not place full task descriptions inside nodes.
- Add a one-sentence legend below the diagram.

### Gantt calendar and entries

Choose the Gantt start date in this order:

1. the user's valid `project_start_date` in `YYYY-MM-DD` format;
2. a confirmed proposal start date;
3. the relative-calendar fallback, with M1 set to `2000-01-01`.

For a real start date, start of Mn = the selected start date plus `n - 1` calendar months. End of an inclusive task range ending in Mn = the selected start date plus `n` calendar months; this is Mermaid's exclusive end boundary. A milestone or deliverable due in Mn occurs on the final day of that project month: the selected start date plus `n` calendar months minus one day. Use the same rules for the relative fallback.

If the user supplies a start date that conflicts with the proposal, use the supplied date for the chart and state the conflict as an evidence limitation. If the supplied date is not a valid calendar date, ask for a valid date rather than guessing.

Examples:

| Project timing | Gantt start | Gantt end |
|---|---|---|
| M1-M12 | 2000-01-01 | 2001-01-01 |
| M3-M24 | 2000-03-01 | 2002-01-01 |
| M12-M30 | 2000-12-01 | 2002-07-01 |

A milestone or deliverable due in M12 occurs on `2000-12-31` in the relative calendar.

State above the Gantt whether dates are actual calendar dates and name their source, or are illustrative relative project months.

Group entries by WP or action. Include:

- every target-led task with reliable timing, labelled `[Task lead]`;
- every major non-led task with reliable timing, labelled `[Major task]`;
- every target-led milestone with a reliable date, labelled `[Milestone]`;
- every target-led deliverable with a reliable due date, labelled `[Deliverable]`.

Render milestones and deliverables as Mermaid `milestone` entries with unique, simple IDs and a `0d` duration. Render tasks as date-range bars. List led outputs and tasks with missing or unclear timing below the diagram; do not invent dates.

Use the led-output register to ensure that every organisation-led deliverable and milestone appears either in the Gantt or in the omitted-timing list.

## 11. Handle common proposal problems

### Repeated reporting-period WPs

Some proposals repeat management or communication WPs for each reporting period. Combine them when their duties are identical. Keep them separate when the scope, lead or timing changes.

### Missing dates

Write “timing not stated.” Omit the activity from the Gantt and retain it in the led-output table and omitted-timing list. Never infer a task range from a deliverable date alone unless the proposal says the deliverable covers the task period.

### Numbering errors

Preserve the printed identifier and flag the inconsistency. Use the task title to avoid confusion.

### Drafting placeholders

Treat highlighted placeholders, blank months and unresolved partner names as source limitations. Do not resolve them by guesswork.

### Programme differences

Horizon Europe proposals commonly use WPs, tasks, deliverables and KERs. LIFE proposals may use actions, milestones, indicators and continuation plans. Apply the same role and boundary logic to the source's terms.

## 12. Final evidence check

Before writing:

- verify every core role against at least the task heading and task body;
- cross-check every target-led deliverable and milestone for ownership and timing;
- check the effort table when reliable;
- confirm methodology links in the methodology text;
- confirm partner boundaries in the same task or a directly related task;
- confirm that every implied capability is licensed by quoted duty wording;
- confirm that no capability in the profile belongs only to another partner's duty;
- record any unresolved conflict.
