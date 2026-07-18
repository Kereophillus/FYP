# CHAPTER FIVE

# CONCLUSION AND RECOMMENDATIONS

## 5.1 Introduction

This chapter presents the conclusion and recommendations of the project. The chapter summarizes the work carried out, explains the major findings, states the contribution of the project, identifies the limitations of the completed prototype, and presents recommendations for future improvement.

The aim of this project was to develop and evaluate a resilient edge-based intelligent visual surveillance prototype that performs local person detection, anonymous tracking, rule-based behavioural-event reasoning, and persistent alert handling for infrastructure-constrained environments. The work was motivated by the limitations of passive CCTV monitoring, cloud-dependent surveillance systems, unreliable connectivity, unstable power supply, and the need for practical security systems that can operate close to where events occur.

Edge Sentinel was developed as a modular prototype that combines local computer vision, anonymous multi-object tracking, configurable behavioural indicators, local event logging, event evidence, alert queueing, HTTP recovery, and GSM/SMS capability. The project did not attempt to build a facial recognition system or a universal anomaly detection model. Instead, it focused on explainable surveillance events and resilience-oriented operation on Raspberry Pi-class edge hardware.

## 5.2 Summary of the Work Carried Out

The project began with the identification of a practical surveillance problem. Traditional CCTV systems are useful for recording and reviewing incidents, but they often remain passive because they depend on human operators to monitor footage or review recordings after an incident. This limitation becomes more serious in environments where security staff, stable power supply, reliable internet, and continuous monitoring cannot be guaranteed.

A literature review was carried out across intelligent visual surveillance, edge computing, low-light computer vision, object detection, multi-object tracking, video anomaly detection, edge AI optimization, and infrastructure-constrained security systems. The review showed that many existing works focus on model accuracy, anomaly detection, or low-light enhancement, while fewer works address the complete system problem of local inference, tracking, explainable event reasoning, alert retention, and fallback communication in constrained environments.

Based on the identified gap, Edge Sentinel was designed using an iterative prototype-based development methodology. This approach allowed the system to evolve through repeated cycles of design, implementation, testing, evaluation, and refinement. The final prototype was structured around sensing, perception, configuration, behavioural reasoning, event and evidence handling, alert communication, resilience, and local interface layers.

The implemented system used Raspberry Pi 5 as the edge platform. The IMX219 NoIR CSI camera provided visual input, the HC-SR501 PIR sensor supported motion-aware input, and the SIMCOM A7670G modem provided GSM/SMS capability. EfficientDet-Lite0 was selected as the reference detector, while ByteTrack was used for anonymous multi-object tracking. The system used rule-based behavioural indicators for restricted presence, lingering, crowd density, tailgating, and abnormal motion.

The prototype was evaluated using hardware verification, detector-tracker screening, behavioural scenario evaluation, sustained Raspberry Pi profiling, and alert queue recovery testing. The hardware verification confirmed that the camera could capture images, the PIR sensor could produce a detectable transition on GPIO17, and the modem could send SMS through the selected serial interface. The behavioural evaluation tested nine scenarios and produced eight correct outcomes, one false negative, and no false-positive scenarios. The sustained profiling test showed stable saved-video processing on the Raspberry Pi, while the alert recovery test showed that failed HTTP alerts could be queued and delivered after endpoint recovery.

## 5.3 Conclusion

The project successfully developed and evaluated Edge Sentinel as a resilience-oriented edge-based intelligent visual surveillance prototype. The system demonstrated that local person detection, anonymous tracking, configurable rule-based event reasoning, local event preservation, and persistent alert recovery can be combined on Raspberry Pi-class hardware.

The results show that the system achieved its main aim within the implemented scope. EfficientDet-Lite0 with ByteTrack provided the final reference perception pipeline. The behavioural indicators produced correct outcomes in eight out of nine evaluated scenarios, with scenario-level precision of 1.000, recall of 0.875, and F1 score of approximately 0.933. The only failed scenario was the short running-past abnormal-motion case, where the observed track did not satisfy the sustained-duration requirement. This finding identified a specific limitation rather than a general failure of the behavioural reasoning design.

The sustained Raspberry Pi profiling also supported the practical edge direction of the project. Under the evaluated saved-video workload, the system processed approximately 22.72 frames per second, made approximately 11.36 detector calls per second, recorded no throttling, no undervoltage, no dropped or failed frames, and completed with a clean shutdown. These results show that the selected reference pipeline was stable under the tested workload.

The alert queue recovery evaluation provided strong support for the resilience contribution of the project. During a simulated HTTP endpoint outage, ten alerts were generated and queued. After endpoint recovery, all ten alerts were delivered, with no lost alerts, no duplicate alerts, and final queue depth of zero. This shows that Edge Sentinel can preserve alerts locally when remote delivery fails and recover delivery when the endpoint becomes available again.

The project therefore concludes that a practical surveillance system for infrastructure-constrained environments should not depend only on model accuracy or continuous cloud connectivity. It should be designed as a complete edge system with local intelligence, explainable event rules, event preservation, and communication recovery. Edge Sentinel demonstrates this direction through an implemented and evaluated prototype.

## 5.4 Contribution of the Project

The main contribution of this project is the development of a modular and resilience-oriented edge surveillance prototype for infrastructure-constrained environments. The project contributes a system design that combines local person detection, anonymous tracking, configurable behavioural indicators, event evidence, persistent alert queueing, and fallback communication capability.

