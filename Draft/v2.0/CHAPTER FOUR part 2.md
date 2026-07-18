## 4.5 Behavioural Scenario Evaluation

The behavioural scenario evaluation tested whether the implemented indicators produced the expected event outcomes under selected surveillance situations. This evaluation was important because the system was not designed only to detect persons in individual frames. It was designed to use detections, anonymous tracks, zones, thresholds, durations, transitions, and movement evidence to decide whether a security event had occurred.

A total of nine behavioural scenarios were evaluated. These scenarios covered the implemented indicator families: restricted presence, lingering, crowd density, tailgating, and abnormal motion. Each scenario had an expected outcome, and the system output was compared with that expected outcome. The result was interpreted at the scenario-event level. This means the reported precision, recall, and F1 score describe whether the expected behavioural event outcome was produced, not whether the detector achieved a particular mAP or recognition accuracy.

Out of the nine scenarios, eight produced the correct outcome. One scenario produced a false negative. The false negative occurred in the abnormal-motion scenario identified as `abnormal_motion__running_past_frame`. No false-positive scenarios were recorded. This produced a scenario-level precision of 1.000, a scenario-level recall of 0.875, and a scenario-level F1 score of approximately 0.933.

The high precision indicates that the system did not raise incorrect behavioural alerts in the evaluated scenarios. This is important for surveillance because frequent false alerts can reduce user trust and make operators ignore future warnings. The recall value shows that the system detected most of the expected positive scenarios, but missed one abnormal-motion case. The F1 score gives a balanced summary of the system’s event-level performance across the evaluated scenarios.

### Table 4.4: Behavioural Scenario Evaluation Summary

| Evaluation Item           | Result                                                    |
| ------------------------- | --------------------------------------------------------- |
| Total scenarios evaluated | 9                                                         |
| Correct outcomes          | 8                                                         |
| False-negative scenarios  | 1                                                         |
| False-positive scenarios  | 0                                                         |
| Scenario-level precision  | 1.000                                                     |
| Scenario-level recall     | 0.875                                                     |
| Scenario-level F1 score   | Approximately 0.933                                       |
| Failed scenario           | `abnormal_motion__running_past_frame`                     |
| Interpretation boundary   | Scenario-level behavioural outcome, not detector accuracy |

### Table 4.5: Scenario-Level Outcome Matrix

| Scenario Category            | Expected Behaviour                                                                        | System Outcome                                  | Result         |
| ---------------------------- | ----------------------------------------------------------------------------------------- | ----------------------------------------------- | -------------- |
| Restricted presence/access   | Generate event when a person enters or remains in a restricted monitored zone             | Expected event produced                         | Correct        |
| Lingering                    | Generate event when a tracked person remains beyond the configured duration               | Expected event produced                         | Correct        |
| Crowd density                | Generate event when person count exceeds configured threshold                             | Expected event produced                         | Correct        |
| Tailgating                   | Generate event when transition or close-following behaviour satisfies the configured rule | Expected event produced                         | Correct        |
| Abnormal motion              | Generate event when movement satisfies configured abnormal-motion criteria                | Expected event produced in most evaluated cases | Mostly correct |
| Running-past abnormal motion | Generate event for short fast movement across the scene                                   | Event not generated                             | False negative |

The evaluation results show that the rule-based behavioural design was effective across most of the tested scenarios. The result is also useful because it shows both the strength and boundary of the system. Edge Sentinel performed well for the evaluated rule conditions, especially where events were sustained long enough for detection, tracking, and rule evaluation. However, the running-past false negative shows that very short motion events can challenge rules that require sustained evidence across time.

This outcome supports the design choice of using explainable behavioural indicators. Because the rules are interpretable, the false negative could be analyzed rather than treated as an unexplained model failure. The system did not simply fail silently. The evaluation made it possible to identify why the event was missed and what type of refinement would be needed in future work.

## 4.6 Running-Past False Negative Analysis

The only failed scenario in the behavioural evaluation was `abnormal_motion__running_past_frame`. This scenario was intended to test whether the abnormal-motion indicator could detect a person moving quickly across the frame. The expected outcome was an abnormal-motion event. The system did not generate the expected event, which made the scenario a false negative.

The failure was not caused by the complete absence of person detection or tracking. The persons involved were detected and tracked, but the tracks were too brief to satisfy the sustained-duration requirement used by the abnormal-motion rule. The rule required movement evidence to persist for a configured duration of 0.6 seconds. In the running-past scenario, the motion was short and passed through the frame quickly. As a result, the system observed fast movement evidence, but not for long enough to trigger the abnormal-motion event under the configured rule.

This result is important because it exposes a real design trade-off. A shorter duration threshold may allow the system to detect very brief running-past events, but it may also increase the risk of false positives from noisy detections, brief tracker jitter, or sudden box movement. A longer duration threshold makes the rule more conservative, but it may miss short events. The prototype chose not to overfit the rule to one difficult clip because doing so could reduce the reliability of the system in other scenarios.

