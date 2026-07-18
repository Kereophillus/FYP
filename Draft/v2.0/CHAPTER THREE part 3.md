## 3.9 Behavioural Indicator Design

The behavioural indicator layer is the part of Edge Sentinel that converts detection and tracking outputs into security events. The system does not attempt to classify every possible human activity. Instead, it focuses on a set of practical surveillance indicators that can be expressed through visible and explainable conditions. This approach was selected because the project targets edge deployment, where computation, memory, power, and reliability must be considered alongside detection performance.

The behavioural indicators use information from the detector, tracker, zone configuration, and indicator settings. The detector provides person locations. The tracker provides temporary continuity across frames. The zone configuration defines the parts of the scene that are important for monitoring. The indicator settings define thresholds such as duration, count, transition conditions, or movement criteria. By combining these sources, the system can reason about selected behaviours without using identity recognition or a heavy general-purpose activity recognition model.

The first indicator is restricted presence or restricted access. This indicator checks whether a detected and tracked person is present inside a configured restricted zone. A restricted zone may represent a laboratory entrance, a staff-only area, a storage section, or any monitored region defined during setup. When a person overlaps that zone and the rule is active, the system can generate a restricted-presence event. The purpose of this rule is to support awareness of unauthorized or sensitive-area presence.

The second indicator is lingering. Lingering occurs when a tracked person remains within a monitored area beyond a configured duration. This indicator requires tracking because it is not enough to know that a person appeared in one frame. The system must estimate whether the same tracked person has remained in the area across time. Lingering is useful for detecting suspicious waiting, loitering near entrances, or unusual presence around sensitive areas.

The third indicator is crowd density. Crowd density is based on the number of persons detected within a configured zone or scene area. When the count exceeds a defined threshold, the system can generate a crowd-density event. This indicator is useful in areas where abnormal crowding may suggest congestion, unauthorized gathering, or safety risk. Since the prototype uses person detections and zone context, the crowd-density rule remains explainable and configurable.

The fourth indicator is tailgating. Tailgating refers to a situation where one person closely follows another through an entry path or controlled region. In the system design, tailgating depends on tracking and zone transitions. The system must observe movement through configured areas and evaluate whether the pattern satisfies the rule condition. This indicator is more complex than simple restricted presence because it depends on temporal relationships between tracked persons and configured zones.

The fifth indicator is abnormal motion. Abnormal motion is used to identify movement behaviour that satisfies configured motion criteria, such as unusually fast movement across a scene or monitored area. This indicator depends on track displacement and timing information. Unlike a full deep-learning-based anomaly detection model, the abnormal-motion rule is based on measurable movement conditions. This makes the rule easier to interpret, although it also means that the system may miss short or poorly sustained motion events. The known limitation of the running-past case is discussed later in Chapter Four.

The use of rule-based behavioural indicators makes Edge Sentinel easier to configure and defend. Each alert can be connected to a clear condition. A user can understand that an event occurred because a person entered a restricted zone, stayed too long, exceeded a crowd threshold, moved through an entry pattern, or satisfied a motion rule. This is important in security systems because alerts should not only be generated. They should also be understandable.

### Table 3.8: Behavioural Indicators and Rule Logic

| Indicator                  | Main Input Used                                            | Rule Condition                                                                    | Output Event                                   | Evaluation Status                                                |
| -------------------------- | ---------------------------------------------------------- | --------------------------------------------------------------------------------- | ---------------------------------------------- | ---------------------------------------------------------------- |
| Restricted presence/access | Detection box, track ID, configured zone                   | A tracked person overlaps a restricted or monitored zone while the rule is active | Restricted-presence or restricted-access event | Implemented and evaluated                                        |
| Lingering                  | Track ID, zone overlap, duration setting                   | A tracked person remains in a monitored zone longer than the configured duration  | Lingering event                                | Implemented and evaluated                                        |
| Crowd density              | Person detections, zone overlap, count threshold           | The number of detected persons in a monitored area exceeds the configured maximum | Crowd-density event                            | Implemented and evaluated                                        |
| Tailgating                 | Track IDs, zone transitions, timing or proximity condition | Movement through configured areas satisfies the tailgating rule condition         | Tailgating event                               | Implemented and evaluated within scenario scope                  |
| Abnormal motion            | Track displacement, timing, movement threshold             | A tracked person satisfies configured abnormal-motion criteria                    | Abnormal-motion event                          | Implemented and evaluated with a known short-crossing limitation |

The behavioural indicator design supports the central balance in the project. The system is intelligent enough to reason about selected security conditions, but simple enough to run on constrained hardware and explain its decisions. The indicators are therefore a practical middle ground between passive CCTV and heavy black-box video anomaly detection.

## 3.10 Zone Configuration and Local Setup Interface

