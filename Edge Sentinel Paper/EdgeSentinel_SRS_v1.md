**EDGE SENTINEL**

Offline Edge-Based Surveillance System

**Software Requirements Specification (SRS)**

Version 1.0 | April 2026

| **Author**           | _\[Your Full Name\]_                    |
| -------------------- | --------------------------------------- |
| **Matric Number**    | _\[Your Matric No.\]_                   |
| **Supervisor**       | _\[Supervisor Name & Title\]_           |
| **Department**       | Software Engineering                    |
| **Institution**      | Federal University of Technology, Akure |
| **Academic Session** | 2025/2026                               |

# **1\. Introduction**

## **1.1 Purpose**

This document specifies the functional and non-functional requirements for Edge Sentinel, an edge-based, offline-capable surveillance system developed as a Final Year Project at the Federal University of Technology, Akure (FUTA). It serves as the primary reference for development, testing, stakeholder communication, and academic evaluation.

## **1.2 Scope**

Edge Sentinel is a self-contained surveillance system that:

- Captures video input via a CSI camera module attached to a Raspberry Pi 5
- Performs on-device object detection using an optimised, quantised computer vision model
- Detects predefined events (human intrusion, loitering, tailgating, etc.)
- Logs detection events locally to persistent storage
- Transmits lightweight alert payloads via GSM to a remote recipient or dashboard
- Operates independently of continuous internet or cloud connectivity

The system is designed for deployment in environments with unreliable broadband infrastructure, such as rural facilities, campuses, or small commercial premises in Nigeria.

## **1.3 Definitions & Acronyms**

| **Term**       | **Definition**                                                                          |
| -------------- | --------------------------------------------------------------------------------------- |
| Edge Computing | On-device data processing without reliance on a remote server or cloud                  |
| Inference      | Running a trained ML model on new input data to produce predictions                     |
| TFLite         | TensorFlow Lite - a lightweight ML runtime optimised for embedded/mobile devices        |
| GSM            | Global System for Mobile Communications - used here for SMS/data alerting over cellular |
| FPS            | Frames per second - rate at which the camera feed is processed                          |
| BOM            | Bill of Materials - itemised list of hardware components and costs                      |
| PIR            | Passive Infrared sensor - detects motion to trigger or wake the system                  |
| CV             | Computer Vision                                                                         |
| SRS            | Software Requirements Specification                                                     |

## **1.4 Intended Audience**

- Project supervisors and academic evaluators
- Stakeholders and sponsors
- The developer (primary implementer)

## **1.5 Document Overview**

Section 2 describes the overall system context and constraints. Section 3 details system features. Sections 4-5 specify functional and non-functional requirements. Section 6 covers system architecture. Section 7 describes the ML pipeline and methodology. Section 8 addresses the project's academic contribution.

# **2\. Overall Description**

## **2.1 Product Perspective**

Edge Sentinel is a standalone embedded system. Unlike conventional IP camera systems that rely on continuous cloud upload and remote processing, Edge Sentinel performs all intelligence locally on the device. The system integrates hardware and software into a single weatherproof unit.

**\[PLACEHOLDER: System Context Diagram\]**

_Insert a diagram showing: Camera → RPi5 (inference + logging) → GSM Module → Recipient/Dashboard. Show solar power input. Show local storage output. Show absence of cloud dependency._

## **2.2 Product Functions - Summary**

| **Function**     | **Description**                                                        |
| ---------------- | ---------------------------------------------------------------------- |
| Video Capture    | Continuous or PIR-triggered frame acquisition from CSI camera          |
| Object Detection | On-device inference to identify persons, vehicles, or defined objects  |
| Event Processing | Rule-based filtering of raw detections into meaningful security events |
| Local Logging    | Persistent JSON/CSV storage of timestamped events                      |
| GSM Alerting     | Lightweight data or SMS transmission on critical event trigger         |
| Power Management | PIR-triggered idle/active throttling to conserve solar energy          |

## **2.3 User Characteristics**

Two user types are anticipated:

- End Users (security personnel): Non-technical. Receive SMS/data alerts. Do not interact with the ML pipeline.
- Technical Administrator (developer/maintainer): Configures thresholds, updates model, reviews logs via SSH or local interface.

