# EDGE SENTINEL REPORT BLUEPRINT v2.1

## Technically Reconciled Thesis Blueprint with Methodology Model

## 1. Purpose of This Blueprint

This blueprint reconciles the original Edge Sentinel report draft with the current implemented and evaluated system. It replaces the earlier simple-MVP blueprint with the updated technical reality of the project while preserving the academic identity, resilience-first framing, Nigerian deployment motivation, and literature-based foundation already established in Chapters One and Two.

The report should now present Edge Sentinel as:

> A resilience-oriented, edge-based intelligent visual surveillance prototype for infrastructure-constrained environments, using local person detection, anonymous multi-object tracking, configurable rule-based behavioural indicators, local event evidence, persistent alert queueing, HTTP recovery, and GSM/SMS alert capability.

The strongest demonstrated contribution is not generic “AI surveillance,” but a **modular edge-surveillance architecture that preserves local event reasoning and alert records when remote communication is unavailable or interrupted**.

---

# 2. Final Project Identity

## 2.1 Recommended Project Title

**Development of Edge Sentinel: A Resilient Edge-Based Intelligent Visual Surveillance System for Infrastructure-Constrained Environments**

Alternative:

**Development of a Resilient Edge-Based Intelligent Surveillance System for Infrastructure-Constrained Environments**

The first title is preferred because it preserves the project name while remaining academically clear.

---

## 2.2 Correct Final Framing

Edge Sentinel should be framed as:

* edge-based;
* resilience-oriented;
* infrastructure-aware;
* non-identity-based;
* modular and configurable;
* capable of local detection, anonymous tracking, rule-based event reasoning, evidence generation, alert queueing, and fallback notification.

It should not be framed as:

* a facial recognition system;
* a general human activity recognition model;
* a universal anomaly detector;
* a full cloud surveillance platform;
* a proven 24/7 solar-powered deployment;
* a fully validated pitch-dark night surveillance system;
* a system with guaranteed GSM/SMS delivery latency;
* a system with measured power consumption in watts.

---

# 3. Claim Boundaries

## 3.1 Claims Supported by Current Evidence

The report can safely claim that:

1. Edge Sentinel performs local person detection and rule-based behavioural-event reasoning on a Raspberry Pi 5.

2. EfficientDet-Lite0 + ByteTrack is the selected reference detector–tracker pair.

3. Anonymous tracking is used for continuity, not identity recognition.

4. Five behavioural indicator families are implemented:

   * restricted presence/access;
   * lingering;
   * crowd density;
   * tailgating;
   * abnormal motion.

5. A labelled behavioural scenario evaluation was completed with:

   * 9 scenarios;
   * 8 correct outcomes;
   * 1 false negative;
   * 0 false-positive scenarios;
   * scenario-level precision of 1.000;
   * scenario-level recall of 0.875;
   * scenario-level F1 of approximately 0.933.

6. The false negative occurred in `abnormal_motion__running_past_frame`.

7. YOLOv8n + ByteTrack was tested only as a sensitivity analysis for the difficult running-past case and did not replace EfficientDet-Lite0.

8. Sustained Raspberry Pi saved-video profiling was completed.

9. The profiling run showed approximately 22.72 processed FPS under saved-video workload conditions, with no throttling, no undervoltage, no dropped or failed frames, and clean shutdown.

10. Alert queue recovery was evaluated using a simulated HTTP endpoint outage.

11. Ten generated alerts were queued during outage and all ten were delivered after recovery, with no loss or duplication.

12. IMX219 NoIR CSI camera capture was verified.

13. HC-SR501 PIR state transition was verified on GPIO17.

14. SIMCOM A7670G SMS delivery was functionally verified through `/dev/ttyUSB2` at 115200 baud.

15. The system is modular and configurable.

---

## 3.2 Claims Requiring Bounded Wording

