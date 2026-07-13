# EDGE SENTINEL REPORT BLUEPRINT v1

## Working Project Title

**Development of Edge Sentinel: A Resilient Edge-Native Intelligent Visual Surveillance System for Infrastructure-Constrained Environments**

Alternative shorter title:

**Development of a Resilient Edge-Native Intelligent Surveillance System for Infrastructure-Constrained Environments**

The first title is better because it preserves the project name, while the second is more academic.

---

# 1. Authoring Strategy

The report should be drafted in **Markdown first**, then transferred into Word/LibreOffice for final formatting.

Markdown will be used for:

* chapter drafting;
* restructuring sections quickly;
* AI-assisted editing;
* version control;
* reducing formatting distractions.

Final formatting will be done later in `.docx` using:

* Times New Roman;
* 12 pt font size;
* justified alignment;
* proper page numbering;
* table of contents;
* figure captions;
* table captions;
* references;
* appendices.

The report will not be written as a perfect formatted document from day one. The first goal is correct academic content. Formatting comes after the chapters stabilize.

---

# 2. Core Project Identity

The report must present Edge Sentinel as:

> A resilient edge-native intelligent surveillance system designed for infrastructure-constrained environments, using local AI inference, explainable rule-based security indicators, adaptive communication, and graceful degradation to maintain useful surveillance functionality under unreliable power and network conditions.

This is the central idea of the report.

The project should not be framed primarily as:

* a privacy-first surveillance system;
* a generic CCTV system;
* a purely cloudless camera;
* a facial recognition system;
* a large-scale commercial security platform;
* a fully completed industrial product.

Privacy remains part of the design, but the main contribution is **resilient edge surveillance under practical deployment constraints**.

---

# 3. Preliminary Pages

These should follow the departmental guide/template.

## Required Front Matter

1. Title Page
2. Declaration
3. Certification
4. Acknowledgements
5. Dedication
6. Abstract
7. Table of Contents
8. List of Tables
9. List of Figures
10. List of Abbreviations/Acronyms, if required

## Abstract Strategy

The abstract should be written last.

It should briefly cover:

* the problem of unreliable surveillance in infrastructure-limited environments;
* the aim of the project;
* the methodology;
* the implemented prototype;
* the major results or evidence;
* the contribution of the work.

The abstract should be one paragraph unless the department explicitly allows otherwise.

---

# 4. CHAPTER ONE — INTRODUCTION

## Purpose of Chapter One

Chapter One should introduce the project, justify why it matters, state the aim and objectives, and give a short methodology overview.

This chapter should not go deeply into technical architecture. It should prepare the reader for the rest of the report.

## Main Sources

* Edge Sentinel R2M Concept Note
* Old Project Proposal
* SRS
* Supervisor/HOD feedback
* Current project direction from this chat

## Proposed Chapter One Structure

### 1.1 Background to the Study

Content to include:

* growth of intelligent surveillance systems;
* limitations of conventional CCTV;
* dependence of many surveillance systems on stable power, broadband internet, cloud access, and human monitoring;
* relevance to Nigerian and infrastructure-constrained environments;
* need for local intelligence and resilient alerting;
* brief introduction of edge computing in surveillance.

This section should gradually lead toward Edge Sentinel.

### 1.2 Research Motivation

Content to include:

* conventional surveillance may fail silently when internet or power is unreliable;
* cloud-based systems may not be practical for schools, hostels, farms, warehouses, laboratories, and semi-off-grid facilities;
* human operators may not continuously monitor footage;
* intelligent surveillance should provide timely awareness, not just passive recording;
* the project is motivated by the need for a low-cost, locally intelligent, resilient surveillance node.

This section can also contain the problem statement, either explicitly or naturally.

### 1.3 Research Aim and Objectives

Recommended aim:

> The aim of this project is to develop a resilient edge-native intelligent visual surveillance system that performs local human activity detection, generates explainable security events, and delivers alerts through adaptive communication channels in infrastructure-constrained environments.