## **2.4 Operating Environment**

| **Parameter**    | **Value**                                                       |
| ---------------- | --------------------------------------------------------------- |
| Primary Hardware | Raspberry Pi 5 (4GB RAM)                                        |
| Operating System | Raspberry Pi OS (Debian-based Linux, 64-bit)                    |
| Python Runtime   | Python 3.11                                                     |
| ML Runtime       | PyTorch / Ultralytics (development); TFLite Runtime (deployment target, post-conversion) |
| Camera           | Official CSI IR Camera Module                                   |
| Communication    | LTE USB Modem (prototype) / GSM SIM800L (production target)     |
| Power            | 40W Solar Panel + MPPT Controller + 12V 20Ah Deep Cycle Battery |
| Storage          | 64GB High-Endurance MicroSD + Optional External SSD             |
| Network          | GSM 2G/3G/4G (Nigerian carriers: MTN, Airtel)                   |

## **2.5 Constraints**

- CPU-only inference - no GPU or hardware accelerator available on RPi5 in this configuration
- Limited and variable power supply from solar source (~15-25W usable in Nigerian conditions)
- Intermittent GSM connectivity - system must tolerate failed transmissions gracefully
- Edge storage limitations - logs must be managed to prevent overflow
- Real-time performance requirement - inference pipeline targets 1–3 FPS in development (PyTorch/CPU baseline); 5 FPS is the aspirational target post-optimisation via INT8 quantisation and TFLite conversion

## **2.6 Assumptions & Dependencies**

- Camera feed is physically stable and consistently illuminated (IR LEDs handle night conditions)
- At least one GSM carrier (MTN or Airtel) provides usable signal at deployment site
- Solar panel receives minimum 4 peak sun hours per day for sustainable operation
- The system is developed using PyTorch/Ultralytics (YOLOv8n) as the development runtime; the model will be converted to TFLite format for final deployment on the Pi
- Python 3.11 and required libraries are pre-installed on the device image

# **3\. System Features**

## **3.1 Video Capture Module**

Responsible for acquiring frames from the CSI camera and passing them to the inference engine.

- Continuous frame acquisition via OpenCV VideoCapture
- Configurable frame rate (target: 5-15 FPS, adjusted by power mode)
- PIR sensor integration: system transitions from low-power idle to active capture on motion detection
- Graceful error handling on camera disconnection - logs fault, retries, sends alert if persistent

**\[PLACEHOLDER: Video Capture Module Diagram\]**

_Insert flow diagram: PIR Trigger → Camera Wake → Frame Buffer → Pass to Inference Engine. Include idle/active state transition._

## **3.2 Object Detection Module**

The core AI component. Performs inference on each captured frame.

- Development runtime: YOLOv8n via Ultralytics/PyTorch (CPU-only); all development and testing is performed in this configuration
- Deployment runtime: YOLOv8n exported to TFLite FlatBuffer (INT8 quantised) for final Pi deployment
- Accepts RGB frames from the capture module
- Returns bounding boxes, class labels, and confidence scores per detection
- Supports configurable confidence threshold (default: 0.6, tunable per deployment)
- Target classes include: Person, Vehicle \[extend as needed for specific deployment\]

**\[PLACEHOLDER: Model Architecture Diagram / Benchmark Table\]**

_Insert: (a) diagram or summary of chosen model architecture (e.g., YOLOv8n, EfficientDet-Lite0), and (b) benchmark table showing model size (MB), inference latency (ms) on RPi5 CPU, and mAP score._

## **3.3 Event Processing Module**

Converts raw detection outputs into meaningful security events with filtering to reduce false positives.

- Applies minimum confidence threshold filter
- Suppresses duplicate detections within a configurable time window (default: 5 seconds)
- Rule engine: defines named event types (e.g., 'Lingering', 'Tailgating', 'Crowd Density Exceeded', 'Abnormal Motion') based on detection patterns
- Assigns event severity level: INFO, WARNING, CRITICAL
- Every event must include a `trigger_reason` field explaining why it was triggered (e.g., `duration_exceeded`, `gap_below_threshold`, `density_exceeded`)