| Claim Area             | Safe Wording                                                                                                              |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| Real-time operation    | “near-real-time under the evaluated saved-video workload” or “real-time-oriented,” not universal real-time proof          |
| Resilience             | “alert retention and recovery under simulated HTTP endpoint outage,” not all possible network/power failures              |
| Offline operation      | “local inference, logging, and queueing continue without immediate remote delivery”                                       |
| Adaptive communication | “HTTP delivery, persistent queue/retry, and SMS capability,” not automatic Wi-Fi-to-GSM data switching unless implemented |
| Low-light capability   | “NoIR camera and low-light motivation,” not proven pitch-dark performance                                                 |
| Power-conscious design | “PIR-assisted activation and no throttling/undervoltage in profiling,” not measured watts or proven solar endurance       |
| Privacy                | “non-identity-based and privacy-conscious,” not a formal privacy/security audit                                           |
| SMS                    | “functionally verified,” not guaranteed reliability or latency                                                            |

---

## 3.3 Claims That Must Not Appear as Completed Achievements

Do not claim:

* detector mAP;
* detector accuracy;
* detector precision/recall/F1;
* MOTA, IDF1, or HOTA;
* perfect anomaly detection;
* all scenarios passed;
* YOLOv8n solved the running-past case;
* measured power consumption in watts;
* verified solar/battery autonomy;
* proven pitch-dark surveillance;
* full physical network-disconnection validation;
* guaranteed GSM/SMS latency or success rate;
* deployed cloud backend;
* remote multi-device account management;
* automatic cloud synchronization;
* live-camera FPS inferred from saved-video profiling.

---

# 4. Revised Aim and Objectives

## 4.1 Revised Aim

The aim of this project is to develop and evaluate a resilient edge-based intelligent visual surveillance prototype that performs local person detection, anonymous tracking, rule-based behavioural-event reasoning, and persistent alert handling for infrastructure-constrained environments.

---

## 4.2 Revised Objectives

The project should retain four objectives.

### Objective 1

To design an edge-based surveillance architecture that performs local person detection, anonymous tracking, and rule-based behavioural-event reasoning without requiring continuous cloud connectivity.

### Objective 2

To implement configurable and explainable rule-based indicators for restricted presence, lingering, crowd density, tailgating, and abnormal motion using local detection, tracking, zone, duration, count, transition, and threshold data.

### Objective 3

To develop a local event-logging and alert-delivery mechanism that persistently queues failed HTTP alerts, retries delivery after endpoint recovery, and supports GSM/SMS notification through an AT-command modem.

### Objective 4

To evaluate the prototype using detector–tracker pair screening, labelled behavioural-event scenario outcomes, sustained Raspberry Pi resource profiling, and alert retention and recovery during a simulated HTTP endpoint outage.

---

# 5. Chapter One Revision Plan

Chapter One does not need a complete rewrite. It needs targeted factual patches.

## 5.1 Preserve

Keep:

* resilience-first motivation;
* Nigerian/infrastructure-constrained context;
* passive CCTV limitations;
* edge computing rationale;
* low-light as a deployment challenge;
* privacy-conscious, non-identity framing;
* four-objective structure.

## 5.2 Patch

Update:

1. Replace “local human activity detection” with “local person detection and rule-based behavioural-event reasoning.”

2. Mention anonymous tracking where the implementation pipeline is summarized.

3. Replace YOLOv8n-only wording with EfficientDet-Lite0 + ByteTrack as the reference pair.

4. Expand indicator examples from restricted-zone, lingering, and crowd density to include tailgating and abnormal motion.

5. Bound power claims: power instability motivates the work, but complete power-failure or solar autonomy was not demonstrated.

6. Bound low-light claims: NoIR camera capture was verified, but controlled low-light/pitch-dark evaluation was not completed.

7. Change methodology language from purely prospective to implementation/evaluation-aware.

---

# 6. Chapter Two Revision Plan

Chapter Two remains valid as a literature review, but some terminology must be updated.

