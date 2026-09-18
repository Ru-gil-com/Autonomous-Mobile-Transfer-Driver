# Project brief: Autonomous Mobile Transfer Driver

**Status:** Discovery draft | **Candidate airport:** Dallas Love Field (DAL), Texas, United States | **Airline:** Rugil Airlines (example for this project, not an identified operating carrier) | **Ground handler and vehicle supplier:** To be confirmed

## Product vision and boundaries

Enable authorized baggage and empty-cart trips to move between approved airport points without a person steering. This brief compares a **supervised autonomous driving pilot** with a later **automated transport and coupling scenario**. People load and unload bags in both scenarios. Neither scenario claims the entire baggage journey is autonomous.

**Current state:** An MTD agent drives the vehicle and physically moves bags, including unloading and loading at aircraft. The existing **MTD app captures bag loading and unloading information**. The transfer process is otherwise manual. The app's fields, scan method, trip assignment, live vehicle location, routing, alerting, and support for the other two trip types have not been established; none is assumed here.

The three in-scope trip types are **aircraft-to-aircraft transfer bags**, **baggage area-to-aircraft bags**, and **empty-cart moves**. Their exact origins, destinations, and handoff events need mapping. The vehicle does not decide which bags belong on a flight, allocate aircraft stands, or prove bags were loaded merely because it arrived.

| Trip type | Physical job outcome | Bag record relationship |
| --- | --- | --- |
| Aircraft to aircraft | Transfer bags reach the receiving aircraft handoff point for human unloading/loading | Preserve the MTD app's actual loading and unloading records; validate how origin and destination flights and bags are linked |
| Baggage area to aircraft | A prepared load reaches the assigned aircraft handoff point for human unloading/loading | Establish which existing application records preparation, release, and aircraft loading |
| Empty-cart move | A specified empty cart reaches an authorized receiving or staging point | No bag-level tracking event should be invented |

### Problem and intended users

Routine trips depend on driver availability; changing destinations and obstructed routes require coordination. Bag loading and unloading entries in the MTD app do not, by themselves, establish live vehicle location, cart identity, custody acceptance, or trip progress. Confirm what the app actually records. Automation introduces risks including wrong load or destination, uncertain positioning, unsafe coupling, connectivity loss, occupied handoff space, and vehicle recovery.

Primary users and accountable partners are MTD agents and app users, transfer dispatchers, ramp and baggage staff, fleet supervisors, Dallas Love Field's airport operator, the proposed Rugil Airlines team, a ground handler, and the vehicle supplier. No airline or handler procedures have been provided. Roles and authorities remain to be agreed.

## Work to automate

| Transfer step | Target capability | Evidence and human decision to define |
| --- | --- | --- |
| Accept and prioritize | Receive a job with trip type, origin, approved destination, deadline, and priority; link a flight leg and load when applicable | Source of job and authority for reassignment; how the MTD app participates |
| Identify and release load | Verify cart or container ID, flight association, readiness, and permission to move | Scan or other proof; handling of mismatch |
| Couple and check | Connect carts, verify attachment and towing limits, and confirm the path is clear | Human attachment and detachment for pilot; robotic coupling is a later option |
| Transport | Select an approved route, localize, yield as required, detect hazards, and stop safely | Authorized operating area, route rules, vehicle telemetry |
| Manage change | Respond to stand change, cancellation, closure, occupied handoff, lost communications, or fault | Rule for hold, reroute, alert, intervention, and recovery |
| Load, unload, and hand off | Position safely; people unload or load bags and detach carts; record the receiving step | App's actual bag event semantics and custody acceptance to discover; robotic coupling is a separate later option |
| Close and reconcile | Record job outcomes and exceptions with timestamps | Separate events for vehicle arrival, load acceptance, and baggage loading |

