## 3.13 Operational State Model

The operational state model describes how Edge Sentinel behaves during normal monitoring, event processing, alert delivery, and recovery. This model is important because the system is not a simple script that only runs detection on a video file. It is a surveillance prototype that moves through different states depending on sensor input, frame availability, detection results, rule outcomes, alert delivery status, and shutdown conditions.

The system begins in an idle or ready state. In this state, the device is available for monitoring and can receive input from the camera pipeline or PIR sensor. The PIR sensor supports motion-aware activation, although the system does not claim deep sleep or complete low-power autonomy. When motion or an active monitoring condition is present, the system moves into an active processing state.

In the active state, frames are captured or read from the selected input source. During evaluation, saved-video workloads were used for repeatable testing. During deployment-oriented operation, the camera supplies the visual input. The frame then enters the processing pipeline, where scheduling determines whether detection should be performed. This scheduling is necessary because the edge device has limited processing capacity and the system must avoid unnecessary overload.

When a frame is selected for inference, the detector identifies persons in the scene. The resulting detections are passed to the tracker, which maintains temporary anonymous track continuity. The tracked outputs are then combined with zone configuration and indicator settings. The behavioural reasoning layer evaluates whether any configured rule has been satisfied.

If no rule is triggered, the system continues monitoring and processing subsequent frames. If a rule is triggered, an event is generated. The event record is stored locally and may be associated with evidence such as a snapshot or relevant metadata. The alert manager then attempts to deliver the alert through the configured HTTP endpoint. If delivery succeeds, the alert is marked as delivered. If delivery fails, the alert is stored in the persistent queue for retry.

The recovery state becomes important when failed alerts exist in the queue. The system can retry queued alerts after the endpoint becomes available again. Once the queued alerts are delivered, the queue depth returns to zero. This recovery behaviour supports the resilience objective of the project because events are not lost simply because immediate remote delivery fails.

The shutdown state represents controlled termination of the system. A clean shutdown is important because logs, queue files, and runtime state should not be corrupted. During resource profiling, the system completed the evaluated saved-video workload and shut down cleanly.

**Figure 3.7: Operational State Machine**

`[Insert operational state machine diagram here]`

### Table 3.12: Operational States of Edge Sentinel

| State                | Description                                                                       | Transition Condition                                   |
| -------------------- | --------------------------------------------------------------------------------- | ------------------------------------------------------ |
| Idle or ready        | The system is available for monitoring and waits for active processing conditions | Motion input, active monitoring, or frame availability |
| Active processing    | Frames are captured or read and prepared for analysis                             | Frame enters scheduling stage                          |
| Frame scheduling     | The system determines whether detection should be performed on the current frame  | Scheduled detector run or skipped detector interval    |
| Detection            | EfficientDet-Lite0 performs person detection                                      | Detections are produced                                |
| Tracking             | ByteTrack maintains anonymous continuity across frames                            | Tracks are updated                                     |
| Indicator evaluation | Configured behavioural rules are evaluated using detections, tracks, and zones    | Rule passes or no rule passes                          |
| Event generation     | A structured event is created when a rule condition is satisfied                  | Event is logged and passed to alert manager            |
| Alert delivery       | The system attempts HTTP delivery of the generated alert                          | Delivery succeeds or fails                             |
| Queueing             | Failed alerts are stored in the persistent local queue                            | Endpoint unavailable or delivery error occurs          |
| Recovery retry       | Queued alerts are retried after endpoint recovery                                 | Endpoint becomes available                             |
| Clean shutdown       | Runtime resources are released and the system stops safely                        | User stop command or workload completion               |

The operational state model shows how the system supports both event detection and alert resilience. The important point is that local event creation and logging are not dependent on immediate remote delivery. This separation is what allows the system to continue preserving surveillance events even during communication failure.

## 3.14 Evaluation Methodology

The evaluation methodology was designed to test the prototype from different angles. Since Edge Sentinel is a complete edge surveillance system, it would not be sufficient to evaluate only the detector or only the alerting module. The system had to be examined in terms of detector-tracker suitability, behavioural event outcomes, resource behaviour on Raspberry Pi hardware, alert retention and recovery, and functional hardware verification.