## 6.1 Preserve

Keep:

* intelligent surveillance foundation;
* low-light vision section;
* object detection section;
* anomaly detection and behavioural reasoning section;
* edge AI and optimization section;
* Nigerian-context papers;
* research gap synthesis.

## 6.2 Patch

Update Chapter Two to:

1. Add ByteTrack and lightweight multi-object tracking literature support.

2. Explain why tracking is important for lingering, tailgating, transition analysis, and abnormal-motion rules.

3. Distinguish EfficientDet architecture literature from the deployed EfficientDet-Lite0 artifact.

4. Treat YOLOv8n and YOLOv5n as lightweight alternatives, not final selected models.

5. State that EfficientDet-Lite0 + ByteTrack later became the selected reference pair, but avoid reporting project results in Chapter Two.

6. Remove any implication that tailgating and abnormal motion are merely future work.

7. Keep low-light as motivation, not as proven project performance.

8. Preserve the resilience gap but avoid implying tested solar, power-failure, cloud, or full physical network-failure resilience.

9. Verify final bibliographic details for Nasir et al., Aliyu & Airoboman, Nte et al., Elmetwally et al., EfficientDet, and ByteTrack.

---

# 7. Chapter Three — Revised Structure

## CHAPTER THREE: METHODOLOGY AND SYSTEM DESIGN

Chapter Three must now explain both the **development methodology** and the **implemented system design**.

The chapter should begin with the methodology model because supervisors often expect to see the development model before the technical system architecture.

---

## 3.1 Introduction

State that the chapter presents:

* the research and development methodology;
* the methodology model;
* requirements analysis;
* system architecture;
* hardware design;
* software architecture;
* detector–tracker selection;
* behavioural indicator design;
* alert resilience design;
* local interface design;
* evaluation methodology;
* ethical and privacy considerations.

---

## 3.2 Research and Development Methodology

Recommended methodology:

> **Iterative Prototype-Based Development Methodology**

This is the correct methodology because Edge Sentinel combines software, embedded hardware, computer vision, tracking, sensors, communication, resilience testing, and UI configuration. The system could not be fully designed and validated in one fixed linear sequence. It required repeated cycles of implementation, testing, evaluation, and refinement.

The methodology should be described as a practical SDLC approach, not simply as “Agile” or “Waterfall.”

### 3.2.1 Justification for the Methodology

Explain that the iterative prototype-based model was selected because:

* hardware behaviour could not be fully predicted before testing;
* detector and tracker choices required experimental screening;
* behavioural indicators needed scenario-based validation;
* alert resilience required simulated failure and recovery testing;
* UI and zone configuration evolved through implementation;
* the system had to remain modular and adjustable.

---

## 3.3 Methodology Model / SDLC Diagram

This section should include the methodology architecture diagram.

### Figure 3.1: Iterative Prototype-Based Development Model for Edge Sentinel

The diagram should show the project development lifecycle:

1. Problem Identification
2. Requirements Analysis
3. Literature Review and Technology Study
4. System Architecture Design
5. Prototype Implementation
6. Hardware and Sensor Integration
7. Module Testing
8. System Evaluation
9. Refinement and Correction
10. Final Prototype and Documentation

A feedback loop should connect:

> System Evaluation → Refinement and Correction → System Architecture Design / Prototype Implementation

### Recommended Diagram Interpretation

The diagram should show that the project did not follow a one-pass Waterfall process. Instead, the system evolved through repeated design and testing cycles.

Example refinements that can be mentioned:

* detector–tracker selection changed as evaluation evidence improved;
* ByteTrack became part of the final reference pipeline;
* EfficientDet-Lite0 became the selected detector;
* alert queue recovery became part of resilience testing;
* abnormal-motion running-past failure informed limitation reporting rather than overfitting the rule;
* PIR, camera, and SMS were verified as hardware components.

---

