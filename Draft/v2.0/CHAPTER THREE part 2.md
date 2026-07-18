## 3.5 Overall System Architecture

The overall architecture of Edge Sentinel was designed around the idea that the most important surveillance functions should remain local to the edge device. The system does not treat the camera as a passive recording tool, and it does not depend on continuous cloud connectivity before useful event reasoning can occur. Instead, it uses the edge device as the centre of the surveillance pipeline. The device receives visual input, performs local person detection, maintains anonymous tracking, evaluates configured behavioural rules, stores event records, and attempts alert delivery through available communication channels.

The architecture is organized into seven main layers. These are the sensing layer, perception layer, configuration layer, behavioural reasoning layer, event and evidence layer, communication and resilience layer, and local interface layer. This layered structure was adopted to keep the system modular. Each layer performs a clear role, while still contributing to the overall purpose of resilient surveillance in infrastructure-constrained environments.

The sensing layer provides input to the system. It consists mainly of the IMX219 NoIR CSI camera and the HC-SR501 PIR sensor. The camera supplies visual frames for detection and tracking, while the PIR sensor provides motion transition input that can support activation or deployment-aware operation. The camera is the main source of visual evidence, while the PIR sensor supports the project’s power-conscious and event-aware direction.

The perception layer processes the visual input. It uses EfficientDet-Lite0 for person detection and ByteTrack for anonymous multi-object tracking. The detector identifies persons in the frame, while the tracker links detections across frames using temporary track identifiers. These track identifiers are not linked to human identity. They only provide continuity so that the system can reason about duration, movement, and zone transitions.

The configuration layer stores site-specific information required for event reasoning. This includes monitored zones, restricted areas, rule settings, thresholds, durations, and indicator options. Surveillance environments differ from one another, so the system cannot depend on fixed hard-coded assumptions. A zone that is restricted in one location may be normal in another. A crowd threshold that is acceptable in a hallway may be unacceptable near a laboratory door. For this reason, configurability is part of the system architecture rather than an afterthought.

The behavioural reasoning layer converts detections and tracks into meaningful security events. This layer evaluates rules for restricted presence, lingering, crowd density, tailgating, and abnormal motion. The rules are designed to be explainable. An alert is not generated simply because a black-box model produces an anomaly score. It is generated because a visible condition is satisfied, such as a person entering a restricted zone, staying too long in a monitored area, exceeding a crowd threshold, following too closely through an entry path, or satisfying configured motion criteria.

The event and evidence layer records what happened when a rule is triggered. An event record may contain the event type, timestamp, zone, track information, measured values, configured thresholds, and trigger reason. Evidence such as snapshots or related event metadata can also be attached where applicable. This layer is important because surveillance alerts should not only notify the user. They should also preserve enough information for later review.

The communication and resilience layer handles alert delivery. Under normal conditions, the system attempts to deliver alerts through a configured HTTP endpoint. When delivery fails, the alert is not discarded. It is placed in a persistent local queue and retried after the endpoint becomes available again. The system also includes GSM/SMS capability through an AT-command modem. In the current evaluation, HTTP queue recovery was formally tested, while SMS delivery was functionally verified separately.

The local interface layer supports configuration, calibration, event review, and system visibility. This makes the system usable during setup and demonstration because users need to configure zones, inspect system state, review generated events, and observe alert queue behaviour. The local interface also reinforces the edge-first nature of the system because setup and review can be performed close to the device.

**Figure 3.2: Overall Architecture of Edge Sentinel**

`[Insert overall system architecture diagram here]`

### Table 3.4: Overall Architecture Layers

| Layer                              | Main Components                                                                       | Function in the System                                          |
| ---------------------------------- | ------------------------------------------------------------------------------------- | --------------------------------------------------------------- |
| Sensing layer                      | IMX219 NoIR CSI camera, HC-SR501 PIR sensor                                           | Provides visual input and motion transition input               |
| Perception layer                   | EfficientDet-Lite0, ByteTrack                                                         | Performs local person detection and anonymous tracking          |
| Configuration layer                | Zone settings, indicator settings, thresholds, durations                              | Stores site-specific rules used for event reasoning             |
| Behavioural reasoning layer        | Restricted presence, lingering, crowd density, tailgating, abnormal motion indicators | Converts detections and tracks into explainable security events |
| Event and evidence layer           | Event records, snapshots, metadata, local logs                                        | Preserves information about triggered security events           |
| Communication and resilience layer | Alert manager, persistent queue, HTTP client, SMS client                              | Delivers alerts and retains failed alerts for recovery          |
| Local interface layer              | Web dashboard, setup interface, configuration pages, event review                     | Allows users to configure and inspect the system locally        |