Recommended objectives should be limited to about four.

Proposed objectives:

1. To design an edge-based surveillance architecture capable of performing local person detection and event reasoning without continuous cloud connectivity.

2. To implement configurable rule-based security indicators such as restricted-zone presence, lingering, and crowd-density monitoring using lightweight computer vision outputs.

3. To develop an adaptive alerting and logging mechanism that supports local event storage, network-based alerts, and GSM/SMS fallback.

4. To evaluate the prototype in terms of detection performance, system responsiveness, alert delivery, resource usage, and suitability for infrastructure-constrained deployment.

These are cleaner than seven objectives and should satisfy the “not too many objectives” feedback from pre-defense.

### 1.4 Methodology

This should be a short overview, not the full Chapter Three.

Recommended framing:

The project follows an **iterative prototype-based software engineering methodology**.

Reason:

* The system combines hardware, software, AI inference, communication, and UI components.
* The design evolved through repeated implementation, testing, and refinement.
* Agile/prototyping fits better than strict Waterfall because hardware integration and model performance issues are discovered through experimentation.

Mention:

* requirements analysis;
* literature review;
* system architecture design;
* prototype implementation;
* module testing;
* Raspberry Pi deployment testing;
* evaluation.

### 1.5 Organization of the Project

Briefly describe Chapters 1–5.

---

# 5. CHAPTER TWO — LITERATURE REVIEW

## Purpose of Chapter Two

Chapter Two should establish the academic foundation for Edge Sentinel.

It should not simply list papers one after another. It should group papers by theme and end with a clear research gap.

## Main Sources

* Literature Review_ADSS
* NotebookLM outputs
* Original reference papers, if exact citations are needed

## Proposed Chapter Two Structure

### 2.1 Introduction

Briefly explain that the literature review covers computer vision for surveillance, low-light detection, anomaly/event detection, and edge deployment.

### 2.2 Intelligent Visual Surveillance Systems

Content:

* traditional CCTV;
* intelligent video surveillance;
* smart cameras;
* security monitoring;
* anomaly detection;
* limitations of passive monitoring.

### 2.3 Low-Light Computer Vision and Surveillance Robustness

Content:

* low-light conditions degrade detection;
* noise, blur, poor contrast, and illumination changes affect computer vision;
* low-light enhancement and machine-vision-driven enhancement;
* relevance to Edge Sentinel’s surveillance context.

Use literature review sources such as:

* Li et al.
* Ghari et al.
* Zhou et al.
* Hashmi et al.
* Qu et al.
* Tran et al.

### 2.4 Object Detection Models for Edge Surveillance

Content:

* object detection as the foundation of surveillance intelligence;
* YOLO family;
* EfficientDet;
* lightweight detection;
* tradeoff between speed, accuracy, and hardware constraints;
* why YOLOv8n is a reasonable MVP choice.

### 2.5 Video Anomaly Detection and Behavioural Event Reasoning

Content:

* anomaly detection in surveillance;
* complex temporal/neural models;
* crowd behavior detection;
* abnormal activity recognition;
* limitations of black-box anomaly models for low-cost edge devices.

Important angle:

Edge Sentinel does not try to solve full general video anomaly detection. It uses lightweight detection plus explainable rules.

### 2.6 Edge AI Deployment and Model Optimization

Content:

* edge computing for surveillance;
* Raspberry Pi/low-cost edge devices;
* quantization;
* pruning;
* compression;
* TFLite;
* constraints of CPU-only deployment;
* need for low-latency and low-bandwidth systems.

Use literature review sources such as:

* Abu Awwad et al.
* Ali et al.
* Cheng et al.
* Gholami et al.
* Batzner et al.

### 2.7 Review of Related Systems

This should be a table.

Suggested columns:

* Author/Year
* System/Approach
* Deployment Platform
* Method Used
* Strength
* Limitation
* Relevance to Edge Sentinel