## 3.4 Requirements Analysis

Include:

* functional requirements;
* non-functional requirements;
* deployment requirements;
* hardware requirements;
* software requirements.

Tables:

* Table 3.1: Functional Requirements
* Table 3.2: Non-Functional Requirements
* Table 3.3: Hardware and Software Requirements

---

## 3.5 Overall System Architecture

Explain the system as a modular edge architecture.

Core layers:

1. Sensing layer

   * IMX219 NoIR CSI camera;
   * HC-SR501 PIR sensor.

2. Perception layer

   * EfficientDet-Lite0 detector;
   * ByteTrack tracker.

3. Configuration layer

   * zone configuration;
   * indicator settings;
   * thresholds.

4. Behavioural reasoning layer

   * restricted presence/access;
   * lingering;
   * crowd density;
   * tailgating;
   * abnormal motion.

5. Event and evidence layer

   * event records;
   * snapshots/evidence;
   * logs.

6. Communication and resilience layer

   * AlertManager;
   * persistent queue;
   * HTTP delivery/retry;
   * GSM/SMS capability.

7. Local interface layer

   * setup dashboard;
   * configuration UI;
   * event/evidence review.

Figure:

* Figure 3.2: Overall Architecture of Edge Sentinel

---

## 3.6 Hardware Design

Discuss:

* Raspberry Pi 5;
* IMX219 NoIR CSI camera;
* HC-SR501 PIR sensor;
* SIMCOM A7670G modem;
* optional IR illumination;
* storage;
* deployment power considerations.

Important wording:

* PIR, camera, and SMS were functionally verified.
* Optional IR illumination and solar/battery support are deployment directions unless formally tested.
* NoIR camera does not prove pitch-dark operation without adequate IR illumination.

Figure:

* Figure 3.3: Hardware Architecture

---

## 3.7 Software Architecture

Discuss:

* capture module;
* frame scheduling;
* detector abstraction;
* tracker abstraction;
* zone manager;
* indicator modules;
* event factory/schema;
* snapshot manager;
* alert manager;
* persistent queue;
* HTTP client;
* AT-command SMS client;
* local API/UI;
* configuration storage.

Figure:

* Figure 3.4: Edge Device Internal Processing Pipeline

---

## 3.8 Detection and Tracking Design

Explain:

* detector candidates;
* tracker need;
* why anonymous tracking is required;
* why DeepSORT was avoided;
* why ByteTrack is suitable;
* why EfficientDet-Lite0 + ByteTrack became the selected reference pair.

Important:

Do not claim detector mAP or tracking MOTA/IDF1/HOTA.

Metrics to discuss:

* detector latency;
* estimated detector–tracker timing;
* track continuity proxy;
* detection coverage proxy;
* scenario-level usefulness.

Table:

* Table 3.4: Detector–Tracker Candidate Screening Criteria

---

## 3.9 Behavioural Indicator Design

Explain each indicator.

Table:

* Table 3.5: Behavioural Indicators and Rule Logic

Columns:

* Indicator
* Input Used
* Rule Condition
* Output Event
* Evaluation Status

Rows:

* restricted presence/access;
* lingering;
* crowd density;
* tailgating;
* abnormal motion.

Important:

Tailgating and abnormal motion are implemented, but abnormal motion has one known short-crossing false negative.

---

## 3.10 Zone Configuration and Local Setup Interface

Explain:

* why zones are needed;
* how rules depend on zones;
* zone-specific configuration;
* local dashboard/setup UI;
* evidence review and alert queue visibility if applicable.

Figure:

* Figure 3.5: Zone-Based Configuration Concept

---

## 3.11 Event, Evidence, and Logging Design

Discuss:

* event type;
* timestamp;
* zone;
* track IDs;
* metrics;
* trigger reason;
* severity/confidence;
* snapshots;
* local logs.

Table:

* Table 3.6: Event Record Schema

---

## 3.12 Alerting and Resilience Design