The first evaluation category was detector-tracker screening. The purpose of this stage was to identify a practical perception pipeline for the implemented behavioural indicators. The selected reference pair was EfficientDet-Lite0 with ByteTrack. The evaluation emphasis was not on formal detector benchmark metrics such as mAP, and it was not on formal tracking metrics such as MOTA, IDF1, or HOTA. Instead, the screening focused on whether the detector-tracker pair could provide useful person detection and anonymous track continuity for the implemented rule-based indicators under the project’s hardware constraints.

The second evaluation category was behavioural scenario evaluation. This was used to test whether the implemented indicators produced the expected outcomes for selected surveillance situations. The evaluated indicators included restricted presence, lingering, crowd density, tailgating, and abnormal motion. Each scenario had an expected outcome, and the system output was compared with that expectation. This provided scenario-level evidence of whether the rule-based event reasoning worked as intended.

The third evaluation category was sustained Raspberry Pi resource profiling. This evaluation examined how the selected pipeline behaved during a sustained saved-video workload. The profiling focused on processed frame rate, detector call rate, detector latency, end-to-end processing latency, temperature, dropped or failed frames, throttling, undervoltage, and shutdown behaviour. This evaluation was important because a surveillance system for constrained environments must be assessed not only by event correctness, but also by whether it can run stably on the target edge device.

The fourth evaluation category was alert queue and recovery testing. Since resilience is a central contribution of the project, the alerting system had to be evaluated under delivery failure. The test simulated a remote HTTP endpoint outage, allowed alerts to be generated and queued, then restored the endpoint and observed whether queued alerts were delivered. This evaluation measured alert retention and recovery within the tested conditions. It did not represent full physical network-disconnection testing, internet latency testing, or SMS reliability testing.

The fifth category was functional hardware verification. This was used to confirm that important hardware components could operate with the prototype. The IMX219 NoIR camera was verified for capture. The HC-SR501 PIR sensor was verified for motion transition on GPIO17. The SIMCOM A7670G modem was verified for SMS delivery through the selected serial interface. These were functional verification tests rather than repeated reliability experiments.

### Table 3.13: Evaluation Categories and Purpose

| Evaluation Category              | Purpose                                                                              | Evidence Produced                                                                                                 |
| -------------------------------- | ------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------- |
| Detector-tracker screening       | To identify a practical reference perception pipeline for edge-based event reasoning | Selection of EfficientDet-Lite0 with ByteTrack                                                                    |
| Behavioural scenario evaluation  | To test whether configured indicators produced expected event outcomes               | Scenario-level precision, recall, F1, and false-negative analysis                                                 |
| Sustained Raspberry Pi profiling | To examine processing stability and resource behaviour under saved-video workload    | FPS, detector call rate, latency, temperature, throttling, undervoltage, frame failure, and shutdown observations |
| Alert queue and recovery testing | To test alert retention during endpoint outage and delivery after recovery           | Queue retention, recovery delivery, lost-alert count, duplicate count, and final queue depth                      |
| Functional hardware verification | To confirm operation of camera, PIR sensor, and GSM/SMS modem                        | Capture verification, GPIO transition verification, and SMS delivery verification                                 |

### Table 3.14: Behavioural Scenario Evaluation Design

| Evaluation Item    | Description                                                                                      |
| ------------------ | ------------------------------------------------------------------------------------------------ |
| Evaluation target  | Rule-based behavioural indicators                                                                |
| Indicators covered | Restricted presence, lingering, crowd density, tailgating, and abnormal motion                   |
| Input type         | Labelled or expected-outcome scenario videos                                                     |
| Output checked     | Whether the expected event outcome was produced                                                  |
| Metric type        | Scenario-level event outcome metrics                                                             |
| Main metrics       | True positives, false positives, false negatives, precision, recall, and F1                      |
| Important boundary | Metrics describe scenario-event outcomes, not detector accuracy or tracker benchmark performance |

### Table 3.15: Resource Profiling Metrics