### Standard Event Schema

Every indicator module must return a structured event conforming to this schema:

```python
{
    "event_type": str,          # e.g. "lingering", "tailgating"
    "timestamp": str,           # ISO8601
    "zone": str,                # zone_id
    "person_count": int,
    "track_ids": list[int],     # anonymous session IDs only
    "metrics": {
        "observed_value": float,
        "threshold": float
    },
    "trigger_reason": str,      # mandatory explanation
    "confidence": str           # "low" | "medium" | "high"
}
```

### Logging vs Alerting Distinction

The system distinguishes between logging and alerting:

- **All events** are always logged locally to persistent storage, regardless of network state
- **Network transmission** (WiFi or GSM data) is attempted for all events when available; failed transmissions are queued
- **SMS fallback** is triggered immediately for CRITICAL events when network is unavailable — SMS serves as a guaranteed direct alert to the operator
- No event shall ever be silently dropped

## **3.4 Local Logging Module**

Provides persistent, structured event storage on the device.

- Records: timestamp (ISO 8601), event type, object class, confidence score, bounding box coordinates, frame reference
- Storage format: JSON Lines (.jsonl) or CSV - configurable
- Rolling log management: archives and compresses logs older than 7 days; maintains maximum storage quota
- Logs persist across device reboots (written to MicroSD/SSD, not RAM)
- Optional: annotated frame snapshots saved on CRITICAL events

**\[PLACEHOLDER: Sample Log Entry\]**

_Insert a real or realistic sample log entry in JSON format once the system is running. Example fields: timestamp, event_type, class_label, confidence, bbox, severity._

## **3.5 GSM Communication Module**

Transmits alert payloads externally over the cellular network. Designed for minimal data usage.

- Interfaces with LTE USB modem (prototype) or SIM800L (production) via AT commands / serial
- Alert payload: JSON object ~0.5-1 KB per event (event type, timestamp, device ID, confidence)
- Transmission modes: (1) HTTP POST to remote endpoint over mobile data, or (2) SMS fallback
- Retry logic: queues failed transmissions locally and retries on next successful network registration
- Daily data estimate: ~3 MB/month at 100 alerts/day - compatible with basic Nigerian data plans

**\[PLACEHOLDER: GSM Alert Payload Example\]**

_Insert a real sample JSON payload once tested. Should show: device_id, timestamp, event_type, confidence, and any relevant metadata._

## **3.6 Power Management Module**

Manages system power states to maintain viability under solar supply constraints (~15.4W average draw vs ~15-25W available).

- Two operational modes: ACTIVE (full inference pipeline running) and IDLE (reduced CPU clock, camera suspended)
- PIR sensor triggers transition from IDLE to ACTIVE
- Configurable inactivity timeout: return to IDLE after N seconds of no detection (default: 30s)
- Power consumption is estimated via software logging: FPS, CPU utilisation, RAM usage, and CPU temperature are recorded continuously as proxy metrics for compute load and energy draw
- No hardware power monitoring module (e.g. INA226) is used; all power analysis is software-derived

**\[PLACEHOLDER: Power State Machine Diagram\]**

_Insert state machine diagram showing: IDLE → (PIR trigger) → ACTIVE → (timeout/no detection) → IDLE, and ACTIVE/IDLE → (low battery) → DEEP SLEEP._

# **4\. Functional Requirements**

<div class="joplin-table-wrapper"><table><tbody><tr><th><p><strong>FR-01</strong></p></th><th><p><strong>Frame Capture</strong></p><ul><li>System shall capture video frames continuously when in ACTIVE mode</li><li>System shall support configurable frame rate between 1 and 30 FPS</li><li>System shall log a fault event and attempt recovery on camera disconnection</li></ul></th></tr></tbody></table></div>

<div class="joplin-table-wrapper"><table><tbody><tr><th><p><strong>FR-02</strong></p></th><th><p><strong>Motion-Triggered Wake</strong></p><ul><li>System shall monitor PIR sensor output continuously, including in IDLE mode</li><li>System shall transition from IDLE to ACTIVE within 500ms of PIR trigger</li><li>System shall return to IDLE after a configurable inactivity period</li></ul></th></tr></tbody></table></div>