Discuss:

* normal HTTP alert delivery;
* failed alert persistence;
* retry after recovery;
* SMS channel;
* local processing during remote endpoint outage;
* boundaries of evaluated resilience.

Figure:

* Figure 3.6: Alert Delivery and Queue Recovery Flow

Important wording:

* HTTP queue recovery was evaluated.
* SMS capability was functionally verified separately.
* This is not proof of all physical network failures or guaranteed SMS delivery.

---

## 3.13 Operational State Model

Include states such as:

* idle/ready;
* PIR-triggered/active;
* capture/schedule frame;
* detection;
* tracking;
* indicator evaluation;
* event generation;
* alert delivery/queueing;
* recovery retry;
* shutdown.

Figure:

* Figure 3.7: Operational State Machine

Do not include deep sleep, low-battery shutdown, or solar autonomy as implemented states unless evidence exists.

---

## 3.14 Evaluation Methodology

Split evaluation into four categories:

1. Detector–tracker screening
2. Behavioural scenario evaluation
3. Sustained Raspberry Pi resource profiling
4. Alert queue and recovery evaluation

Also include:

5. Functional hardware verification

   * camera;
   * PIR;
   * SMS.

Tables:

* Table 3.7: Evaluation Categories and Purpose
* Table 3.8: Scenario Evaluation Design
* Table 3.9: Resource Profiling Metrics
* Table 3.10: Alert Resilience Test Design

Important distinction:

* formal quantitative evaluation;
* diagnostic sensitivity experiment;
* functional verification;
* implementation demonstration.

---

## 3.15 Ethical, Privacy, and Scope Considerations

Discuss:

* no facial recognition;
* no identity classification;
* anonymous track IDs;
* event-based evidence;
* local processing;
* privacy-conscious operation;
* limitations of surveillance technology;
* need for lawful deployment and user awareness.

---

## 3.16 Summary of Chapter

Summarize the methodology and lead into Chapter Four.

---

# 8. Chapter Four — Revised Structure

## CHAPTER FOUR: IMPLEMENTATION, RESULTS AND DISCUSSION

Chapter Four should present only verified results and bounded discussion.

---

## 4.1 Introduction

Brief overview of implementation and evaluation results.

---

## 4.2 Experimental Environment and Evidence Integrity

Include:

* Raspberry Pi 5;
* IMX219 NoIR;
* EfficientDet-Lite0 + ByteTrack;
* saved-video workload distinction;
* clean Git/evidence archive mention if appropriate;
* evaluated artifacts.

Table:

* Table 4.1: Experimental Environment

---

## 4.3 Hardware Component Verification

Discuss:

* camera capture verified;
* PIR transition verified on GPIO17;
* SMS delivery verified through A7670G.

Table:

* Table 4.2: Hardware Verification Summary

Important:

Do not claim repeated reliability or latency testing.

---

## 4.4 Detector–Tracker Screening and Final Selection

Discuss:

* why detector–tracker pair matters;
* EfficientDet-Lite0 + ByteTrack selection;
* pair campaign;
* descriptive/proxy metrics.

Table:

* Table 4.3: Detector–Tracker Screening Summary

Do not report detector mAP or tracking benchmark metrics.

---

## 4.5 Behavioural Scenario Evaluation

Discuss:

* 9 labelled scenarios;
* 8 correct outcomes;
* 1 false negative;
* 0 false positives;
* precision, recall, F1.

Table:

* Table 4.4: Scenario Evaluation Results

Important:

Call these scenario/event-level metrics, not detector accuracy.

---

## 4.6 Running-Past False Negative Analysis

Discuss:

* abnormal_motion__running_past_frame;
* people were detected and tracked;
* tracks were too brief;
* sustained-duration requirement not met;
* rule was not over-tuned to pass one clip.

Table:

* Table 4.5: Running-Past Diagnostic Summary

---