### 2.8 Summary of Research Gap

This is one of the most important sections.

The gap should be stated clearly:

> Existing intelligent surveillance research often emphasizes detection accuracy, anomaly recognition, or model efficiency, but gives less attention to integrated deployment in environments where power, connectivity, bandwidth, and human monitoring cannot be assumed. There is therefore a need for a resilient edge-native surveillance architecture that combines local inference, explainable event reasoning, adaptive communication, local logging, and fallback alert delivery.

This gap directly leads into Chapter Three.

---

# 6. CHAPTER THREE — METHODOLOGY

## Purpose of Chapter Three

Chapter Three explains how the system was designed.

This is the technical backbone of the report.

It should not read like a code manual. It should explain the methodology, architecture, model choice, system components, event logic, and evaluation methods.

## Main Sources

* SRS
* Agent.md
* Concept Note
* System Guardians implementation summary
* Existing PlantUML diagrams
* Future draw.io diagrams

## Proposed Chapter Three Structure

### 3.1 Overview of the Proposed System

Introduce Edge Sentinel as a modular edge surveillance system consisting of:

* sensing layer;
* perception layer;
* behaviour/event reasoning layer;
* evidence and logging layer;
* communication layer;
* user/admin interface.

### 3.2 Development Methodology

Recommended methodology:

**Iterative Prototype-Based Methodology**

Explain that the system was developed through cycles:

1. Requirement identification
2. Literature review and design refinement
3. Architecture design
4. Module implementation
5. Hardware bring-up
6. Integration testing
7. Evaluation and refinement

This is better than pretending the project followed strict Waterfall.

### 3.3 System Requirements

Summarize:

* functional requirements;
* non-functional requirements;
* hardware requirements;
* software requirements.

This should be derived from the SRS.

Suggested tables:

* Table 3.1: Functional Requirements
* Table 3.2: Non-Functional Requirements
* Table 3.3: Hardware and Software Requirements

### 3.4 System Architecture

This should include the main architecture diagram.

The architecture should show:

* Camera / PIR sensor
* Raspberry Pi 5
* Frame capture module
* YOLOv8n inference module
* Behavioural indicator modules
* Event manager
* Local storage
* Communication manager
* GSM/SMS fallback
* Optional dashboard/server

Diagram needed:

**Figure 3.1: Overall System Architecture of Edge Sentinel**

PlantUML can be used as draft, but final should be redrawn in draw.io.

### 3.5 Hardware Architecture

Content:

* Raspberry Pi 5;
* IMX219 CSI camera;
* PIR sensor;
* GSM/LTE modem;
* storage;
* power subsystem;
* optional solar/battery design.

Diagram needed:

**Figure 3.2: Hardware Architecture of Edge Sentinel**

This can be adapted from the existing physical architecture diagram.

### 3.6 Software Architecture

Content:

* capture module;
* inference module;
* indicator/event modules;
* communication module;
* logging module;
* dashboard/API/UI;
* configuration system.

Diagram needed:

**Figure 3.3: Software Architecture of Edge Sentinel**

### 3.7 Computer Vision Model Selection

Content:

* YOLOv8n selected for MVP;
* lightweight;
* suitable for CPU-only testing;
* detects persons as the main surveillance class;
* future optimization through TFLite/quantization may be discussed as future work or deployment path.

Important wording:

Do not overclaim that quantization is already completed unless it is actually completed.

Suggested table:

**Table 3.4: Candidate Model Comparison**

Possible columns:

* Model
* Strength
* Weakness
* Edge Suitability
* Decision

Models:

* YOLOv8n
* YOLOv5n
* EfficientDet-D0 / EfficientDet-Lite
* Larger YOLO models, rejected for MVP

### 3.8 Behavioural Event Reasoning

This section explains the rule-based indicators.

Indicators to include:

* restricted-zone presence;
* lingering detection;
* crowd-density monitoring;
* tailgating, if included as experimental/future;
* abnormal motion, if included as future/experimental.

