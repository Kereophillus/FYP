## 4.8 Sustained Raspberry Pi Resource Profiling

The sustained resource profiling test was carried out to examine how the selected Edge Sentinel pipeline behaved on the Raspberry Pi during a controlled saved-video workload. This test was important because the project targets constrained edge deployment. A system may produce correct event outcomes in short tests, but still be unsuitable if it overheats, drops frames, suffers unstable processing, or cannot sustain useful throughput on the edge device.

The profiling test used the selected reference pipeline, EfficientDet-Lite0 with ByteTrack. The model was loaded once and warm-up behaviour was excluded from the measured period. The profiling run then measured sustained processing behaviour over the selected workload duration. The main metrics included processed frames per second, detector calls per second, detector latency, end-to-end processing latency, device temperature, throttling status, undervoltage status, dropped or failed frames, and shutdown behaviour.

The profiling result showed approximately 22.72 processed frames per second under the evaluated saved-video workload. The detector was called approximately 11.36 times per second. The mean detector latency was approximately 47.06 ms, while the mean end-to-end processing latency was approximately 43.00 ms as reported by the profiling output. The peak temperature recorded during the run was approximately 49.05°C.

No throttling was recorded during the profiling run. No undervoltage was recorded. No dropped or failed frames were reported. The workload completed with a clean shutdown. These results suggest that the selected reference pipeline was stable under the evaluated saved-video profiling condition on the Raspberry Pi.

The results must be interpreted carefully. The profiling workload was based on saved-video processing, not full live-camera throughput. Therefore, the reported processed FPS should not be presented as guaranteed live-camera FPS in every deployment condition. The test also did not measure electrical power consumption in watts. It can support claims about processing behaviour, latency, temperature, throttling, undervoltage status, and frame processing stability under the tested workload, but it cannot support claims about measured energy consumption or solar endurance.

### Table 4.8: Sustained Raspberry Pi Resource Profiling Results

| Metric                             | Result                            | Interpretation                                                   |
| ---------------------------------- | --------------------------------- | ---------------------------------------------------------------- |
| Reference pipeline                 | EfficientDet-Lite0 with ByteTrack | Final evaluated detector-tracker pair                            |
| Workload type                      | Saved-video workload              | Repeatable profiling condition, not live-camera FPS proof        |
| Processed FPS                      | Approximately 22.72 FPS           | Indicates sustained processing rate under the evaluated workload |
| Detector calls per second          | Approximately 11.36 calls/s       | Shows detector scheduling frequency during profiling             |
| Mean detector latency              | Approximately 47.06 ms            | Indicates average detector inference time                        |
| Mean end-to-end processing latency | Approximately 43.00 ms            | Indicates average reported pipeline processing latency           |
| Peak temperature                   | Approximately 49.05°C             | Device remained within a safe thermal range during the test      |
| Throttling                         | None recorded                     | No thermal or performance throttling observed                    |
| Undervoltage                       | None recorded                     | No undervoltage condition observed                               |
| Dropped or failed frames           | None recorded                     | No frame failure reported during the run                         |
| Shutdown behaviour                 | Clean shutdown                    | Runtime ended without reported shutdown error                    |

The sustained profiling result supports the claim that the selected pipeline can run stably on the Raspberry Pi under the evaluated workload. This is important for the project because Edge Sentinel is not intended to be only a desktop demonstration. The system is designed for edge deployment, so the resource behaviour of the prototype is part of the evidence for its suitability.

At the same time, the result does not prove every deployment condition. Live camera operation, different scene complexity, stronger low-light processing, larger models, poor power supply, or simultaneous extra services may change runtime behaviour. The result should therefore be used as evidence of stable saved-video profiling on the Raspberry Pi, not as a universal performance guarantee.

## 4.9 Alert Queue and Recovery Evaluation

The alert queue and recovery evaluation tested one of the central resilience features of Edge Sentinel. Since the project targets infrastructure-constrained environments, it is not enough for the system to generate alerts only when a remote endpoint is immediately available. A practical surveillance system should preserve alerts when delivery fails and deliver them later when communication recovers.

The evaluation simulated a remote HTTP endpoint outage. During the outage, the system continued to generate alerts locally. Since the endpoint was unavailable, the alerts could not be delivered immediately. Instead of losing them, the alert manager stored them in the persistent local queue. After the endpoint was restored, the system retried the queued alerts and delivered them successfully.

A total of ten alerts were generated during the simulated outage. All ten were retained in the queue. After recovery, all ten queued alerts were delivered. No alerts were lost. No duplicate alerts were recorded. The final queue depth was zero. This means that, under the tested condition, the system achieved complete alert retention and complete recovery delivery.

This result is important because it demonstrates that Edge Sentinel can preserve alert records during a remote delivery failure. The system does not stop local event creation simply because the HTTP endpoint is unavailable. It continues to record events and queue failed alerts. This supports the project’s resilience claim, especially for environments where remote connectivity may be intermittent.