## 4.7 YOLOv8n Sensitivity Experiment

Discuss:

* tested only on running-past edge case and negative control;
* stronger instantaneous speed evidence;
* still failed sustained-duration requirement;
* higher latency and lower throughput;
* did not replace EfficientDet-Lite0;
* no YOLOv5n follow-up required.

Table:

* Table 4.6: YOLOv8n Sensitivity Comparison

---

## 4.8 Sustained Raspberry Pi Resource Profiling

Discuss:

* saved-video workload;
* model loaded once;
* warm-up excluded;
* 60-second measurement;
* 22.72 processed FPS;
* 11.36 detector calls/s;
* detector latency;
* CPU/memory/temperature;
* no throttling;
* no undervoltage;
* no dropped/failed frames;
* clean shutdown.

Table:

* Table 4.7: Sustained Resource Profiling Results

Important:

Do not call this live-camera FPS.

Do not call it measured power consumption.

---

## 4.9 Alert Queue and Recovery Evaluation

Discuss:

* simulated HTTP endpoint outage;
* 10 alerts generated and queued;
* 10 delivered after recovery;
* 0 lost;
* 0 duplicates;
* final queue depth 0.

Table:

* Table 4.8: Alert Queue Recovery Results

Important:

* receiver was local;
* SMS disabled during this experiment;
* this is not full physical network disconnection validation.

---

## 4.10 Local Interface and Configuration Demonstration

Discuss:

* setup UI;
* zone configuration;
* indicator configuration;
* event/evidence review;
* alert queue visibility if implemented.

This section is implementation demonstration, not formal performance evaluation.

---

## 4.11 Discussion Against Objectives

Table:

* Table 4.9: Objective Achievement Summary

Rows:

* Objective 1: achieved;
* Objective 2: achieved;
* Objective 3: achieved within bounded scope;
* Objective 4: achieved within revised evaluation scope.

---

## 4.12 Limitations and Threats to Validity

Include:

* no detector mAP;
* no formal tracking MOTA/IDF1/HOTA;
* one abnormal-motion false negative;
* no controlled low-light campaign;
* no pitch-dark validation;
* no power meter or watt measurement;
* no solar/battery endurance test;
* no repeated PIR or SMS reliability study;
* no full physical network disconnection test;
* saved-video profiling, not live-camera profiling;
* no cloud backend/account management.

---

## 4.13 Summary of Findings

Summarize:

* what worked;
* what was validated;
* what remains bounded;
* why the project still satisfies its aim.

---

# 9. Revised Diagram List

## 9.1 Required Diagrams

1. **Iterative Prototype-Based Development Model**
   Shows the SDLC/methodology model used for the project.

2. **Overall System Architecture**
   Shows Pi 5, camera, PIR, EfficientDet-Lite0, ByteTrack, indicators, events, queue, HTTP, SMS, and UI.

3. **Hardware Architecture**
   Shows camera, PIR, modem, Pi, storage, optional illumination, and deployment power direction.

4. **Edge Device Internal Processing Pipeline**
   Capture → scheduling → detection → tracking → zones → indicators → events → logs/evidence → alerts.

5. **Zone-Based Configuration Concept**
   Shows monitored scene, configured zones, and per-zone rule assignment.

6. **Alert Delivery and Queue Recovery Flow**
   Normal HTTP delivery → failure → persistent queue → continued local operation → endpoint recovery → queue flush. SMS shown as separate fallback channel.

7. **Operational State Machine**
   Idle/ready → triggered/active → capture → inference → tracking → event reasoning → alert/logging → retry/recovery → shutdown.

---

## 9.2 Optional Diagrams

1. Use case diagram
2. UI navigation diagram
3. Deployment/network-resilience architecture
4. Detector–tracker evaluation flow

---

## 9.3 Diagram Production Strategy

For the supervisor meeting and early review:

* PlantUML diagrams may be used as placeholder diagrams.
* They should be treated as draft models for communication, not final presentation graphics.
* The placeholder diagrams should be technically correct even if visually plain.

For final submission:

* diagrams should be recreated or refined in draw.io/diagrams.net;
* diagrams should use consistent colors, spacing, fonts, captions, and labels;
* overly dense diagrams should be split into smaller diagrams;
* architecture diagrams should be readable when printed in the final report.

Suggested explanation if asked:

> The current diagrams are placeholder technical models used to validate the architecture and methodology. I am refining the final visual diagrams separately for the completed report.

---

## 9.4 Items That Must Not Be Shown as Implemented in Diagrams

Do not show as completed implementation:

* cloud synchronization;
* multi-device account management;
* solar autonomy;
* deep sleep power state;
* guaranteed GSM failover;
* concurrent production live streaming and inference;
* measured power-management subsystem;
* full remote cloud dashboard;
* pitch-dark performance guarantee.

---

# 10. Revised Table List

## Chapter Two

* Table 2.1: Low-Light Vision and Object Detection Literature Comparison
* Table 2.2: Video Anomaly Detection and Behavioural Surveillance Literature Comparison
* Table 2.3: Edge AI and Infrastructure-Constrained Security Systems Literature Comparison
* Table 2.4: Research Gap Synthesis and Edge Sentinel Response

## Chapter Three

* Table 3.1: Functional Requirements
* Table 3.2: Non-Functional Requirements
* Table 3.3: Hardware and Software Requirements
* Table 3.4: Detector–Tracker Candidate Screening Criteria
* Table 3.5: Behavioural Indicators and Rule Logic
* Table 3.6: Event Record Schema
* Table 3.7: Evaluation Categories and Purpose
* Table 3.8: Scenario Evaluation Design
* Table 3.9: Resource Profiling Metrics
* Table 3.10: Alert Resilience Test Design

## Chapter Four

* Table 4.1: Experimental Environment
* Table 4.2: Hardware Verification Summary
* Table 4.3: Detector–Tracker Screening Summary
* Table 4.4: Scenario Evaluation Results
* Table 4.5: Running-Past Diagnostic Summary
* Table 4.6: YOLOv8n Sensitivity Comparison
* Table 4.7: Sustained Resource Profiling Results
* Table 4.8: Alert Queue Recovery Results
* Table 4.9: Objective Achievement Summary
* Table 4.10: Limitations and Threats to Validity

---

# 11. Formatting Strategy

Continue drafting in Markdown.

Final `.docx` formatting should use:

* Times New Roman;
* 12 pt;
* justified alignment;
* proper chapter numbering;
* table captions above tables;
* figure captions below figures;
* landscape pages for wide tables where necessary.

Large Chapter Two, Three, and Four tables may need landscape orientation in the final document.

---

# 12. Next Work Order

The next actions should be:

1. Patch Chapter One using the revised aim/objectives and technical reality.
2. Patch Chapter Two to include tracking/ByteTrack and revised detector language.
3. Generate PlantUML placeholder models for the required Chapter Three diagrams.
4. Draft Chapter Three using this blueprint.
5. Draft Chapter Four using verified results from the handoff.
6. Return to Chapter Five and Abstract after Chapters Three and Four stabilize.

Do not draft Chapter Five before Chapter Four, because the conclusion must reflect the actual results.

---

# 13. Final Blueprint Assessment

The project is now strong enough to proceed into Chapters Three and Four.

The report no longer needs to defend only a proposed idea. It can defend an implemented and evaluated prototype, provided that claims remain carefully bounded.

The strongest final thesis argument is:

> Edge Sentinel demonstrates a modular, edge-based surveillance prototype that combines local person detection, anonymous tracking, explainable behavioural-event rules, local evidence handling, and persistent alert recovery, making it suitable as a resilience-oriented surveillance architecture for infrastructure-constrained environments.

This argument should guide the rest of the report.