Important:

Only implemented/stable indicators should be described as implemented. Experimental ones should be marked as planned or limited.

Suggested table:

**Table 3.5: Behavioural Indicators and Trigger Conditions**

Columns:

* Indicator
* Input Used
* Trigger Condition
* Output Event
* Status

### 3.9 Event Schema and Evidence Handling

Explain:

* every event contains what happened, where, when, measured value, threshold, and trigger reason;
* events are logged locally;
* snapshots may be captured for verified/security events;
* identity recognition is not used;
* privacy-conscious evidence handling.

Suggested figure/table:

**Table 3.6: Standard Event Schema**

### 3.10 Adaptive Communication and Resilience Model

This is a major contribution section.

Explain fallback order:

1. Wi-Fi/HTTP alert
2. GSM data/HTTP alert
3. SMS fallback
4. Local queue for delayed delivery

Include the principle:

> No event should be silently dropped.

Diagram needed:

**Figure 3.4: Adaptive Alert Delivery Flow**

### 3.11 System State Model

Explain system states:

* Idle;
* Triggered/Active;
* Detection;
* Event Processing;
* Alerting;
* Logging;
* Return to Idle.

Diagram needed:

**Figure 3.5: Edge Sentinel State Machine**

### 3.12 UML Diagrams and Flowchart

Minimum required diagrams:

1. Use Case Diagram
2. System Architecture Diagram
3. Detection-to-Alert Flowchart
4. State Machine Diagram

Optional diagrams:

* Sequence diagram for event alert flow;
* component diagram;
* deployment diagram.

Do not create too many diagrams unless the supervisor/template demands them.

### 3.13 Evaluation Methods

Evaluation should be practical and prototype-focused.

Recommended evaluation dimensions:

1. Detection performance

   * number of detections;
   * confidence scores;
   * false positives/false negatives where test scenarios allow;
   * precision/recall only if enough labeled test data exists.

2. System performance

   * FPS;
   * detector interval;
   * CPU usage;
   * memory usage;
   * latency from frame capture to event generation.

3. Alerting performance

   * HTTP alert success;
   * SMS alert success;
   * queued delivery behavior;
   * failed transmission handling.

4. Functional testing

   * restricted zone test;
   * lingering test;
   * crowd density test;
   * zone calibration test;
   * Pi camera capture test.

5. Resilience testing

   * operation without internet;
   * local logging when alert delivery fails;
   * fallback communication behavior.

Suggested table:

**Table 3.7: Evaluation Metrics and Measurement Method**

---

# 7. CHAPTER FOUR — IMPLEMENTATION, RESULTS AND DISCUSSION

## Purpose of Chapter Four

Chapter Four should show what was actually built, how it was implemented, and what results were obtained.

This chapter must be grounded in the real system status.

## Main Sources

* System Guardians implementation summary
* Real screenshots
* Logs
* Pi test results
* Camera capture evidence
* UI screenshots
* API responses
* Alert delivery tests

## Proposed Chapter Four Structure

### 4.1 Overview of the Chapter

Briefly state that this chapter presents implementation details, testing, results, and discussion.

### 4.2 System Requirements and Development Environment

Content:

* hardware used;
* operating system;
* Python version;
* libraries/frameworks;
* development laptop;
* Raspberry Pi deployment environment.

Suggested table:

**Table 4.1: Development and Deployment Environment**

### 4.3 Hardware Setup

Content:

* Raspberry Pi 5;
* IMX219 CSI camera;
* GSM/LTE modem;
* PIR sensor status;
* power setup status.

Include actual photos/screenshots if available.

### 4.4 Software Implementation

Break into modules:

* capture module;
* inference module;
* event/indicator module;
* zone calibration;
* logging;
* communication;
* UI/dashboard/API.

Suggested table:

**Table 4.2: Implemented System Modules**

Columns:

* Module
* Function
* Implementation Status
* Evidence

