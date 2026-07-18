# CHAPTER FOUR

# IMPLEMENTATION, RESULTS AND DISCUSSION

## 4.1 Introduction

This chapter presents the implementation, results, and discussion of the Edge Sentinel prototype. The purpose of the chapter is to show how the system described in Chapter Three was implemented and how the completed prototype performed during evaluation. While Chapter Three focused on design, architecture, and methodology, this chapter focuses on what was actually built, tested, observed, and learned.

The implementation was carried out as a modular edge surveillance system running on Raspberry Pi-class hardware. The system combines camera input, local person detection, anonymous tracking, configurable behavioural indicators, event logging, evidence handling, persistent alert queueing, HTTP alert delivery, and GSM/SMS capability. The implemented behavioural indicators include restricted presence, lingering, crowd density, tailgating, and abnormal motion.

The evaluation was designed to reflect the system-level nature of the project. Edge Sentinel was not evaluated only as an object detection model, because the main contribution of the project is not limited to detection accuracy. Instead, the evaluation examined whether the complete prototype could support local event reasoning, stable edge processing, and alert recovery under a communication failure condition. The results therefore cover hardware verification, detector-tracker selection, behavioural scenario outcomes, Raspberry Pi resource profiling, and alert queue recovery.

The chapter also discusses the limitations discovered during evaluation. One scenario, `abnormal_motion__running_past_frame`, produced a false negative because the detected motion did not satisfy the sustained-duration requirement. This result is important because it shows a real boundary of the current rule logic. The chapter also explains why YOLOv8n was tested as a sensitivity experiment for this case, why it did not replace EfficientDet-Lite0, and why the project avoids unsupported claims such as detector mAP, formal tracking accuracy, measured power consumption, or guaranteed SMS reliability.

## 4.2 Implementation Overview and Experimental Environment

The Edge Sentinel prototype was implemented as an edge-based surveillance system with local processing as the core design principle. The Raspberry Pi 5 served as the main edge device. It hosted the detection and tracking pipeline, behavioural indicator logic, alert manager, persistent queue, configuration files, local logs, and the local setup interface. The system was designed so that event detection and local logging could continue even when immediate remote alert delivery was unavailable.

The visual input component was based on the IMX219 NoIR CSI camera. The camera was used as the main capture device during hardware verification and deployment-oriented testing. For repeatable evaluation and profiling, saved-video workloads were also used. This distinction is important because saved-video profiling provides controlled and repeatable evidence of processing behaviour, but it should not be described as live-camera FPS performance.

The selected reference perception pipeline was EfficientDet-Lite0 with ByteTrack. EfficientDet-Lite0 provided local person detection, while ByteTrack maintained anonymous track continuity across frames. The tracker did not identify people. It only assigned temporary track identifiers so that the system could reason about duration, movement, zone overlap, and transition patterns.

The behavioural reasoning layer used configured rules to generate security events. Each rule used detection, tracking, zone, duration, count, transition, or movement data depending on the event type. This allowed the system to generate explainable events rather than relying on a black-box anomaly score. The event and alerting layers then recorded the event locally and attempted remote delivery through the configured alert path.

The alerting implementation included HTTP delivery and persistent queueing. When HTTP delivery failed, the alert was stored locally and retried after endpoint recovery. GSM/SMS capability was also implemented through a SIMCOM A7670G modem and functionally verified separately. The HTTP queue recovery evaluation and the SMS verification should be interpreted separately because they tested different parts of the communication design.

### Table 4.1: Experimental Environment

| Component                 | Description                                                                | Role in Evaluation                                                                          |
| ------------------------- | -------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------- |
| Edge device               | Raspberry Pi 5                                                             | Main platform for local inference, tracking, event reasoning, alert handling, and profiling |
| Camera                    | IMX219 NoIR CSI camera                                                     | Visual input device, capture function verified                                              |
| Motion sensor             | HC-SR501 PIR sensor                                                        | Motion transition input, verified on GPIO17                                                 |
| GSM modem                 | SIMCOM A7670G                                                              | SMS alert capability, functionally verified through serial communication                    |
| Detector                  | EfficientDet-Lite0                                                         | Selected reference detector for person detection                                            |
| Tracker                   | ByteTrack                                                                  | Selected anonymous multi-object tracker                                                     |
| Behavioural indicators    | Restricted presence, lingering, crowd density, tailgating, abnormal motion | Rule-based event generation                                                                 |
| Alert mechanism           | HTTP delivery with persistent queue, GSM/SMS capability                    | Alert delivery, retention, recovery, and fallback capability                                |
| Profiling workload        | Saved-video workload                                                       | Repeatable measurement of processing performance and device stability                       |
| Alert resilience workload | Simulated HTTP endpoint outage and recovery                                | Evaluation of queue retention and delayed delivery                                          |

The implementation environment reflects the central direction of the project. Edge Sentinel was developed not as a cloud-first surveillance application, but as a local edge prototype that can detect, reason, log, and preserve alerts even when external communication is interrupted.

## 4.3 Hardware Component Verification

The hardware verification stage confirmed that the main physical components required for the prototype were able to operate with the system. This stage did not attempt to perform long-term reliability testing. Its purpose was to confirm that the camera, PIR sensor, and GSM modem could function as part of the Edge Sentinel hardware design.