<div class="joplin-table-wrapper"><table><tbody><tr><th><p><strong>FR-03</strong></p></th><th><p><strong>Object Detection</strong></p><ul><li>System shall run inference on each captured frame using YOLOv8n (PyTorch/CPU in development; TFLite post-conversion for deployment)</li><li>System shall return bounding boxes, class labels, and confidence scores</li><li>System shall filter detections below the configured confidence threshold</li></ul></th></tr></tbody></table></div>

<div class="joplin-table-wrapper"><table><tbody><tr><th><p><strong>FR-04</strong></p></th><th><p><strong>Event Classification</strong></p><ul><li>System shall classify detections into named event types per the rule engine</li><li>System shall assign a severity level (INFO / WARNING / CRITICAL) to each event</li><li>System shall suppress duplicate events within a configurable time window</li></ul></th></tr></tbody></table></div>

<div class="joplin-table-wrapper"><table><tbody><tr><th><p><strong>FR-05</strong></p></th><th><p><strong>Local Logging</strong></p><ul><li>System shall persist all events to local storage in JSON Lines or CSV format</li><li>System shall include timestamp, event type, class, confidence, and bbox per record</li><li>System shall enforce storage quota limits via rolling log archival</li><li>Logs shall survive system reboot without data loss</li></ul></th></tr></tbody></table></div>

<div class="joplin-table-wrapper"><table><tbody><tr><th><p><strong>FR-06</strong></p></th><th><p><strong>GSM Alert Transmission</strong></p><ul><li>System shall attempt alert delivery via a strict three-layer fallback hierarchy: (1) WiFi HTTP POST → (2) GSM data HTTP POST → (3) SMS via AT commands</li><li>SMS layer must always remain functional as a guaranteed fail-safe; system must be able to operate with ONLY SMS available</li><li>All events shall be logged locally regardless of transmission outcome — no alert shall be silently dropped</li><li>Events that cannot be transmitted immediately shall be queued locally and retransmitted upon network availability</li><li>CRITICAL events shall additionally trigger an immediate SMS regardless of whether WiFi/GSM data transmission succeeded</li></ul></th></tr></tbody></table></div>

<div class="joplin-table-wrapper"><table><tbody><tr><th><p><strong>FR-07</strong></p></th><th><p><strong>Offline Operation</strong></p><ul><li>System shall perform all detection, classification, and logging without internet connectivity</li><li>Loss of GSM connectivity shall not interrupt local detection or logging functions</li></ul></th></tr></tbody></table></div>

<div class="joplin-table-wrapper"><table><tbody><tr><th><p><strong>FR-08</strong></p></th><th><p><strong>Power State Management</strong></p><ul><li>System shall enter IDLE state after a configurable inactivity timeout</li><li>System shall log power proxy metrics continuously (FPS, CPU utilisation, RAM usage, CPU temperature) for post-hoc energy analysis</li><li>Power state transitions shall be logged with timestamps</li></ul></th></tr></tbody></table></div>

# **5\. Non-Functional Requirements**

## **5.1 Performance**

| **Metric**         | **Requirement**                                                        |
| ------------------ | ---------------------------------------------------------------------- |
| Inference Latency  | ≤ 500ms per frame (target: ≤ 300ms with quantised model)               |
| Processing Rate    | 1–3 FPS sustained in development (PyTorch/CPU baseline); 5 FPS target post-TFLite optimisation |
| Alert Transmission | GSM alert dispatched within 2 seconds of CRITICAL event classification |
| Wake Latency       | IDLE → ACTIVE transition within 500ms of PIR trigger                   |

**\[PLACEHOLDER: Performance Benchmark Results\]**

_Insert measured results here after system testing: actual inference latency (ms), achieved FPS on RPi5, and wake transition time._

## **5.2 Reliability**

- System shall recover from transient software faults via watchdog process or systemd service restart (configured as a deployment step — a systemd unit file restarts the process automatically on crash; no complex code required)
- Local logs shall be durable across unexpected power loss (using append-only write strategy)
- GSM module shall be polled for network registration status before each transmission attempt
- System uptime target: ≥ 99% during peak sun hours (assuming stable solar input)