The evaluation also has important boundaries. The receiver used in the test was local, so the recovery timing represents local queue-flushing overhead rather than internet, cloud, WAN, or SMS latency. The test simulated an HTTP endpoint outage. It was not a full physical network-disconnection test. SMS capability was functionally verified separately, but SMS was not the focus of this queue recovery experiment. Therefore, the result supports HTTP alert queue retention and recovery under the tested simulated outage, not guaranteed alert delivery under every communication failure condition.

### Table 4.9: Alert Queue Recovery Results

| Evaluation Item                 | Result                         | Interpretation                                                          |
| ------------------------------- | ------------------------------ | ----------------------------------------------------------------------- |
| Failure condition               | Simulated HTTP endpoint outage | Remote HTTP delivery was made unavailable                               |
| Alerts generated during outage  | 10                             | Local alert creation continued                                          |
| Alerts queued                   | 10                             | Failed alerts were retained locally                                     |
| Queue retention rate            | 100 percent                    | No generated alert was discarded during outage                          |
| Alerts delivered after recovery | 10                             | All queued alerts were delivered after endpoint recovery                |
| Recovery delivery rate          | 100 percent                    | Queue recovery succeeded under the tested condition                     |
| Lost alerts                     | 0                              | No alert loss observed                                                  |
| Duplicate alerts                | 0                              | No duplicate delivery observed                                          |
| Final queue depth               | 0                              | Queue was emptied after successful recovery                             |
| Important boundary              | Receiver was local             | Recovery timing does not represent internet, cloud, WAN, or SMS latency |

The alert recovery result is one of the strongest pieces of evidence for the system’s resilience-oriented design. It shows that the system can separate local event creation from immediate remote delivery. This separation matters because infrastructure-constrained environments may not always provide stable connectivity. By retaining failed alerts and retrying them after recovery, Edge Sentinel avoids one of the major weaknesses of simple alert systems, which is the permanent loss of alerts during delivery failure.

## 4.10 Local Interface and Configuration Demonstration

The local interface was implemented to support setup, configuration, and review of the Edge Sentinel prototype. This part of the system is important because a surveillance device should not require code-level changes for every deployment environment. Users need a practical way to configure monitoring zones, apply indicator settings, inspect system state, and review generated event evidence.

The interface supports local setup and calibration activities. A camera frame can be used as the basis for zone configuration, allowing monitored regions to be defined according to the actual camera view. This is necessary because every deployment scene is different. A restricted area, doorway, corridor, or monitored boundary must be defined based on the physical environment being observed.

The interface also supports indicator configuration. Behavioural rules depend on settings such as zone assignment, duration threshold, count threshold, and movement conditions. Providing these settings through a local interface makes the system more adaptable. It allows the same prototype to be configured for different monitoring needs without changing the underlying detection and tracking pipeline.

Event and evidence review were also included within the prototype scope. This allows generated events and related evidence to be inspected locally. This feature supports both usability and evaluation. During evaluation, it helps confirm whether events were generated and whether evidence was stored as expected. During deployment-oriented use, it helps users review what happened after an alert or incident.

The interface also provides visibility into alert queue behaviour. This is useful because the resilience layer depends on whether alerts are delivered, queued, retried, or recovered. A user-facing view of queue status makes the alerting system easier to understand and verify. Without such visibility, it would be harder to know whether a failed alert was lost or simply waiting for recovery.

The local interface was treated as an implementation demonstration rather than a formal usability study. The project did not measure user experience, task completion time, or interface satisfaction. However, the existence of the interface strengthens the prototype because it shows that Edge Sentinel is not only a backend pipeline. It includes practical setup and review mechanisms needed for a configurable surveillance system.

### Table 4.10: Local Interface and Configuration Features

| Feature                   | Purpose                                                     | Evaluation Interpretation                      |
| ------------------------- | ----------------------------------------------------------- | ---------------------------------------------- |
| Local setup interface     | Provides access to device setup and configuration functions | Implemented as part of prototype demonstration |
| Calibration frame support | Allows the camera view to be used for zone configuration    | Supports scene-specific setup                  |
| Zone configuration        | Defines monitored or restricted areas in the camera frame   | Supports rule-based event reasoning            |
| Indicator settings        | Allows rules and thresholds to be configured                | Supports adaptable surveillance behaviour      |
| Event review              | Allows generated events to be inspected                     | Supports verification and system review        |
| Evidence access           | Allows event-related evidence to be viewed where available  | Supports incident review                       |
| Alert queue visibility    | Shows queue state and delivery status information           | Supports resilience monitoring                 |
| Local operation           | Allows setup and review near the edge device                | Supports edge-first deployment direction       |

The local interface contributes to the practical value of Edge Sentinel. A surveillance system that is technically functional but difficult to configure may not be useful in real environments. By including local setup, zone configuration, indicator settings, event review, and queue visibility, the prototype moves closer to a deployable edge surveillance tool.

The interface also supports the modular nature of the system. Zones, thresholds, indicators, events, and alerts are not hidden inside one fixed script. They are exposed through configuration and review mechanisms. This makes the prototype easier to demonstrate, evaluate, and extend in future work.