### 4.5 Model Implementation

Content:

* YOLOv8n;
* person detection;
* confidence threshold;
* CPU inference;
* Pi performance, if available.

Screenshots:

* detection result;
* terminal output;
* UI detection preview if available.

### 4.6 Behavioural Indicator Implementation

Content:

* restricted-zone detection;
* lingering detection;
* crowd density;
* other indicators if available.

Use scenario-based explanation.

Suggested table:

**Table 4.3: Indicator Test Scenarios**

### 4.7 Zone Calibration and Configuration

Content:

* why zone calibration matters;
* how zones are defined;
* how indicators apply to zones;
* screenshots of calibration UI.

This is important because it makes your system look configurable and practical.

### 4.8 Alerting and Logging Implementation

Content:

* event schema;
* local logs;
* HTTP alerts;
* alert queue;
* SMS/GSM status;
* screenshots/log samples.

Suggested table:

**Table 4.4: Alert Delivery Test Results**

### 4.9 Raspberry Pi Deployment Results

Content:

* camera bring-up;
* frame capture;
* detector run;
* FPS;
* CPU/memory/temperature if available;
* limitations encountered.

This section should be filled from System Guardians.

### 4.10 System Evaluation

Use whatever real results are available.

Possible result tables:

* detection test results;
* FPS/performance table;
* indicator scenario test table;
* alert delivery table;
* hardware bring-up table.

### 4.11 Discussion of Results

Explain:

* what worked;
* what did not fully work;
* how the results support the research aim;
* why the MVP is still valid;
* limitations of CPU-only edge inference;
* limitations of tracking/advanced behaviour detection;
* why resilience remains the main contribution.

---

# 8. CHAPTER FIVE — CONCLUSION AND RECOMMENDATIONS

## Purpose of Chapter Five

This chapter closes the report.

It should not introduce new technical material.

## Main Sources

* Concept Note
* SRS evaluation plan
* Actual implementation results
* Supervisor feedback
* Future work list

## Proposed Chapter Five Structure

### 5.1 Conclusion

Summarize:

* problem addressed;
* system developed;
* methodology used;
* major implementation achievements;
* key findings.

### 5.2 Contribution to Knowledge

Suggested contributions:

1. A resilience-first edge surveillance architecture for infrastructure-constrained environments.

2. An explainable rule-based event reasoning layer built on lightweight local person detection.

3. An adaptive communication model combining local logging, network alerts, GSM/SMS fallback, and delayed synchronization.

4. A practical Raspberry Pi-based prototype demonstrating feasibility of local surveillance intelligence under constrained deployment conditions.

### 5.3 Limitations

Possible limitations:

* limited field testing;
* CPU-only inference constraints;
* unstable tracking performance if no tracker is finalized;
* limited dataset/scene diversity;
* PIR/GSM/solar may be partially validated depending on final status;
* low-light performance may depend on camera and illumination conditions.

### 5.4 Recommendations

Recommendations may include:

* broader field testing;
* improved camera/IR setup;
* model optimization;
* tracker integration;
* solar/battery validation;
* stronger dashboard/mobile app;
* multi-camera support.

### 5.5 Suggestions for Further Research

Future research:

* optimized temporal anomaly detection;
* edge model quantization and benchmarking;
* distributed multi-node surveillance;
* low-light enhancement integration;
* robust tracking under low FPS;
* adaptive power-aware scheduling.

---

# 9. Minimum Diagrams Needed

Do not generate all diagrams immediately.

Generate them when their chapter requires them.

Minimum required diagrams:

1. **Overall System Architecture**
   Chapter 3

2. **Hardware Architecture / Physical Deployment Diagram**
   Chapter 3

3. **Software Component Architecture**
   Chapter 3

4. **Detection-to-Alert Flowchart**
   Chapter 3

5. **System State Machine**
   Chapter 3

6. **Use Case Diagram**
   Chapter 3

Optional if time allows:

7. Sequence Diagram for Alert Delivery
8. Zone Calibration Diagram
9. UI Navigation Diagram
10. Deployment Diagram

Final diagrams should be redrawn in draw.io, even if first drafted in PlantUML.

---

# 10. Minimum Tables Needed

Minimum required tables:

1. Functional Requirements
2. Non-Functional Requirements
3. Hardware and Software Requirements
4. Candidate Model Comparison
5. Behavioural Indicators and Trigger Conditions
6. Event Schema
7. Evaluation Metrics and Measurement Method
8. Implemented Modules
9. Test Scenarios and Results
10. Limitations and Future Work

These tables will make the report look structured and technical without requiring excessive prose.

---

# 11. NotebookLM Workflow

NotebookLM should be used only when Chapter Two needs literature evidence.

Ask NotebookLM for targeted outputs, not full chapters.

Recommended NotebookLM prompts:

## Prompt 1

Compare the uploaded papers on low-light computer vision and surveillance robustness. Focus on how low-light conditions affect object/person detection and what limitations remain for real-world deployment.

## Prompt 2

Summarize the papers on video anomaly detection and abnormal activity recognition. Focus on computational complexity, explainability, and suitability for low-cost edge devices.

## Prompt 3

Compare the edge AI and model optimization papers. Focus on Raspberry Pi or low-cost edge deployment, quantization, latency, FPS, power, and communication limitations.

## Prompt 4

Create a related works table for my final-year project on resilient edge-native intelligent surveillance. Columns: Author/Year, Method, Deployment Platform, Strength, Limitation, Relevance to Edge Sentinel.

NotebookLM outputs should be pasted back here. They will then be rewritten into proper Chapter Two prose.

---

# 12. System Guardians Workflow

System Guardians should be used when Chapter Three or Four needs implementation truth.

Recommended prompts:

## Prompt 1

Generate a current implementation summary for Edge Sentinel suitable for Chapter Four of a final-year project report. Include implemented modules, partially implemented modules, pending modules, hardware tested, known limitations, and evidence available.

## Prompt 2

Explain the current system pipeline from frame capture to event generation and alert/logging, based on the current codebase. Keep it accurate to what is implemented.

## Prompt 3

List the current project modules and their responsibilities in a table suitable for an academic report.

## Prompt 4

Summarize all current Raspberry Pi, IMX219 camera, GSM/SMS, UI, API, zone calibration, and detection pipeline tests that have been completed, including outputs or evidence where available.

Those outputs should be pasted here before drafting Chapter Three or Chapter Four.

---

# 13. Immediate Next Step

The next step is to draft **Chapter One**.

Chapter One can be drafted now because it mostly depends on:

* concept note;
* project proposal;
* SRS;
* project identity;
* supervisor feedback;
* current resilience-first framing.

No NotebookLM output is required before Chapter One.

Before drafting Chapter Two, NotebookLM should be queried.

Before drafting Chapter Three and Four, System Guardians should be queried.

---

# 14. Current Formatting Rules

Drafting format:

* Markdown headings;
* clear subsections;
* no final pagination yet;
* no forced final layout yet.

Final report format:

* Times New Roman;
* 12 pt;
* justified alignment;
* formal academic writing;
* properly numbered tables and figures;
* figure captions below figures;
* table captions above tables, unless department template says otherwise;
* consistent citation style.

Final `.docx` formatting should happen after the chapters are substantially complete.

---

# 15. Practical Build Order

Recommended order:

1. Chapter One draft
2. NotebookLM extraction for Chapter Two
3. Chapter Two draft
4. System Guardians extraction for Chapter Three/Four
5. Chapter Three draft
6. Required diagrams for Chapter Three
7. Chapter Four draft
8. Insert screenshots/results/logs
9. Chapter Five draft
10. Abstract
11. References cleanup
12. Formatting into final `.docx`
13. Slides

This avoids wasting time on diagrams or assets before the report actually asks for them.