## **5.3 Portability & Deployment**

- System shall be deployable via a single setup script with no manual dependency installation
- Model can be replaced by substituting a compatible TFLite file and updating the class map configuration
- System is designed for Raspberry Pi OS 64-bit; porting to other ARM Linux devices should require minimal changes

## **5.4 Maintainability**

- Codebase shall follow a modular architecture with strict separation between: capture, inference, event processing, logging, and communication layers
- Configuration parameters (thresholds, timeouts, file paths, GSM endpoint) shall be centralised in a single config file
- All modules shall emit structured log output to support remote diagnostics

## **5.5 Security**

- Alert payloads shall not contain raw video frames or personally identifiable information
- Device access shall require SSH key authentication (password login disabled)
- GSM transmission endpoint should use HTTPS where supported; credentials shall be stored in environment variables, not source code

## **5.6 Power & Energy Efficiency**

- System average draw shall not exceed 16W in sustained ACTIVE mode
- IDLE mode shall reduce system draw to ≤ 5W
- System shall be self-sustaining under ≥ 4 peak solar hours per day in Nigerian conditions

# **6\. System Architecture**

## **6.1 High-Level Architecture**

**\[PLACEHOLDER: Full System Architecture Diagram\]**

_Insert a professional architecture diagram showing all hardware and software components: Solar Panel → MPPT → Battery → RPi5. On the RPi5: Camera → Capture Module → Inference Engine → Event Processor → \[Logger + GSM Module\]. Also show PIR → Power Manager → Capture Module control path. Tools: draw.io, Lucidchart, or hand-drawn scan._

## **6.2 Software Component Diagram**

**\[PLACEHOLDER: Software Component / Module Diagram\]**

_Insert a UML-style component diagram showing all software modules and their interfaces/dependencies: `capture` → `inference` → `tracking` → `indicators` → `events` → `comms`. Show `power` controlling `capture` via PIR trigger. Show `core` as the central orchestration layer that coordinates all modules and keeps `main.py` clean. Show `main.py` as the entry point that initialises `core`._

## **6.3 Data Flow**

| **Stage**             | **Description**                                                                          |
| --------------------- | ---------------------------------------------------------------------------------------- |
| 1. Frame Acquisition  | PIR trigger → Camera wake → OpenCV VideoCapture → Frame Buffer (RAM)                    |
| 2. Inference          | Frame Buffer → YOLOv8n (PyTorch/CPU → TFLite post-deployment) → Detections (bbox, class, conf) |
| 3. Tracking           | Detections → Multi-object tracker → Persistent anonymous track IDs                      |
| 4. Indicator Logic    | Tracked objects → Rule-based indicator modules → Structured event with trigger_reason    |
| 5. Orchestration      | `core` module coordinates pipeline; routes events to logging and alerting                |
| 6. Logging            | All events → JSON Lines Writer → MicroSD (persistent, regardless of network state)      |
| 7. Alerting           | CRITICAL events → Payload Builder → WiFi HTTP POST → GSM data HTTP POST → SMS fallback  |

## **6.4 Hardware Integration Diagram**

**\[PLACEHOLDER: Hardware Wiring / Integration Diagram\]**

_Insert a diagram or photo showing how components connect to the RPi5 GPIO and USB ports: CSI ribbon cable (camera), GPIO pins (PIR, IR LEDs, GSM serial if applicable), USB (LTE modem), power rails from buck converter._

# **7\. ML Pipeline & Development Methodology**

## **7.1 Model Selection**

The system uses a pre-trained, lightweight object detection model suited for CPU inference on ARM hardware. Selection criteria:

| **Criterion**        | **Target**                                                             |
| -------------------- | ---------------------------------------------------------------------- |
| Model Size           | < 10 MB (post-quantisation) to fit comfortably in RPi5 RAM             |
| Inference Speed      | < 300ms per frame on RPi5 CPU (target)                                 |
| Accuracy (mAP)       | \> 0.45 mAP on COCO validation set for Person/Vehicle classes          |
| TFLite Compatibility | Must be exportable to TFLite FlatBuffer format without unsupported ops |
| Licence              | Must permit academic/non-commercial use                                |