**Terminology:** The pilot is *autonomous driving with people performing bag loading and unloading, cart attachment and detachment, and handoff*. A trained human also monitors the vehicle under the approved test plan. A later scenario could automate cart coupling and release while people still handle bags. Call it *automated transport and coupling*, not full end-to-end baggage handling. Truly unattended baggage transfer would require separate solutions for physical bag handling, receiving custody, exceptions, and authorization; it is not committed scope. Every autonomy claim must specify trip type, route, conditions, human tasks, and supervision model.

The original ambition to make the **whole MTD role autonomous** remains a possible long-term vision. It requires a separate decision on bag loading/unloading automation, load identity, physical handoff, and acceptable human oversight. The agreed pilot and coupling scenario deliberately retain human bag handling; no claim of full role replacement follows from either one.

## First pilot: controlled transfer

The candidate airport is **Dallas Love Field (DAL)**. Propose named, approved non-movement-area routes and handoff points, subject to City of Dallas airport authorization and FAA coordination. Select the number of vehicles, shifts, weather conditions, loads, and towing configurations with the partners. A trained and appropriately badged monitor near the vehicle with the ability to take control is the starting operating concept for testing around active aircraft and personnel, subject to the airport's approved plan. People attach and detach every cart, load and unload bags, and accept custody. The pilot tests **autonomous driving with supervised physical work**, measuring all human labor. It must not be reported as a fully autonomous transfer.

**Pilot entry gates:** agreed route and workflow map; airport approval and applicable FAA coordination; documented hazards and mitigations; validated load, destination, coupling, and serviceability checks; safe-stop, intervention, and recovery procedures; trained people; secure commands and logs; and baseline measurements. Do not launch live trips merely because the vehicle can navigate a test route.

**Pilot acceptance criteria (thresholds to be agreed after baseline):** a correctly identified load reaches and is accepted at its verified destination for bag trips; an empty cart reaches its authorized receiving point for empty-cart trips; travel stays within approved conditions; unsafe uncertainty triggers a safe stop and alert; operators can see and audit status and intervene; fallback transport can complete an interrupted job. Record the frequency and duration of every intervention.

## Operational sequence and event model

1. **Authorize job:** Resolve trip type, origin, deadline, and current physical handoff point; require a unique flight leg and load ID for bag trips, and a cart ID for empty-cart trips. Reject missing, stale, or conflicting required data.
2. **Prepare departure:** Verify vehicle condition, permitted towing configuration, load association, coupling, available route, and required monitor or staff presence. Record who or what performed each check.
3. **Release and travel:** Dispatch authorizes movement; fleet control reports position, ETA, health, and route state. Alternative routes must be explicitly approved for the operating area.
4. **Manage exceptions:** For a stand change, cancellation, blocked route, occupied destination, sensor fault, or lost link, apply the approved hold/stop rule and alert the named operator. Never infer permission to enter a restricted area.
5. **Arrive and hand off:** Record `vehicle_arrived`, `load_presented`, `load_accepted`, and, if obtained from the baggage system, `bags_loaded` as distinct events for bag trips. Record cart receipt for empty-cart trips. Record failed handoff and recovery separately. Define how the MTD app closes each job type.

Every job event should carry a unique job ID, flight-leg and load identifiers where available, vehicle ID, source, timestamp, location, actor/system, and correction history. The authoritative owner and retention rule for each field remain to be confirmed.

| Exception | Proposed safe behavior for design review | Decision owner to name |
| --- | --- | --- |
| Stand change or cancellation | Validate the new physical destination and authorized route; hold or stop if either is uncertain | Dispatch and airline/airport stand authority |
| Blocked route or shared narrow space | Use only a preapproved alternative; otherwise stop in an approved safe place and alert | Airport route owner and fleet supervisor |
| Wrong cart/load or incomplete coupling | Prevent vehicle release; log mismatch and request physical correction | Dispatch and ramp lead |
| Occupied handoff area | Wait only at a designated location; do not obstruct aircraft or emergency access | Ramp coordinator and airport operator |
| Lost link, localization/sensor fault, or breakdown | Execute validated safe stop, alert, recover vehicle and reassign load under a manual fallback | Vehicle supplier and on-site recovery lead |

