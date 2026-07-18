# CHAPTER THREE

# METHODOLOGY AND SYSTEM DESIGN

## 3.1 Introduction

This chapter presents the methodology and system design used in the development of Edge Sentinel. The chapter explains how the project was planned, designed, implemented, and evaluated. It also describes the architectural structure of the system, the hardware and software components, the detector-tracker pipeline, the behavioural-event reasoning approach, the alerting mechanism, and the evaluation strategy.

Edge Sentinel was developed as an edge-based intelligent visual surveillance prototype for infrastructure-constrained environments. The system was designed to perform local person detection, anonymous tracking, rule-based behavioural-event reasoning, local event logging, and persistent alert handling without depending on continuous cloud connectivity. The design was influenced by the need to build a system that is not only intelligent, but also practical under weak connectivity, limited compute resources, and unreliable infrastructure.

The chapter begins by describing the development methodology adopted for the project. It then presents the requirements analysis, system architecture, hardware design, software design, detection and tracking design, behavioural indicator design, event and alerting design, operational state model, and evaluation methodology. Ethical and privacy considerations are also discussed because the system deals with visual surveillance and security-event evidence.

## 3.2 Research and Development Methodology

This project adopted an iterative prototype-based development methodology. This methodology was selected because Edge Sentinel combines several interacting components, including computer vision, embedded hardware, sensor input, multi-object tracking, behavioural-event logic, alert delivery, persistent queueing, local storage, and a web-based configuration interface. These components could not be fully designed, implemented, and validated in one fixed sequence. The project therefore required repeated cycles of design, implementation, testing, evaluation, and refinement.

A purely waterfall approach would have been too rigid for this project because several important design decisions depended on implementation evidence. For example, the final detector-tracker pair could only be selected after practical evaluation of lightweight detection and tracking options. The behavioural indicators also required testing with scenario videos before their limitations could be understood. Similarly, hardware components such as the camera, PIR sensor, and GSM modem had to be verified through direct testing before they could be described confidently as part of the prototype.

The iterative prototype-based approach allowed the system to evolve from an initial concept into a more complete evaluated prototype. Early stages focused on defining the problem, reviewing literature, identifying requirements, and designing a resilience-oriented architecture. Later stages focused on implementing the perception pipeline, adding tracking, configuring behavioural indicators, verifying hardware components, evaluating scenario outcomes, profiling Raspberry Pi performance, and testing alert queue recovery. Each stage produced feedback that influenced the next version of the system.

The methodology also matches the practical nature of the project. Edge Sentinel is not only a software application. It is a cyber-physical surveillance prototype that depends on the interaction between hardware, software, AI models, sensors, communication channels, and deployment constraints. For such a system, the development process must allow testing and correction as soon as real constraints appear. The final prototype therefore reflects both the original design goals and the lessons learned during implementation.

## 3.3 Iterative Prototype-Based Development Model

The development model used for this project is shown in Figure 3.1. The model begins with problem identification and requirements analysis. This is followed by literature review and technology study, where existing works on intelligent surveillance, low-light vision, object detection, tracking, edge AI, anomaly detection, and infrastructure-constrained security systems were examined. These activities informed the initial system architecture.

After the initial architecture was designed, the system entered the implementation phase. The prototype was built in modules so that individual parts could be tested and improved independently. The camera pipeline, detector, tracker, behavioural indicators, alert manager, persistent queue, local configuration interface, PIR input, and SMS capability were developed and verified through repeated implementation cycles.

Testing and evaluation were not treated as activities that occurred only at the end of the project. Instead, they were used throughout the development process to guide refinement. For example, evaluation of detector-tracker behaviour led to the adoption of EfficientDet-Lite0 with ByteTrack as the selected reference pipeline. Scenario evaluation also revealed that the abnormal-motion running-past case produced a false negative because the track duration was too brief to satisfy the sustained-duration requirement. This result was not hidden or overfitted away. It was treated as a limitation that helped define the system’s boundary.

The alerting mechanism also benefited from iteration. Since the project is concerned with resilience, it was not enough to generate alerts only when a remote endpoint was available. The system therefore included persistent queueing so that failed HTTP alerts could be retained and retried after endpoint recovery. This design choice was evaluated through a simulated endpoint outage and recovery experiment.

Figure 3.1 should therefore be interpreted as the development lifecycle of Edge Sentinel. The feedback loop from system evaluation to refinement and correction shows that implementation results were used to improve the design and to bound the final claims of the project.