**\[PLACEHOLDER: Model Selection Comparison Table\]**

_Insert a table comparing 2-3 candidate models (e.g., YOLOv8n, EfficientDet-Lite0, MobileNet SSD) across the above criteria. Include actual benchmark numbers once measured._

## **7.2 Development Pipeline**

- Model acquisition: Download pre-trained weights (COCO-trained baseline)
- Fine-tuning (if required): Additional training on domain-specific data using cloud GPU (Colab/Kaggle)
- Evaluation: Measure mAP, precision, recall on held-out validation set
- Conversion: Export to TFLite FlatBuffer; apply INT8 or FP16 quantisation
- On-device validation: Run converted model on RPi5; measure latency, FPS, accuracy delta post-quantisation
- Pipeline integration: Wire model into capture → inference → event processor chain
- System testing: End-to-end testing of full pipeline including GSM alerting
- Optimisation: Profile and tune for power/performance balance

**\[PLACEHOLDER: Training / Evaluation Results\]**

_Insert training curves (loss, mAP vs epoch) and final evaluation metrics (precision, recall, mAP@0.5) once model training/fine-tuning is complete._

## **7.3 Quantisation Strategy**

Post-training quantisation will be applied to reduce model size and inference latency on CPU:

- Primary target: INT8 full-integer quantisation using TFLite converter with representative dataset
- Fallback: FP16 quantisation if INT8 produces unacceptable accuracy degradation (>5% mAP drop)
- Accuracy delta between float32 baseline and quantised model to be documented

**\[PLACEHOLDER: Quantisation Results Table\]**

_Insert a table comparing: Model variant | Size (MB) | Inference latency (ms) on RPi5 | mAP | Accuracy delta vs baseline._

# **8\. Academic Contribution & Validation**

## **8.1 Research Contribution**

Edge Sentinel addresses a gap between state-of-the-art computer vision research and practical deployment in infrastructure-constrained environments. Specifically, it demonstrates:

| **Contribution**              | **Significance**                                                                                                                         |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| Offline-first AI surveillance | A fully functional CV-based security pipeline operating without cloud dependency - relevant to rural and semi-urban Nigerian deployments |
| Edge deployment optimisation  | Quantitative documentation of model compression trade-offs (size, speed, accuracy) on consumer ARM hardware                              |
| GSM-native alerting           | A low-bandwidth alerting architecture (~3 MB/month) replacing high-bandwidth cloud video streaming                                       |
| Energy-aware AI inference     | PIR-triggered power state management enabling solar-only operation in a compute-intensive application                                    |

## **8.2 Evaluation Plan**

System performance will be evaluated across three dimensions:

### **8.2.1 Model Accuracy**

- mAP@0.5 on a held-out test set
- Precision and recall per target class
- Accuracy retention after quantisation (float32 → INT8)

### **8.2.2 System Performance**

- Sustained FPS on RPi5 under real workload
- End-to-end latency: PIR trigger → alert transmitted
- System CPU and RAM utilisation during inference

### **8.2.3 Power & Sustainability**

- Average system draw (W) in ACTIVE and IDLE modes - measured, not estimated
- Battery duration under simulated low-sun conditions
- GSM data consumption over a 24-hour simulated operation period

**\[PLACEHOLDER: Evaluation Results Summary Table\]**

_Insert final evaluation results across all three dimensions once testing is complete. This table will be a key submission deliverable._

## **8.3 Stakeholder Value Proposition**

For stakeholders questioning the cost and complexity of this prototype:

| **Dimension**               | **Commercial Cloud System**     | **Edge Sentinel (This Project)** |
| --------------------------- | ------------------------------- | -------------------------------- |
| Internet Required           | Yes - continuous                | No - GSM only                    |
| Monthly Recurring Cost      | ₦15,000-50,000 (cloud fees)     | < ₦500 (data plan)               |
| Video Bandwidth             | High (continuous stream)        | Negligible (text alerts)         |
| Data Privacy                | Video stored on 3rd-party cloud | All data stays on-device         |
| Rural Viability             | Low (requires broadband)        | High (GSM-only)                  |
| Prototype Unit Cost         | ₦30,000-80,000 (camera only)    | ₦910,000 (full system)           |
| Production Unit Cost (est.) | Not applicable                  | ₦150,000-350,000                 |