The project also contributes an explainable approach to behavioural surveillance. Instead of relying entirely on a heavy black-box anomaly detection model, the system uses interpretable rules based on zones, duration, count, transitions, and motion evidence. This makes the generated events easier to understand, tune, and evaluate. It also makes the system more suitable for constrained edge hardware.

Another contribution is the integration of alert resilience into the surveillance architecture. The system does not discard alerts when HTTP delivery fails. It stores them in a persistent queue and retries them after recovery. This is important for environments where connectivity may be unstable. The alert recovery result shows that resilience can be treated as a measurable part of surveillance system evaluation, not only as a design intention.

The project also contributes a bounded evaluation approach. Rather than claiming unsupported detector accuracy or universal anomaly detection, the system was evaluated using scenario-level event outcomes, edge-device profiling, alert recovery testing, and hardware verification. This makes the findings more honest and easier to defend.

## 5.5 Limitations of the Project

The completed prototype has some limitations. The project did not measure detector mAP, detector-level precision, detector-level recall, or detector-level F1 score using a fully annotated detection dataset. The reported precision, recall, and F1 score are scenario-level behavioural outcome metrics, not detector benchmark metrics.

The project also did not evaluate formal tracking metrics such as MOTA, IDF1, or HOTA. ByteTrack was used to provide anonymous continuity for event reasoning, and its usefulness was evaluated through the behaviour of the implemented indicators. A separate tracking benchmark was outside the completed scope.

The abnormal-motion rule also has a known limitation. The running-past scenario produced a false negative because the movement did not satisfy the 0.6-second sustained-duration requirement. This shows that the current abnormal-motion indicator is less effective for very brief fast crossings and would benefit from further refinement.

Low-light operation remains another limitation. The project used and verified the IMX219 NoIR camera, but a controlled low-light or pitch-dark evaluation campaign was not completed. The project therefore cannot claim proven night surveillance or pitch-dark performance.

Power evaluation was also limited. The system was motivated by unreliable power conditions and included PIR-assisted activation as a power-conscious direction, but watt-level power consumption, solar autonomy, and battery endurance were not formally measured. The profiling result supports device stability under the evaluated workload, but it does not represent a full power study.

The network-resilience test was also bounded. The alert recovery experiment simulated an HTTP endpoint outage and used a local receiver. It demonstrated queue retention and recovery under the tested condition, but it did not represent all possible physical network failures, cloud failures, WAN delays, or mobile network conditions. SMS delivery was functionally verified, but repeated SMS reliability and latency testing were not performed.

## 5.6 Recommendations

Future work should improve the abnormal-motion indicator so that short and fast movements can be detected more reliably without increasing false positives. This may require a separate short-burst motion rule, adaptive duration thresholds, improved track smoothing, or a more refined movement scoring method.

A more detailed detector and tracker evaluation should also be carried out. Future versions of the system should evaluate detector-level metrics using annotated frames and tracking metrics using suitable tracking ground truth. This would make it possible to compare detection and tracking performance more formally, beyond scenario-level event outcomes.

Further low-light testing is also recommended. The system should be tested under controlled lighting levels, including daylight, evening, dim indoor lighting, infrared illumination, and near-dark conditions. This would help determine how reliably the detector-tracker pipeline works under poor illumination and whether low-light enhancement is necessary.

Power measurement should be added in future work. A proper power meter or measurement setup should be used to record actual watt-level consumption during idle, active processing, alert delivery, and sensor-triggered operation. This would provide stronger evidence for deployment planning, especially where solar and battery operation are desired.

The alerting system should also be tested under more realistic network conditions. Future tests should include physical network disconnection, unstable Wi-Fi, mobile data failure, delayed endpoint recovery, and SMS delivery under different signal conditions. This would make the resilience evaluation closer to real deployment environments.

The local interface can also be improved. Future work should refine the user experience for zone drawing, indicator assignment, event review, evidence browsing, and alert queue monitoring. A more polished interface would make the system easier to configure and demonstrate in real environments.

Finally, future work may extend Edge Sentinel into a more complete deployment platform. This could include multi-device management, secure remote access, cloud synchronization, role-based access control, encrypted evidence storage, and longer field trials. These additions should build on the current edge-first architecture without removing the system’s local processing and alert-retention strengths.

## 5.7 Summary of Chapter

This chapter concluded the project by summarizing the work carried out, stating the major findings, identifying the contribution of the project, discussing limitations, and presenting recommendations for future improvement. The project achieved its aim by developing and evaluating a resilient edge-based intelligent visual surveillance prototype for infrastructure-constrained environments.

The final system demonstrated local person detection, anonymous tracking, explainable behavioural-event reasoning, local event logging, alert queue recovery, and GSM/SMS capability. The evaluation showed strong scenario-level performance, stable saved-video profiling on the Raspberry Pi, and successful alert retention and recovery under a simulated HTTP endpoint outage.

The project does not claim to solve every surveillance problem. It does not claim perfect anomaly detection, formal detector accuracy, measured power consumption, complete low-light performance, or guaranteed communication reliability. Its contribution is more focused and more practical. Edge Sentinel shows that a modular edge surveillance system can preserve useful local intelligence and alert records even when infrastructure conditions are limited.