The layered architecture supports the central aim of the project. Edge Sentinel is not only concerned with detecting persons. It is concerned with maintaining useful surveillance behaviour when infrastructure conditions are imperfect. The architecture therefore combines perception, reasoning, evidence, communication, and recovery into one edge-based prototype.

## 3.6 Hardware Design

The hardware design of Edge Sentinel was based on the need to build a low-cost and locally deployable surveillance prototype. The selected hardware had to support camera input, local processing, motion sensing, alert communication, and field-oriented experimentation. The hardware design therefore centres on the Raspberry Pi 5 as the edge computing device, with the IMX219 NoIR CSI camera, HC-SR501 PIR sensor, and SIMCOM A7670G modem serving as supporting components.

The Raspberry Pi 5 acts as the main edge device. It hosts the software pipeline, processes camera frames, runs detection and tracking, evaluates behavioural indicators, stores local logs, manages alert queueing, and serves the local interface. The choice of Raspberry Pi-class hardware reflects the project’s focus on affordability and infrastructure-constrained deployment. Although the Raspberry Pi does not provide the same compute capacity as GPU servers or high-end edge accelerators, it is accessible, widely documented, and suitable for demonstrating practical edge surveillance.

The IMX219 NoIR CSI camera provides the visual input for the surveillance pipeline. The NoIR camera was chosen because security monitoring often takes place in low-light or night-time environments, and the absence of an infrared filter allows the camera to work with infrared illumination. In this project, camera capture was verified as part of hardware bring-up. However, the use of a NoIR camera does not mean that pitch-dark surveillance was fully validated. Effective night operation still depends on adequate illumination, camera placement, scene conditions, and later low-light evaluation.

The HC-SR501 PIR sensor provides motion transition input. The PIR sensor is useful because it can detect changes in infrared radiation associated with movement. In the Edge Sentinel design, the PIR sensor supports deployment-aware activation by helping the system react to motion rather than treating all moments equally. This aligns with the project’s power-conscious direction, although the project does not claim measured energy savings or complete low-power autonomy. The PIR transition was functionally verified on GPIO17.

The SIMCOM A7670G modem provides GSM/SMS alert capability. This component is important because internet access may not always be stable in the target deployment context. A GSM/SMS fallback path gives the system a way to deliver simple notifications even where normal network-based alerting is unavailable or unreliable. In this project, SMS delivery was functionally verified through the modem using `/dev/ttyUSB2` at 115200 baud. This verification confirms that the system can send SMS messages through the modem, but it does not represent a formal reliability or latency study of SMS delivery.

Local storage is also part of the hardware design. The device requires storage for configuration files, event records, alert queue data, snapshots, logs, and evaluation artifacts. Local storage is important for resilience because alerts and evidence should not disappear simply because a remote endpoint is unavailable at the moment an event occurs.

Optional deployment support such as infrared illumination, solar power, battery backup, and charge control remains relevant to the broader deployment vision. However, these components must be described carefully. The project was motivated by power and lighting constraints, and it explored hardware directions that support such environments, but it does not claim fully measured solar autonomy, watt-level power consumption, or complete pitch-dark surveillance performance.

**Figure 3.3: Hardware Architecture of Edge Sentinel**

`[Insert hardware architecture diagram here]`

### Table 3.5: Hardware Components and Functions

| Hardware Component                | Function                                                                                                              | Verification or Status                                                   |
| --------------------------------- | --------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| Raspberry Pi 5                    | Main edge computing platform for local inference, tracking, rule evaluation, logging, queueing, and interface hosting | Used as the prototype deployment platform                                |
| IMX219 NoIR CSI camera            | Captures visual frames for surveillance processing                                                                    | Camera capture verified                                                  |
| HC-SR501 PIR sensor               | Provides motion transition input for deployment-aware activation                                                      | Transition verified on GPIO17                                            |
| SIMCOM A7670G modem               | Provides GSM/SMS alert capability                                                                                     | SMS delivery functionally verified through `/dev/ttyUSB2` at 115200 baud |
| Local storage                     | Stores configuration, event records, logs, queue data, and evidence                                                   | Used within the prototype                                                |
| Optional IR illumination          | Supports possible low-light or night-time visibility                                                                  | Considered for deployment support, not fully evaluated                   |
| Optional solar and battery system | Supports possible field deployment under unstable power conditions                                                    | Deployment direction, not formally validated in the current evaluation   |

The hardware design reflects the balance between ambition and practicality. Edge Sentinel is intended to show that useful surveillance intelligence can be placed close to the camera using affordable components. At the same time, the project avoids claiming that every possible deployment constraint has been solved. The verified hardware components support the implemented prototype, while some deployment extensions remain future work.

## 3.7 Software Architecture