These are product proposals, not issued DAL procedures. Agree precise stop positions, timing, communication, and human authority through the airport's safety review.

## Regulatory, standards, and partner requirements discovery

This is a **requirements inventory for a proposed DAL pilot**, not an operating authorization. The cited DAL rules are the publicly linked revision dated March 2, 2023; airport staff must confirm current rules, directives, certification manual, and whether the selected route crosses leased areas or regulated boundaries. Legal and safety owners should validate applicability and current editions before design approval. FAA advisory guidance and IATA publications have different status from federal regulations and airport rules.

| Layer | Starting source and relevance | Product decision and owner to confirm |
| --- | --- | --- |
| FAA autonomous ground vehicle guidance | [FAA AGVS program](https://www.faa.gov/airports/new_entrants/agvs_on_airports) and [Emerging Entrants Bulletin 25-02 (2025)](https://www.faa.gov/airports/new_entrants/bulletins/25_02): current FAA framework for controlled non-movement-area testing; day-to-day baggage-tow use is treated as a testing activity with instantaneous human takeover capability; nearby trained monitor recommended around active aircraft/people. FAA FAQ also distinguishes operational testing in movement areas closed to aircraft from active movement-area use. | DAL airport sponsor and FAA airport certification/safety contact: route, test classification, monitor position and control, approvals, and any future change in supervision |
| Federal airport certification | [14 CFR § 139.329](https://www.ecfr.gov/current/title-14/chapter-I/subchapter-G/part-139/subpart-D/section-139.329) governs access, control, training, and records in movement and safety areas; the [FAA bulletin](https://www.faa.gov/airports/new_entrants/bulletins/25_02) cites § 139.335(a)(1) safeguards against unauthorized entry | DAL airport certification team: actual route boundaries, positive exclusion of movement/safety areas, airport certification manual, and required records |
| Vehicle operation guidance | [FAA AC 150/5210-20A](https://www.faa.gov/airports/resources/advisory_circulars/index.cfm/go/document.information/documentID/1028089) informs airport ground vehicle training and operating procedures | Airport operator: applicable local driver, right-of-way, marking, lighting, and training rules; advisory circular applicability |
| DAL airport rules | [DAL Airport Rules and Regulations, rev. 2 (2023)](https://www.dallas-lovefield.com/home/showpublisheddocument/2775/638136918027270000), §§ 5.9 and 8.9: aircraft and pedestrian right-of-way; approved ramp/VSR use; AOA speed limits of 20 mph on VSR outside Terminal Area and 15 mph in Terminal Area and ramps; maximum four containers/carts/pods towed; vehicle authorization, identification, training, permitted parking, and secured GSE. Access to leased areas requires leaseholder permission (§ 5.9.26). | Airport operator, tenant, and supplier: validate current text and applicability to an autonomous vehicle, approved routes/speeds, cart train, stopping/charging/staging locations, monitor credentials, insurance, and handling of any exception or variance |
| Radio and infrastructure | [FAA Bulletin 25-02](https://www.faa.gov/airports/new_entrants/bulletins/25_02) describes FAA Form 7460-1 aeronautical review for AGVS radio emitters/associated infrastructure and relevant FCC authorization or exception | Airport, supplier, and communications owners: equipment inventory, frequencies, filing, and determination before operation |
| IATA baggage tracking | [IATA Resolution 753 overview](https://www.iata.org/en/programs/ops-infra/baggage/baggage-tracking/) identifies tracking at passenger acceptance, aircraft loading, delivery to transfer area, and passenger return, and interline sharing where applicable. [Implementation guide](https://www.iata.org/contentassets/5316edd3aafb4866876e37883211cfc4/baggage_tracking_implementation_guide.pdf) gives implementation context. A vehicle arrival is not a substitute for a bag scan or transfer event. | Future real airline partner and handler: member/contract obligations, existing MTD event meaning, bag ID linkage, transfer-area and aircraft-load scans, and data exchange |
| IATA ground operations guidance | [IGOM](https://www.iata.org/en/publications/manuals/iata-ground-operations-manual/) covers baggage, ramp safety, and airside oversight; [AHM](https://www.iata.org/en/publications/manuals/airport-handling-manual/) addresses ground handling policy and GSE, with baggage material also referenced to IATA's Baggage Reference Manual. Full clauses require current licensed editions. | Airline and handler: which manuals and clauses are adopted in their procedures or contracts; IATA guidance is not itself a U.S. regulation |
| Airline, handler, and airport local rules | Rugil Airlines is an example, so no airline station/ramp/baggage manual or ground handling agreement has been identified. DAL security plan, current airport directives, and supplier operating limits must be obtained from authorized owners. | Each owner: load release, cart limits, aircraft clearance, coupling, handoff, incident response, insurance, maintenance, and labor procedures; do not invent an example-airline policy |

**Regulatory planning implication:** The FAA bulletin explicitly describes day-to-day baggage-tow use as a testing activity when a human can regain instantaneous control, and recommends nearby human monitoring around active aircraft and personnel. Full unattended operation requires a separate, documented evaluation and agreement with the airport and relevant authorities; this brief does not presume approval.

## Systems and data dependencies

| Capability / potential system | Data or function | Initial need |
| --- | --- | --- |
| Existing MTD app and dispatch/work management | MTD app currently captures bag loading/unloading information; job creation, priority, assignment, cancellation, trip status, and other trip types are unverified | Preserve existing bag events; map current data and owners before defining vehicle or dispatch integrations |
| Flight and stand source (AODB/resource manager) | Unique flight leg, current stand, approved physical stopping point, change timestamp | Verified destination required; live feed needed for dynamic changes |
| Load identity (cart tracking, BRS, BHS, scans) | Cart/load/flight association, readiness, custody, baggage tracking where claimed | Validated load ID required; depth of automated integration depends on pilot claim |
| Fleet, vehicle, map, and maintenance | Vehicle health, coupling state, position, approved routes and closures, stop and recovery controls | Essential to pilot |
| Operator console and ramp staff workflow | Status, alerts, acknowledgments, interventions, release and receiving acceptance | Essential to pilot |
| Turnaround and airline load control | Timing priorities, offloads, loading windows | Conditional; system remains authoritative for baggage decisions |
| Network, identity, and monitoring | Connectivity, authenticated commands, audit log, outage alerting | Essential to pilot |
| Baggage tracing, e.g., NetTracer | Investigation reference after mishandling | Later exception use, not core transport dispatch |

MTD is an existing app, though its implementation details are unknown. “Commodity Tracking,” “Turn,” and “Gate” remain unverified product names. Inventory each actual application, owner, authoritative fields, interface, update delay, outage behavior, and approval rights.

**Integration principle:** An aircraft stand label must resolve to a surveyed, approved physical stop point; an aircraft-to-aircraft job needs an origin flight and receiving flight. Record trip assignment, cart identity, vehicle arrival, physical handoff, and bag loading/unloading as distinct facts with their actual source systems. Do not mark a bag delivered solely from GPS arrival. Before connecting MTD, inspect sample records and document the direction, frequency, freshness, and outage behavior of each data feed.

## Outcomes and safeguards

| Measure | Working definition |
| --- | --- |
| Correct, on-time trip | Bag trips whose identified load is accepted at the correct point, and empty-cart trips whose cart is received, by deadline ÷ eligible jobs started; report each trip type separately |
| Autonomous driving completion | Jobs completed without a human steering or remote driving ÷ eligible jobs started; report manual coupling, bag handling, and oversight separately |
| Human effort per trip | Total minutes of monitoring, bag loading/unloading, coupling, handoff, intervention, and recovery ÷ eligible jobs started; split routine and exception effort |
| Exception response | Time from alert to acknowledgement and to resolution, by reason |

Guardrails: review collisions, injuries, unsafe movement, and emergency stops individually; track wrong-load/wrong-stand events, near misses, abandoned trips, recovery and fallback use, and lost or conflicting custody records. Measure against current human-operated trips before setting numerical thresholds. Agree on exclusions and attribution before comparing modes.

## Delivery progression

| Stage | Demonstrable outcome | Advancement gate |
| --- | --- | --- |
| 0. Discovery and approval design | Driver shift map, route survey, rule-to-workflow register, system-of-record matrix, baseline | Named owners resolve route, operating concept, approval path, and critical hazards |
| 1. Supervised driving pilot | One or more of the three eligible trip types on approved routes; nearby trained monitor, human coupling and bag handling, safe exceptions; connect to MTD app only after its interfaces are confirmed | Safety and service criteria met with all human effort measured |
| 2. Connected transfer | Live dispatch and stand data, load identity, chain-of-custody events, coordinated recovery | Reliable data freshness and handoff evidence |
| 3. Coupling feasibility and pilot | Test robotic coupling/uncoupling for selected standardized carts while retaining human bag handling | Correct cart selection, secure connection under towing loads, failure detection, safe release and recovery, approved procedures |
| 4. Expanded autonomous operation | More eligible routes and conditions, fleet coordination, reduced routine monitoring where authorized | Airport and relevant authority acceptance of revised operating concept |

The stages are planning hypotheses, not a promise that approval or physical task automation is available on a set date.

## Constraints, exclusions, and open decisions

**Constraints:** Operate only within the authorized conditions and routes; stop safely when load, position, destination, route, coupling, or vehicle health cannot be trusted. Protect command access and operational data, retain an auditable decision trail, and provide an accessible operator workflow and manual recovery process. Budget and dates depend on supplier capability, route works, interfaces, staffing, and safety review.

**Outside the first pilot:** unattended operation; movement or safety areas under the proposed U.S. route concept; unapproved routes or weather; automatic physical coupling and detachment; unsupported loads; multiple airports; and automated bag loading/unloading. Coupling is a future feasibility workstream. Bag allocation, stand allocation, and lost-baggage claims remain decisions in their respective operational systems.

**Decisions requiring named owners:**

- **Airport and route:** DAL airport sponsor selects route geometry, ramp/VSR and leased-area boundaries, conditions, stopping points, current local rules, and test approval pathway.
- **Role and workflow:** proposed handler and MTD process owner document the three in-scope trip types, manual steps, app fields, workarounds, and custody ownership; return trips with bags are outside the stated scope unless added later.
- **Airline acceptance:** identify a real airline partner before any operational pilot; Rugil Airlines is an example only. Its hypothetical requirements cannot stand in for an operating airline's release, scanning, transfer deadline, or handoff rules.
- **Vehicle design:** supplier demonstrates towing limits, coupling, obstacle response, localization, safe stop, lost-link behavior, and recoverability.
- **Systems:** partner owners identify actual products and source of truth for trip, stand, cart, route, vehicle, and baggage events.
- **Commercial:** sponsors agree pilot cost, dates, service support, measurable gates, and expansion decision rights.

**Baseline and operating details still needed:** current trips per shift by type, route and towing patterns, driver time, loading/unloading and coupling time, deadline performance, transfer connection window, stand changes, obstructions, weather/visibility, alert staffing, location of permitted waiting areas, and manual recovery time. No numerical target, budget, launch date, or robot supplier is asserted in this brief.

**First discovery deliverable:** A current-state shift and transfer map, surveyed pilot route, rule-to-workflow matrix, and system-of-record matrix. Each requirement should record its source and edition, whether it is binding or adopted guidance, owner, evidence to retain, unresolved question, and approval status.

**Source review:** Public FAA, IATA, and DAL materials were checked on September 18, 2026. Their linked text is a starting point for the named owners to validate against current controlled documents and the final vehicle and route design.