Zone configuration is essential to the operation of Edge Sentinel because surveillance meaning depends on the environment being monitored. A person standing in one part of a scene may be normal, while the same person standing in another part may be suspicious or unauthorized. For this reason, the system must allow monitored areas and rule settings to be configured according to the deployment site.

A zone is a defined region in the camera view. It may represent a restricted area, an entrance path, a doorway region, a corridor section, or any part of the scene that the user wants to monitor. Behavioural indicators use these zones to determine whether an event should be triggered. Without zones, the system would only know that people are present in the frame. With zones, the system can reason about where the person is, how long the person has remained there, and whether movement through the scene has security meaning.

The local setup interface supports this configuration process. It provides a way to capture or view a camera frame, define monitoring zones, assign indicator settings, and inspect relevant system state. This is important because the system is intended to be configurable rather than hard-coded for one environment. A surveillance prototype that requires code changes for every new scene would not be practical for deployment.

The zone configuration process also supports explainability. When an event is generated, the user can relate the event to a visible zone and a configured rule. For example, a restricted-presence event can be understood by checking the restricted zone. A lingering event can be understood by reviewing the configured duration threshold. A crowd-density event can be understood by comparing the observed count with the configured maximum. This makes the system easier to tune and verify.

The local interface also supports event review and alert queue visibility within the prototype scope. These features are useful because resilience is not only about detecting events. It is also about knowing whether events were recorded, whether alerts were delivered, and whether any alerts remain queued after a delivery failure. A local interface gives the user a way to inspect these states without relying on a remote cloud dashboard.

**Figure 3.5: Zone-Based Configuration Concept**

`[Insert zone-based configuration concept diagram here]`

### Table 3.9: Zone Configuration Elements

| Configuration Element | Purpose                                                           | Example Use                                                   |
| --------------------- | ----------------------------------------------------------------- | ------------------------------------------------------------- |
| Zone name             | Identifies the monitored area                                     | Entrance, restricted area, storage corner, laboratory door    |
| Zone coordinates      | Defines the region of interest in the camera frame                | Rectangle or polygon drawn over a calibration frame           |
| Active indicators     | Specifies which rules apply to the zone                           | Restricted presence, lingering, crowd density                 |
| Duration threshold    | Defines how long a person may remain before an event is triggered | Lingering after a configured number of seconds                |
| Count threshold       | Defines the maximum allowed number of persons in a zone           | Crowd-density event when count exceeds the limit              |
| Transition condition  | Defines movement relationships across zones                       | Tailgating or entry-pattern evaluation                        |
| Motion threshold      | Defines movement criteria for abnormal-motion detection           | Fast or unusual movement based on track displacement and time |

The zone configuration design makes Edge Sentinel adaptable. The same software can support different monitoring scenes by changing zones and thresholds. This aligns with the project’s modularity objective and supports deployment in varied infrastructure-constrained environments.

## 3.11 Event, Evidence, and Logging Design

The event, evidence, and logging layer is responsible for preserving what the system observes when a behavioural rule is triggered. This layer is important because a surveillance system should not only detect events in memory and then forget them. It should produce records that can be reviewed, explained, and used to support later response or analysis.

An event is created when one of the behavioural indicators satisfies its configured rule condition. The event record contains structured information about the incident. This may include the event type, timestamp, zone name, track identifier, trigger reason, measured value, configured threshold, and delivery state. The exact fields may vary depending on the event type, but the purpose is the same. The system should record enough information to explain why the event was generated.

The use of structured event records supports both evaluation and real deployment. During evaluation, event records help determine whether the system produced the expected event for each scenario. During deployment, event records allow users to review what happened and when it happened. This also helps debugging because the event record can reveal whether a rule was triggered by zone overlap, duration, count, transition, or movement.

Evidence handling extends the value of the event record. In a visual surveillance system, a text alert alone may not be enough. A snapshot or related visual evidence can help the user verify the situation. Edge Sentinel supports evidence capture within the prototype scope, especially for security events where later review may be required. However, evidence capture must be described as event-based and bounded. The system is not presented as a continuous cloud recording platform.

Local logging is central to the resilience goal of the project. If remote alert delivery fails, the system should still preserve the local record of the event. This means that communication failure should not erase the fact that the event occurred. Local logs and queue files therefore play an important role in maintaining continuity between event detection, alert delivery, and recovery.

The design also supports privacy-conscious operation. The system does not require facial recognition or identity classification for routine event reasoning. It uses anonymous track IDs for temporary continuity. Evidence is associated with triggered security events rather than being treated as identity recognition output. This does not remove all privacy concerns, but it makes the design more limited and responsible than identity-based surveillance.

### Table 3.10: Event Record Schema