The IMX219 NoIR CSI camera was verified for image capture. This confirmed that the camera could supply visual input to the system. The use of a NoIR camera is relevant because the project is motivated partly by surveillance needs in low-light environments. However, camera capture verification does not prove complete night surveillance performance. Low-light and pitch-dark operation still depend on illumination, scene conditions, and further controlled testing.

The HC-SR501 PIR sensor was verified on GPIO17. The verification confirmed that the sensor could produce a motion transition detectable by the Raspberry Pi. This supports the deployment-aware activation direction of the system. The PIR verification should be understood as a functional test, not as a repeated reliability study or a formal latency experiment.

The SIMCOM A7670G modem was verified for SMS delivery through `/dev/ttyUSB2` at 115200 baud. This confirmed that the system could communicate with the modem and send an SMS message through the selected serial interface. This result supports the fallback communication direction of the project, especially for environments where internet-based alerting may not always be available. However, the verification does not prove guaranteed SMS delivery, delivery latency, or reliability under all mobile network conditions.

### Table 4.2: Hardware Verification Summary

| Hardware Component     | Verification Performed                                  | Result                     | Interpretation                                                       |
| ---------------------- | ------------------------------------------------------- | -------------------------- | -------------------------------------------------------------------- |
| IMX219 NoIR CSI camera | Camera capture test                                     | Capture verified           | The system can receive visual input from the camera                  |
| HC-SR501 PIR sensor    | GPIO transition test on GPIO17                          | Motion transition verified | The system can detect PIR state changes for motion-aware operation   |
| SIMCOM A7670G modem    | SMS delivery test through `/dev/ttyUSB2` at 115200 baud | SMS delivery verified      | The system can send SMS through the modem under the tested condition |

The hardware verification results show that the prototype was supported by working physical components. The camera provided the visual foundation, the PIR sensor supported motion-aware input, and the GSM modem supported SMS capability. These results do not remove the need for future reliability testing, but they confirm that the selected hardware components were usable within the implemented prototype.

## 4.4 Detector-Tracker Screening and Final Selection

The perception pipeline is a critical part of Edge Sentinel because every behavioural indicator depends on reliable person detection and useful temporal continuity. The system must first identify persons in the scene, then maintain enough continuity across frames to support duration, count, transition, and motion-based rules. For this reason, detector-tracker selection was treated as a system design decision rather than a purely model-comparison exercise.

EfficientDet-Lite0 with ByteTrack was selected as the reference detector-tracker pair for the final evaluation. EfficientDet-Lite0 was selected because it fits the lightweight edge deployment direction of the project. It provides a practical balance between detection capability and computational cost. ByteTrack was selected because it provides anonymous multi-object tracking from detection outputs, allowing the system to reason about movement and persistence without facial recognition or identity classification.

This selection aligns with the project’s privacy-conscious and resource-aware design. The system does not need to know who a person is. It only needs to know that a detected person is likely the same temporary track across nearby frames. This makes ByteTrack suitable for indicators such as lingering, tailgating, and abnormal motion. The track identifiers are used only within the system for event reasoning and do not represent personal identity.

The screening process did not produce formal detector mAP or formal tracker metrics such as MOTA, IDF1, or HOTA. This is an important boundary. The project evaluated the perception pipeline mainly by its usefulness to the implemented surveillance rules, its timing behaviour, and its stability under the evaluated workload. The final results should therefore be discussed as system-level and scenario-level evidence, not as a benchmark claim that one detector or tracker is universally superior.

YOLOv8n with ByteTrack was also tested, but only as a sensitivity experiment for the difficult running-past case. That experiment showed stronger instantaneous speed evidence, but it still failed the 0.6-second sustained-duration requirement used by the abnormal-motion rule. It also operated with substantially higher detector latency and lower pipeline throughput compared with the selected reference pipeline. Therefore, YOLOv8n did not replace EfficientDet-Lite0, and no additional YOLOv5n follow-up was required for the final evaluation path.

### Table 4.3: Detector-Tracker Selection Summary

| Item                        | Observation                                                                     | Final Interpretation                                                      |
| --------------------------- | ------------------------------------------------------------------------------- | ------------------------------------------------------------------------- |
| Selected reference detector | EfficientDet-Lite0                                                              | Used as the final detector for the evaluated reference pipeline           |
| Selected reference tracker  | ByteTrack                                                                       | Used for anonymous multi-object tracking and rule continuity              |
| Main reason for tracking    | Behavioural rules require continuity across frames                              | Tracking supports lingering, tailgating, and abnormal-motion reasoning    |
| Identity handling           | Temporary track IDs only                                                        | The system does not perform facial recognition or identity classification |
| YOLOv8n role                | Sensitivity analysis for the running-past case                                  | Did not replace EfficientDet-Lite0                                        |
| YOLOv8n outcome             | Stronger instantaneous speed evidence, but still failed sustained-duration rule | The running-past limitation remained unresolved by switching detector     |
| Unsupported metrics         | Detector mAP, detector accuracy, MOTA, IDF1, HOTA                               | These metrics were not claimed because they were not formally evaluated   |

The detector-tracker screening supports the final architecture of Edge Sentinel. EfficientDet-Lite0 and ByteTrack provided the perception foundation for the evaluated prototype, while the YOLOv8n sensitivity test helped clarify that the running-past false negative was not simply solved by changing detectors. This result strengthened the final claim boundary of the project because it showed where the current rule design works and where it still needs improvement.