The false negative therefore does not mean that the entire abnormal-motion indicator is unusable. Rather, it shows that the current rule is better suited to sustained abnormal movement than to very short fast crossings. This distinction should be preserved in the final interpretation of the result. The system can support abnormal-motion reasoning under configured conditions, but the present implementation has a known limitation with short-duration motion events.

### Table 4.6: Running-Past False Negative Diagnostic Summary

| Diagnostic Item         | Observation                                                                      |
| ----------------------- | -------------------------------------------------------------------------------- |
| Failed scenario         | `abnormal_motion__running_past_frame`                                            |
| Expected event          | Abnormal-motion event                                                            |
| Actual outcome          | Event not generated                                                              |
| Detection status        | Persons were detected                                                            |
| Tracking status         | Tracks were produced, but were too brief                                         |
| Main reason for failure | Sustained-duration requirement was not satisfied                                 |
| Relevant rule condition | 0.6-second sustained-duration requirement                                        |
| Interpretation          | The current abnormal-motion rule is less effective for very short fast crossings |
| Design decision         | The rule was not over-tuned to force one difficult clip to pass                  |

The running-past failure is a useful finding because it improves the honesty of the evaluation. It shows that the system was tested under a condition that exposed a boundary, and the boundary was reported rather than hidden. This strengthens the credibility of the project because the final claims are based on what the prototype actually demonstrated.

## 4.7 YOLOv8n Sensitivity Experiment

After the running-past false negative was observed, YOLOv8n with ByteTrack was tested as a sensitivity experiment. The purpose of this experiment was to determine whether using a different detector would change the outcome of the difficult short-crossing abnormal-motion scenario. It was not a renewed model-selection campaign, and it was not intended to replace the selected reference pipeline unless it clearly resolved the issue without unacceptable trade-offs.

The YOLOv8n test produced stronger instantaneous speed evidence than the EfficientDet-Lite0 reference pipeline. This suggests that the detector-tracker combination captured some aspects of the fast movement more strongly. However, the event still did not satisfy the 0.6-second sustained-duration requirement. The abnormal-motion event therefore remained undetected under the configured rule.

This result showed that the limitation was not solved simply by switching the detector. The core problem was related to the duration and rule logic of the abnormal-motion indicator. Even when stronger instantaneous speed evidence was available, the movement did not persist long enough to satisfy the sustained condition. This means that future improvement should focus on rule design for short-duration motion, rather than assuming that a heavier detector alone will fix the issue.

The YOLOv8n sensitivity experiment also revealed a performance trade-off. YOLOv8n operated with substantially higher detector latency and lower pipeline throughput than the EfficientDet-Lite0 reference pipeline. Since Edge Sentinel targets Raspberry Pi-class deployment, this trade-off matters. A detector that gives slightly stronger evidence in one diagnostic case may still be unsuitable if it reduces the overall responsiveness or efficiency of the pipeline.

For these reasons, EfficientDet-Lite0 with ByteTrack remained the selected reference detector-tracker pair. YOLOv8n was retained only as a diagnostic comparison for understanding the running-past edge case. No YOLOv5n follow-up was required because the sensitivity result already showed that the main unresolved issue was the sustained-duration rule rather than simply the detector family.

### Table 4.7: YOLOv8n Sensitivity Experiment Summary

| Item                      | Observation                                                       | Interpretation                                                    |
| ------------------------- | ----------------------------------------------------------------- | ----------------------------------------------------------------- |
| Purpose of test           | Diagnostic sensitivity analysis for running-past false negative   | Not a renewed model-selection campaign                            |
| Alternative pipeline      | YOLOv8n with ByteTrack                                            | Tested only for the difficult short-crossing case                 |
| Observed benefit          | Stronger instantaneous speed evidence                             | YOLOv8n captured some fast movement evidence more strongly        |
| Remaining issue           | 0.6-second sustained-duration requirement was still not satisfied | The abnormal-motion event still failed                            |
| Performance trade-off     | Higher detector latency and lower pipeline throughput             | Less suitable for the final Raspberry Pi reference pipeline       |
| Final decision            | EfficientDet-Lite0 with ByteTrack remained the reference pair     | YOLOv8n did not replace the selected pipeline                     |
| Further YOLOv5n follow-up | Not required for final evaluation path                            | The main limitation was rule duration, not simply detector choice |

The YOLOv8n sensitivity experiment helped clarify the nature of the failure. It showed that the running-past scenario was not missed merely because EfficientDet-Lite0 was the selected detector. The missed event was tied to the abnormal-motion rule’s requirement for sustained evidence. This finding supports a careful final conclusion: Edge Sentinel demonstrated strong scenario-level performance under the evaluated conditions, but short-duration motion remains a limitation for future refinement.