**Figure 3.1: Iterative Prototype-Based Development Model for Edge Sentinel**

`[Insert methodology model diagram here]`

The diagram should show the following sequence:

| Stage                                  | Description                                                                                                                                      | Output                                     |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------ |
| Problem Identification                 | Identification of weaknesses in passive and cloud-dependent surveillance systems under infrastructure constraints                                | Defined research problem                   |
| Requirements Analysis                  | Identification of functional, non-functional, hardware, software, and deployment requirements                                                    | System requirements                        |
| Literature Review and Technology Study | Review of intelligent surveillance, object detection, tracking, edge AI, low-light vision, anomaly detection, and Nigerian deployment challenges | Technical foundation                       |
| System Architecture Design             | Design of the modular edge surveillance architecture                                                                                             | Initial system architecture                |
| Prototype Implementation               | Implementation of capture, detection, tracking, indicators, alerts, queueing, and local interface modules                                        | Working prototype modules                  |
| Hardware and Sensor Integration        | Integration and verification of camera, PIR sensor, and GSM/SMS modem                                                                            | Verified hardware components               |
| Module Testing                         | Testing of individual software and hardware components                                                                                           | Corrected modules                          |
| System Evaluation                      | Scenario evaluation, detector-tracker screening, resource profiling, and alert recovery testing                                                  | Evaluation evidence                        |
| Refinement and Correction              | Adjustment of design and claims based on evaluation findings                                                                                     | Improved prototype and bounded conclusions |
| Final Prototype and Documentation      | Preparation of final implemented system, results, report, and presentation                                                                       | Final project deliverables                 |

The feedback loop in the model connects system evaluation and refinement back to system architecture design and prototype implementation. This represents the fact that the project evolved through repeated testing rather than a single one-way development process.

## 3.4 Requirements Analysis

The requirements analysis defines what the Edge Sentinel prototype was expected to do and the conditions under which it was expected to operate. The requirements were derived from the project problem, literature review, deployment constraints, and implementation goals. Since the system targets infrastructure-constrained environments, the requirements include not only detection and alerting functions, but also local operation, resilience, configurability, and bounded resource usage.

The functional requirements describe the services that the system must provide. These include video capture, person detection, anonymous tracking, behavioural-event reasoning, zone configuration, event logging, evidence handling, alert delivery, queue recovery, and local user interaction. The non-functional requirements describe qualities expected from the system, such as modularity, explainability, resilience, edge suitability, maintainability, and privacy-conscious operation.

### Table 3.1: Functional Requirements

| Requirement ID | Functional Requirement        | Description                                                                                                                                   | Implementation Status                                               |
| -------------- | ----------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------- |
| FR1            | Video capture                 | The system shall capture visual input from a camera connected to the edge device.                                                             | Implemented and camera capture verified                             |
| FR2            | Local person detection        | The system shall detect persons locally without requiring cloud inference.                                                                    | Implemented using EfficientDet-Lite0 as selected reference detector |
| FR3            | Anonymous tracking            | The system shall maintain temporary track continuity for detected persons without identifying individuals.                                    | Implemented using ByteTrack                                         |
| FR4            | Zone configuration            | The system shall allow monitored areas or zones to be configured for rule-based event evaluation.                                             | Implemented through local configuration workflow                    |
| FR5            | Restricted presence detection | The system shall generate an event when a person is detected in a restricted or monitored zone.                                               | Implemented and evaluated                                           |
| FR6            | Lingering detection           | The system shall generate an event when a tracked person remains in a zone beyond a configured duration.                                      | Implemented and evaluated                                           |
| FR7            | Crowd-density monitoring      | The system shall generate an event when the number of persons in a zone exceeds a configured threshold.                                       | Implemented and evaluated                                           |
| FR8            | Tailgating detection          | The system shall generate an event when movement patterns indicate unauthorized close-following or entry behaviour based on configured rules. | Implemented and evaluated within scenario scope                     |
| FR9            | Abnormal-motion detection     | The system shall generate an event when movement behaviour satisfies configured abnormal-motion criteria.                                     | Implemented and evaluated with one known false negative             |
| FR10           | Event logging                 | The system shall store generated event records locally.                                                                                       | Implemented                                                         |
| FR11           | Evidence handling             | The system shall support event evidence such as snapshots or related event metadata where applicable.                                         | Implemented within prototype scope                                  |
| FR12           | HTTP alert delivery           | The system shall attempt to deliver event alerts to a configured HTTP endpoint.                                                               | Implemented and evaluated                                           |
| FR13           | Persistent alert queueing     | The system shall retain failed alerts and retry delivery after endpoint recovery.                                                             | Implemented and evaluated                                           |
| FR14           | GSM/SMS capability            | The system shall support SMS alert notification through an AT-command modem.                                                                  | Functionally verified                                               |
| FR15           | PIR-assisted activation       | The system shall support motion-sensor input as part of deployment-aware activation.                                                          | Functionally verified                                               |
| FR16           | Local configuration interface | The system shall provide a local interface for setup, configuration, and review of system state.                                              | Implemented within prototype scope                                  |