| Metric                             | Purpose                                                                             |
| ---------------------------------- | ----------------------------------------------------------------------------------- |
| Processed FPS                      | Measures the rate at which frames were processed in the evaluated workload          |
| Detector calls per second          | Measures how often detector inference was performed                                 |
| Mean detector latency              | Measures the average time spent in detector inference                               |
| Mean end-to-end processing latency | Measures average processing latency through the evaluated pipeline                  |
| Peak temperature                   | Indicates thermal behaviour during sustained workload                               |
| Throttling status                  | Indicates whether the device reduced performance due to thermal or power conditions |
| Undervoltage status                | Indicates whether power supply instability was detected                             |
| Dropped or failed frames           | Indicates whether the workload produced frame processing failures                   |
| Shutdown behaviour                 | Indicates whether the system terminated cleanly                                     |

### Table 3.16: Alert Resilience Test Design

| Test Element                | Description                                                                                                                          |
| --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| Failure condition           | Simulated HTTP endpoint outage                                                                                                       |
| Expected system behaviour   | Alerts should be created locally and retained in the persistent queue                                                                |
| Recovery condition          | HTTP endpoint becomes available again                                                                                                |
| Expected recovery behaviour | Queued alerts should be delivered after recovery                                                                                     |
| Main measurements           | Number of alerts generated, queued, recovered, lost, duplicated, and remaining in queue                                              |
| Important boundary          | The receiver was local, so recovery timing represents local queue-flushing overhead rather than internet, cloud, WAN, or SMS latency |

The evaluation methodology reflects the nature of the project. Edge Sentinel is not evaluated as a dataset-only machine learning model. It is evaluated as an integrated edge surveillance prototype. For this reason, the evaluation combines scenario outcomes, edge-device profiling, alert recovery, and hardware verification.

## 3.15 Ethical, Privacy, and Scope Considerations

Visual surveillance systems raise ethical and privacy concerns because they observe people and may produce records of human presence or behaviour. For this reason, Edge Sentinel was designed with a privacy-conscious approach. The system does not perform facial recognition, identity classification, or personal identification during routine operation. It uses anonymous track identifiers only to maintain continuity across frames for rule-based event reasoning.

The use of anonymous tracking reduces the need to attach real-world identity to a detected person. A track ID only indicates that a detection in one frame is likely associated with a detection in another nearby frame. This is enough to support rules such as lingering or movement analysis, but it does not identify who the person is. This design choice is important because the project is focused on behavioural security events rather than personal identity.

The system also uses event-based evidence rather than presenting itself as a complete continuous cloud recording platform. Evidence is associated with triggered security events and is intended to support review and verification. This helps balance security usefulness with responsible data handling. However, privacy risk is not completely removed. Any system that captures images of people must still be deployed lawfully, with appropriate permission, awareness, access control, and data handling practices.

The project also has technical scope boundaries. Edge Sentinel does not claim perfect anomaly detection. It does not claim full activity recognition across all human behaviours. It does not claim detector mAP, formal tracking accuracy, measured power consumption in watts, full solar autonomy, full physical network-failure validation, guaranteed SMS delivery, or pitch-dark surveillance performance. These boundaries are necessary because the evaluation focused on the implemented prototype and the evidence actually collected.

The ethical position of the project is therefore practical and bounded. Edge Sentinel attempts to reduce unnecessary identity processing while still allowing evidence capture for genuine security events. It also attempts to make alerts explainable through rule-based trigger reasons. These choices make the system easier to review, configure, and justify than a black-box identity-based surveillance system.

## 3.16 Summary of Chapter

This chapter presented the methodology and system design for Edge Sentinel. The project adopted an iterative prototype-based development methodology because the system involved several interacting components, including computer vision, embedded hardware, sensor input, tracking, behavioural-event logic, alert communication, local storage, and a configuration interface. The methodology allowed the system to evolve through repeated design, implementation, testing, evaluation, and refinement.

The chapter also presented the requirements analysis, overall system architecture, hardware design, software architecture, detection and tracking design, behavioural indicator design, zone configuration, event and logging structure, alerting and resilience design, operational state model, and evaluation methodology. The architecture was organized around local edge processing, anonymous tracking, explainable rule-based event reasoning, persistent alert queueing, and bounded fallback communication.

The design shows that Edge Sentinel is more than a simple object detector attached to a camera. It is a modular edge surveillance prototype designed to support local reasoning, event preservation, and alert recovery in infrastructure-constrained environments. The next chapter presents the implementation, results, and discussion of the evaluated prototype.