The software architecture of Edge Sentinel follows a modular structure. This was necessary because the system combines different types of functionality. It includes camera input, frame scheduling, detection, tracking, rule-based reasoning, event generation, local storage, alert delivery, queue recovery, SMS communication, and a local interface. A monolithic design would make the system difficult to test and extend. The modular design allows individual components to be improved or replaced without changing the entire system.

The capture and scheduling module receives frames from the camera or from saved-video input during evaluation. It controls how frames enter the processing pipeline and helps manage the workload on the edge device. This is important because Raspberry Pi-class hardware cannot be treated like an unlimited compute environment. The system must decide how often detection should run and how frames should be processed so that event reasoning remains responsive without overwhelming the device.

The detector module performs local person detection. In the final reference pipeline, EfficientDet-Lite0 is used as the selected detector. The detector produces bounding boxes and confidence values for persons in the frame. These detections are then passed to the tracking module.

The tracking module uses ByteTrack to maintain anonymous continuity across frames. The tracker assigns temporary track IDs to detections. These IDs allow the system to observe whether the same detected person remains in a zone, moves between zones, or appears to satisfy a motion-based rule. The system does not use these IDs for identity recognition. They are internal labels used only for event reasoning.

The zone and configuration modules provide the context needed to interpret the scene. A detection box alone does not indicate whether a person is in a sensitive area. The zone configuration defines which parts of the scene are monitored and what rules apply to them. The indicator configuration defines thresholds such as linger duration, crowd count, and movement conditions. These configurations allow the same system to be adapted to different environments.

The indicator modules evaluate the configured rules. Each indicator receives relevant detection, tracking, and zone information, then decides whether an event should be generated. Restricted presence depends on zone overlap. Lingering depends on duration. Crowd density depends on count. Tailgating depends on transition or close-following behaviour. Abnormal motion depends on movement-related criteria over time. This rule-based design keeps the system explainable and easier to debug.

The event module creates structured event records when indicators are triggered. Each event record can include the event type, timestamp, zone, track ID, measured value, configured threshold, and trigger reason. This structure is important because it allows events to be reviewed later and helps users understand why an alert was generated.

The evidence and logging modules preserve local records. Evidence may include snapshots or event-related metadata. Logs and event records support review, debugging, and evaluation. In infrastructure-constrained environments, local storage is important because remote delivery may fail temporarily. The system should not lose its record of what happened simply because a network endpoint was unavailable.

The alert manager handles outgoing alerts. When an event occurs, the alert manager attempts delivery through a configured HTTP endpoint. If the delivery fails, the alert is written into a persistent queue. The queue is later retried after recovery. This design supports resilience because local event creation can continue during a remote endpoint outage. The SMS module provides a separate GSM/SMS capability through the AT-command modem, although formal alert recovery evaluation focused on HTTP queueing.

The local API and dashboard provide user access to configuration and system state. This includes setup, zone calibration, indicator settings, event review, and alert queue visibility within the prototype scope. The local interface is important because a surveillance system must be configurable without requiring direct code changes for every deployment.

**Figure 3.4: Edge Device Internal Processing Pipeline**

`[Insert edge-device internal processing pipeline diagram here]`

### Table 3.6: Main Software Modules

| Software Module               | Main Function                                       | Role in the System                                                                                 |
| ----------------------------- | --------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| Capture and scheduling module | Receives frames and controls processing flow        | Supplies frames to the detector while managing device workload                                     |
| Detector module               | Performs local person detection                     | Produces person bounding boxes for tracking and event reasoning                                    |
| Tracker module                | Maintains anonymous track continuity                | Supports duration, movement, and transition-based indicators                                       |
| Zone manager                  | Stores and applies configured monitoring zones      | Provides spatial context for event reasoning                                                       |
| Indicator modules             | Evaluate behavioural rules                          | Generate events for restricted presence, lingering, crowd density, tailgating, and abnormal motion |
| Event module                  | Creates structured event records                    | Records event type, time, zone, track information, and trigger reason                              |
| Evidence and logging module   | Stores snapshots, metadata, logs, and event records | Preserves local evidence and supports later review                                                 |
| Alert manager                 | Delivers alerts and manages delivery failures       | Sends HTTP alerts and hands failed alerts to the persistent queue                                  |
| Persistent queue              | Stores failed alerts for later retry                | Supports alert retention and recovery after endpoint outage                                        |
| SMS module                    | Sends messages through the GSM modem                | Provides functionally verified fallback notification capability                                    |
| Local API and dashboard       | Provides setup, configuration, and review interface | Allows user interaction with the local system                                                      |
| Configuration storage         | Stores zones, thresholds, and indicator settings    | Makes the system configurable across deployment sites                                              |