# **9\. Future Work**

The following extensions are outside scope for v1 but are identified as logical next steps:

| **Extension**                 | **Description**                                                                           |
| ----------------------------- | ----------------------------------------------------------------------------------------- |
| Multi-camera support          | Extend pipeline to handle 2-4 camera feeds on a single unit or distributed cluster        |
| Mobile companion app          | React Native app for real-time alert viewing and remote configuration                     |
| Advanced event classification | Re-identification, crowd density estimation, abandoned object detection                   |
| Edge clustering               | Multiple Pi units sharing a common GSM uplink node for campus-scale deployment            |
| Custom SBC migration          | Replace Raspberry Pi 5 with purpose-built board (Rockchip RK3588) to reduce BOM to ~₦150k |
| OTA model updates             | Push updated model weights to deployed devices over GSM without physical access           |

# **10\. Appendices**

## **Appendix A: Hardware Bill of Materials**

| **Component**               | **Cost (₦)** |
| --------------------------- | ------------ |
| Raspberry Pi 5 (4GB)        | ₦160,000     |
| CSI IR Camera Module        | ₦40,000      |
| IR LED Illumination Module  | ₦25,000      |
| PIR Motion Sensor           | ₦8,000       |
| 64GB High-Endurance MicroSD | ₦25,000      |
| Official Cooling Case       | ₦25,000      |
| LTE USB Modem               | ₦70,000      |
| SIM Card                    | ₦2,000       |
| First Month Data Plan       | ₦15,000      |
| 40W Solar Panel             | ₦50,000      |
| MPPT Charge Controller      | ₦55,000      |
| 12V 20Ah Deep Cycle Battery | ₦140,000     |
| 12V→5V Buck Converter       | ₦20,000      |
| Weatherproof Enclosure      | ₦60,000      |
| Mounting Brackets & Wiring  | ₦40,000      |
| Surge Protector / DC Fuse   | ₦20,000      |
| Pole / Mount Stand          | ₦50,000      |
| External SSD (Logging)      | ₦80,000      |
| TOTAL (Prototype)           | ₦885,000     |

## **Appendix B: Software Dependencies**

| **Package**             | **Purpose**                                                        |
| ----------------------- | ------------------------------------------------------------------ |
| Python 3.11             | Runtime                                                            |
| OpenCV (cv2)            | Frame capture and image processing                                 |
| Ultralytics / PyTorch   | YOLOv8n inference (development runtime, CPU-only)                  |
| TensorFlow Lite Runtime | On-device model inference (deployment target, post-conversion)     |
| NumPy                   | Array operations                                                   |
| pyserial                | GSM module serial communication (AT commands)                      |
| Requests                | HTTP POST for WiFi/GSM data alert transmission                     |
| PyYAML / TOML           | Configuration file parsing                                         |
| systemd                 | Service management and watchdog (deployment only)                  |

> **Note:** All dependencies are managed via `uv`. Never use `pip install` directly.

## **Appendix C: Version History**

| **Version**       | **Changes**                                                                             |
| ----------------- | --------------------------------------------------------------------------------------- |
| v1.0 - April 2026 | Initial SRS. Full system requirements documented. Placeholders pending testing results. |
| v2.0 - May 2026   | RAM corrected to 4GB. FPS targets clarified (1–3 FPS dev baseline; 5 FPS post-TFLite). ML runtime updated to PyTorch dev / TFLite deployment. Three-layer alert hierarchy made explicit. Power monitoring changed from INA226 hardware to software-based logging. INA226 removed from BOM. `core` orchestration module added to architecture. systemd watchdog framed as deployment step. HTTPS softened to recommended. Dependencies updated. |

## **Appendix D: References**

**\[PLACEHOLDER: References / Bibliography\]**

_Insert academic references here in your institution's required citation format (IEEE, APA, etc.). Include: TFLite documentation, YOLO/EfficientDet papers, any Nigerian connectivity / IoT deployment studies cited in your literature review._

_- End of Document -_