### Table 3.2: Non-Functional Requirements

| Requirement ID | Non-Functional Requirement  | Description                                                                                               | Design Response                                                                                    |
| -------------- | --------------------------- | --------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| NFR1           | Edge operation              | The system should perform core detection and event reasoning locally on Raspberry Pi-class hardware.      | Local inference and rule processing are performed on the edge device                               |
| NFR2           | Resilience                  | The system should preserve event records and alerts when immediate remote delivery fails.                 | Persistent alert queueing and retry mechanism are used                                             |
| NFR3           | Modularity                  | The system should allow major components to be changed or improved without redesigning the entire system. | Detector, tracker, indicators, alerting, and configuration logic are separated into modules        |
| NFR4           | Explainability              | Security events should include understandable trigger reasons.                                            | Rule-based indicators are used instead of opaque anomaly scores                                    |
| NFR5           | Privacy-conscious operation | The system should avoid identity recognition during routine operation.                                    | Anonymous track IDs are used for continuity instead of facial recognition                          |
| NFR6           | Configurability             | The system should support site-specific zones, thresholds, and indicator settings.                        | Local zone and indicator configuration are supported                                               |
| NFR7           | Resource awareness          | The system should be suitable for constrained hardware and should avoid unnecessary heavy computation.    | Lightweight detection, ByteTrack tracking, saved-video profiling, and modular scheduling are used  |
| NFR8           | Maintainability             | The system should be understandable and extendable for future work.                                       | Modular architecture and documented components support maintenance                                 |
| NFR9           | Deployment relevance        | The system should address weak internet, limited technical support, and unreliable infrastructure.        | Local processing, event logging, queue recovery, and SMS capability support constrained deployment |
| NFR10          | Bounded security claims     | The system should not claim capabilities that were not evaluated.                                         | Evaluation results and limitations are reported with claim boundaries                              |

### Table 3.3: Hardware and Software Requirements

| Category | Requirement                            | Purpose                                                              | Status                                                |
| -------- | -------------------------------------- | -------------------------------------------------------------------- | ----------------------------------------------------- |
| Hardware | Raspberry Pi 5                         | Main edge computing platform for local inference and event reasoning | Used as deployment platform                           |
| Hardware | IMX219 NoIR CSI camera                 | Visual input for surveillance monitoring                             | Capture verified                                      |
| Hardware | HC-SR501 PIR sensor                    | Motion transition input for deployment-aware activation              | GPIO17 transition verified                            |
| Hardware | SIMCOM A7670G modem                    | GSM/SMS alert capability                                             | SMS delivery functionally verified                    |
| Hardware | Storage medium                         | Local logs, configuration, queue, and evidence storage               | Used within prototype                                 |
| Hardware | Optional IR illumination               | Support for improved low-light visibility                            | Considered as deployment support, not fully validated |
| Software | Python runtime and project environment | Main implementation environment                                      | Used for system modules                               |
| Software | EfficientDet-Lite0                     | Selected reference detector for person detection                     | Used in final reference pipeline                      |
| Software | ByteTrack                              | Anonymous multi-object tracking                                      | Used in final reference pipeline                      |
| Software | Local web/API interface                | Configuration, calibration, and review of system state               | Implemented within prototype scope                    |
| Software | Alert manager and queue                | HTTP alert delivery, retry, and recovery                             | Implemented and evaluated                             |
| Software | AT-command SMS client                  | Communication with GSM modem for SMS capability                      | Functionally verified                                 |
| Software | Configuration files                    | Zone and indicator settings                                          | Used for local configurability                        |
| Software | Evaluation scripts and logs            | Scenario evaluation, profiling, and alert recovery evidence          | Used for project evaluation                           |

The requirements show that Edge Sentinel was designed as more than a detector running on a camera feed. The system combines local perception, anonymous tracking, configurable reasoning, persistent alert handling, and deployment-aware hardware integration. These requirements provide the foundation for the system architecture presented in the next section.