The software architecture supports the modularity objective of the project. Each module has a specific responsibility, and the modules are connected through a processing pipeline. This makes the system easier to evaluate because the behaviour of the detector, tracker, indicators, alerts, and queue can be examined separately before discussing the complete prototype.

## 3.8 Detection and Tracking Design

Detection and tracking form the perception foundation of Edge Sentinel. The detector identifies persons in individual frames, while the tracker links detections across frames. This combination is necessary because the behavioural indicators depend not only on whether a person is visible, but also on how that person behaves over time.

The detection task in this project focuses on persons. This is because the implemented security indicators are person-centred. Restricted presence, lingering, crowd density, tailgating, and abnormal motion all require the system to know where people are in the scene. The system does not attempt to detect every possible object category or classify detailed human activities. This narrower focus helps keep the prototype practical for edge deployment.

EfficientDet-Lite0 was selected as the reference detector for the final evaluation pipeline. This choice reflects the project’s emphasis on lightweight inference and edge suitability. Larger detectors may offer stronger performance in some conditions, but they also increase computation cost and may reduce practical throughput on Raspberry Pi-class hardware. The final selection therefore considered not only detection ability, but also timing behaviour, pipeline stability, and usefulness for the implemented rules.

Tracking was added because detection alone was not enough for the intended behavioural indicators. Without tracking, the system could detect that a person is present in a frame, but it would struggle to know whether the same person remained in a zone for a long time or moved through a sequence of areas. ByteTrack was selected because it provides multi-object tracking based on detection outputs and can maintain temporary track continuity without requiring appearance-based identity recognition.

DeepSORT was not used as the final tracker because it introduces appearance-based re-identification features that are heavier and less aligned with the project’s privacy-conscious and resource-aware direction. Edge Sentinel does not need to recognize who a person is. It only needs to know that a detected person is likely the same track across nearby frames. ByteTrack therefore provides a better fit for the prototype because it supports anonymous continuity while avoiding identity recognition.

The detector-tracker pair used in the selected reference pipeline is EfficientDet-Lite0 with ByteTrack. This pair supports the five behavioural indicators implemented in the system. Restricted presence can be evaluated from the location of detected persons. Lingering can be evaluated from track duration in a zone. Crowd density can be evaluated from the count of persons in a monitored area. Tailgating can be evaluated from transition patterns or close-following behaviour. Abnormal motion can be evaluated from movement-related track information.

YOLOv8n with ByteTrack was later tested as a sensitivity analysis for the difficult running-past case. The purpose of that experiment was not to reopen model selection. It was used to examine whether a different detector would change the behaviour of the abnormal-motion edge case. The result showed stronger instantaneous speed evidence, but the scenario still failed the sustained-duration requirement. YOLOv8n therefore did not replace EfficientDet-Lite0 in the final reference pipeline.

The detection and tracking design is evaluated in this project using practical system-level evidence rather than formal detector and tracker benchmark metrics. The project does not claim detector mAP, detector accuracy, MOTA, IDF1, or HOTA. Instead, it evaluates whether the selected detector-tracker pair supports the implemented surveillance rules under the tested scenarios, and whether the pipeline can run with acceptable stability and timing on the Raspberry Pi.

### Table 3.7: Detector-Tracker Design Considerations

| Design Consideration        | Reason for Consideration                                                            | Final Design Response                                                                                        |
| --------------------------- | ----------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------ |
| Person-focused detection    | The implemented behavioural indicators depend mainly on human presence and movement | The detector is used primarily for person detection                                                          |
| Lightweight inference       | The system targets Raspberry Pi-class edge deployment                               | EfficientDet-Lite0 was selected as the reference detector                                                    |
| Temporal continuity         | Lingering, tailgating, and abnormal motion require continuity across frames         | ByteTrack was used for anonymous multi-object tracking                                                       |
| Privacy-conscious operation | The system should avoid identity recognition during routine operation               | Temporary track IDs are used instead of facial identity or re-identification                                 |
| Explainable event reasoning | Security events should be traceable to understandable conditions                    | Rule-based indicators use detection, tracking, zone, count, duration, and movement data                      |
| Practical edge performance  | The perception pipeline must support local processing without excessive delay       | Detector-tracker selection considered timing behaviour and system stability                                  |
| Bounded evaluation          | The project should not claim metrics that were not measured                         | Results are reported using scenario outcomes, profiling, and alert recovery evidence rather than mAP or MOTA |

The detection and tracking design supports the broader purpose of Edge Sentinel. The system does not attempt to solve all surveillance intelligence problems through one large model. Instead, it uses a lightweight detector and anonymous tracker to provide enough perception for practical, explainable, rule-based surveillance events. This makes the system more suitable for the deployment constraints addressed in the project.