| Field                | Description                                                           | Purpose                                                                                  |
| -------------------- | --------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| Event ID             | Unique identifier for the generated event                             | Allows event tracking and reference                                                      |
| Event type           | Category of event generated                                           | Identifies restricted presence, lingering, crowd density, tailgating, or abnormal motion |
| Timestamp            | Time the event was generated                                          | Supports review and ordering of events                                                   |
| Zone name            | Name of the zone associated with the event                            | Provides spatial context                                                                 |
| Track ID             | Temporary anonymous identifier of the tracked person where applicable | Supports continuity without identity recognition                                         |
| Trigger reason       | Human-readable explanation of why the event was generated             | Supports explainability                                                                  |
| Measured value       | Observed value that satisfied the rule                                | May represent count, duration, movement, or transition evidence                          |
| Configured threshold | Threshold used by the rule                                            | Allows comparison between observed condition and configured setting                      |
| Evidence path        | Location of snapshot or related evidence where available              | Supports visual review                                                                   |
| Delivery status      | Indicates whether alert delivery succeeded, failed, or was queued     | Supports alert recovery and monitoring                                                   |
| Retry metadata       | Information about retry attempts where applicable                     | Supports persistent alert delivery                                                       |

The event design gives Edge Sentinel its audit trail. It connects perception, reasoning, evidence, and alerting into a traceable record. This is necessary because the value of a surveillance system depends not only on detecting something, but also on preserving enough context to support action and review.

## 3.12 Alerting and Resilience Design

The alerting and resilience layer is one of the most important parts of Edge Sentinel because the project is motivated by infrastructure-constrained deployment. In many environments, internet connectivity may be unstable or temporarily unavailable. A surveillance system that loses alerts during communication failure would be unreliable, even if its detection pipeline works correctly. The alerting design therefore focuses on retention and recovery.

When a behavioural event is generated, the alert manager attempts to deliver the alert to a configured HTTP endpoint. This represents the normal network-based alert path. If the endpoint is available, the alert can be delivered immediately. If the endpoint is unavailable or delivery fails, the alert is not discarded. Instead, it is stored in a persistent local queue. The queue can then be retried after the endpoint recovers.

This queue-based design supports graceful degradation. During a remote endpoint outage, the system can continue local detection, event reasoning, event logging, and alert queueing. Remote delivery may be delayed, but local event creation does not stop. Once the endpoint becomes available again, queued alerts can be delivered. This behaviour was evaluated in the project using a simulated HTTP endpoint outage and recovery test.

The alert queue is important because it changes how communication failure is handled. Without a queue, a failed alert may be lost permanently. With a persistent queue, the system preserves the alert until delivery becomes possible. This is especially useful in infrastructure-constrained environments where network conditions may fluctuate. The queue does not solve every possible network problem, but it provides a practical recovery mechanism for delivery failure.

The system also includes GSM/SMS capability through the SIMCOM A7670G modem. SMS is relevant because it can serve as a simple fallback notification path when internet-based delivery is not suitable. In the current project, SMS delivery was functionally verified separately from the HTTP queue recovery evaluation. This means the report can state that SMS capability was verified, but it should not claim guaranteed SMS reliability, SMS latency, or formal SMS recovery performance.

The resilience design must therefore be described with careful boundaries. The project evaluated alert retention and recovery under simulated HTTP endpoint outage. It did not perform a full physical network-disconnection campaign, did not measure internet or cloud recovery latency, and did not validate guaranteed SMS delivery under all mobile network conditions. These limitations do not weaken the design. They make the evaluation honest and define what remains for future work.

**Figure 3.6: Alert Delivery and Queue Recovery Flow**

`[Insert alert delivery and queue recovery flow diagram here]`

### Table 3.11: Alerting and Resilience Design

| Component            | Function                                                        | Resilience Contribution                                         |
| -------------------- | --------------------------------------------------------------- | --------------------------------------------------------------- |
| Alert manager        | Receives event alerts and attempts delivery                     | Centralizes alert handling                                      |
| HTTP delivery client | Sends alerts to a configured endpoint                           | Supports normal network-based notification                      |
| Persistent queue     | Stores failed alerts locally                                    | Prevents alert loss during endpoint outage                      |
| Retry mechanism      | Attempts delivery of queued alerts after recovery               | Supports delayed delivery                                       |
| Local event log      | Stores event records independent of remote delivery             | Preserves event history during communication failure            |
| SMS client           | Sends SMS messages through the GSM modem                        | Provides functionally verified fallback notification capability |
| Local interface      | Displays relevant system and queue state within prototype scope | Helps users inspect alert and recovery status                   |

The alerting and resilience design reflects the main contribution of the project. Edge Sentinel is not only designed to detect selected events. It is designed to preserve event information and recover alert delivery when remote communication fails. This makes the system more suitable for environments where reliable connectivity cannot be assumed.
