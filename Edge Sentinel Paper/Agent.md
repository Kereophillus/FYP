# AGENTS.md — Edge Sentinel Project Instructions

> This file is the single source of truth for all AI agents working on this project.
> Read this fully before making any suggestions, edits, or implementations.
> Full academic proposal is in `docs/Edge_Sentinel_Project_Proposal.docx`.

---

## 1. Project Identity

**Name:** Edge Sentinel
**Type:** Final Year Project (Software Engineering)
**Institution:** Federal University of Technology Akure (FUTA), Nigeria
**Developer:** Austine Chukwuebuka Raphael

### One-line Summary

A resilient edge-native intelligent surveillance system for infrastructure-constrained environments, performing local AI inference, generating explainable behavioral events, capturing evidential snapshots during security events, and delivering alerts through available network channels with GSM/SMS fallback.

---

## 2. System Philosophy (Core Idea)

Edge Sentinel prioritizes **resilience:** edge-first intelligence that remains functional during poor or absent connectivity, with graceful degradation based on available network conditions.

The system is built on these core pillars:

1. **Deterministic behavioral intelligence (rule-based, fully explainable)**
2. **Guaranteed multi-layer alert delivery (WiFi → GSM → SMS, with queuing)**
3. **Power-aware operation (PIR-triggered activation, solar-capable)**

Design principles:

* **Resilience before connectivity** — works fully offline
* **Edge-first intelligence** — all detection runs locally
* **Graceful degradation** — adapts to available network capacity
* **Interpretable and explainable** — no black-box models for behavior
* **Privacy-conscious evidence handling** — captures snapshots only during verified/security events, via authenticated channels only

---

## 3. Target Deployment Hardware

* **Compute:** Raspberry Pi 5 (4GB RAM)
* **Camera:** Low-light capable module (IMX219 target)
* **GSM Module:** USB LTE dongle (AT command capable)
* **PIR Sensor:** Motion-triggered activation
* **Power:** Solar-based system (external module)
* **Environment:** Remote / rural / infrastructure-limited locations

---

## 4. Non-Negotiable Design Decisions

These are strict constraints. Do NOT violate unless explicitly instructed.

---

### 4.1 CPU-Only Execution

* No GPU, no CUDA
* No `.cuda()` or GPU-based inference
* All processing must run on CPU (Raspberry Pi target)

---

### 4.2 Rule-Based Behavioral Inference

* All anomaly detection logic is deterministic Python code
* No ML classifiers for behavior
* No neural behavioral inference

**Reason:**

* Lower compute cost
* Full explainability
* Easier tuning and validation

---

### 4.3 Lightweight Detection Model

* Default: YOLOv8n
* Must remain lightweight

**Allowed:**

* Future benchmarking with YOLOv5n, EfficientDet-lite

**Not allowed:**

* Larger models (v8m, v8l, etc.) unless explicitly requested

---

### 4.4 Multi-Layer Alert System

Strict fallback hierarchy:

1. WiFi → HTTP POST
2. GSM Data → HTTP POST
3. SMS → AT commands (fail-safe)

Rules:

* SMS must always work independently
* No alert should be silently lost
* System must not depend on internet

---

### 4.5 PIR-Based Activation (Power Control)

* PIR sensor runs continuously (low power)
* Full pipeline activates ONLY on motion
* System returns to idle after inactivity timeout

⚠️ Important:
This is **power gating**, not power measurement.

---

### 4.6 No Identity Inference

* No facial recognition or identity matching
* No identity inference or person re-identification across events
* Tracking uses anonymous track IDs only (no biometric features)
* Event evidence (snapshots, logs) is allowed for security purposes
* Evidence is treated as private/sensitive data and must not be shared without authorization

---

### 4.7 Full Edge Processing (Core to Resilience)

* **Core operation:** The Pi must perform detection, event generation, queueing, and SMS fallback without requiring cloud connectivity.
* **Evidence capture:** Snapshots and logs are stored locally and transmitted only when network permits or when the operator authorizes remote sync.
* **Cloud-assisted features:** Cloud connectivity is not required for core operation, but it is an important enhancement for remote monitoring, configuration, evidence review, reporting, event synchronization, and future cloud-assisted analysis.
* **Offline-capable:** System must continue to detect events and deliver SMS alerts even when cloud connectivity is unavailable

---

## 5. Event System (CRITICAL ARCHITECTURE)

This system does NOT just detect events.

It must:

> **Explain why events were triggered**

---

### 5.1 Standard Event Schema (Internal Representation)

Every indicator MUST return structured events:

```python
{
    "event_type": "tailgating",
    "timestamp": "<ISO8601>",
    "zone": "<zone_id>",

    # Core context
    "person_count": int,
    "track_ids": list[int],

    # Measurement data
    "metrics": {
        "observed_value": float,
        "threshold": float
    },

    # Explanation
    "trigger_reason": "<string>",

    # Optional but encouraged
    "confidence": "low" | "medium" | "high"
}
```

---

### 5.2 Mandatory Output Principles

Every event MUST include:

* What happened → `event_type`
* Where → `zone`
* When → `timestamp`
* Supporting data → `person_count`, `metrics`
* Why → `trigger_reason`

---

### 5.3 SMS Output (Minimal)

```
ALERT: <event_type>
Zone: <zone>
Time: <timestamp>
Count: <person_count>
Info: <short explanation>

```

---

### 5.4 Data Output (WiFi / GSM)

```json
{
  "device_id": "edge-sentinel-01",
  "timestamp": "<ISO8601>",
  "event": "<event_type>",
  "zone": "<zone>",
  "person_count": 2,
  "metrics": {
    "observed_value": 0.6,
    "threshold": 1.5
  },
  "trigger_reason": "gap_below_threshold"
}
```
### 5.5 Alerting & Delivery Logic (MANDATORY)
The system distinguishes between LOGGING and ALERTING.

All events must always be:
- Logged locally
- Optionally transmitted via network

Alert escalation depends on network availability:

CASE 1 — Network Available:
- Log event locally
- Send event via WiFi or GSM data
- Server is responsible for user notification

CASE 2 — Network Unavailable:
- Log event locally
- Queue event for later transmission
- If event is classified as an ALERT:
    - MUST send SMS fallback immediately

SMS serves as:
- A direct user alert mechanism
- OR a lightweight data transport layer to a server

Future optimization may include compressed SMS payloads for server decoding.

No alert must ever be silently dropped.
---

## 6. Indicator Modules (Core Intelligence)

Each module must:

* Be independent
* Be testable
* Follow the event schema strictly

---

### 6.1 Lingering Detection

Tracks how long a person stays in a zone.

Trigger conditions:

* Duration > threshold
* OR restricted hours override

Outputs must include:

* duration
* threshold
* trigger_reason:

  * `duration_exceeded`
  * `restricted_hours_override`

---

### 6.2 Tailgating Detection

Detects closely spaced entries.

Trigger:

* Time gap between persons < threshold

Outputs must include:

* person_count
* gap value
* trigger_reason: `gap_below_threshold`

---

### 6.3 Crowd Density

Counts persons per zone.

Trigger:

* Count > threshold

Outputs must include:

* person_count
* threshold
* trigger_reason: `density_exceeded`

---

### 6.4 Abnormal Motion Detection

Detects irregular movement patterns.

Must remain **rule-based**.

Initial logic (simple):

* Direction change frequency
* Speed variance
* Path irregularity

Trigger_reason examples:

* `excessive_direction_changes`
* `irregular_velocity_pattern`

⚠️ Do NOT introduce ML-based anomaly detection.

---

## 7. Configuration System

All thresholds must be configurable.

Use:

* YAML or TOML config file

Do NOT:

* Hardcode thresholds in logic

Configurable items:

* linger duration
* tailgate gap
* density limits
* restricted hours
* zone definitions

---

## 8. Project Structure
```
src/
├── capture/        # raw input (camera)
├── inference/      # detection (YOLO)
├── tracking/       # IDs (later)
├── indicators/     # logic (your "brain")
├── events/         # event schema + formatting
├── comms/          # sending alerts
├── power/          # PIR logic
├── core/           # orchestration (THE GLUE)
└── main.py         # entry point 
```
---

## 9. Current Project State

Working:

* Camera capture
* YOLOv8n detection (CPU)
* Basic event pipeline

Planned:

* Tracker (persistent IDs)
* Indicators (full implementation)
* Comms (WiFi/GSM/SMS)
* Power manager (PIR-triggered flow)

---

## 10. Development Environment

* Python 3.11
* Package manager: `uv` ONLY
* OS: Linux (Ubuntu/Debian)
* Deployment: Raspberry Pi OS (64-bit)

Do NOT:

* Use pip directly
* Introduce OS-specific dependencies

---

## 11. What This Project Is NOT

Do NOT introduce as *core requirements*:

* SaaS platforms as a dependency for core detection/eventing
* Facial recognition or identity inference
* Neural behavioral models as replacement for explainable rules
* GPU-only features that break CPU-only operation

Notes:

* Cloud dashboards and cloud-assisted features are acceptable as optional enhancements (remote monitoring, evidence review, reporting), but they must not be required for the device to detect events, queue alerts, and send SMS fallbacks.
* Continuous cloud streaming is not part of the core prototype; authorized live viewing may be offered later when network conditions and operator consent allow it.

---

## 12. Engineering Principles

All contributions must:

* Be modular
* Be explainable
* Be resource-aware
* Be reproducible
* Avoid unnecessary complexity

---

## 13. Key Rule (Most Important)

> The system must not only detect anomalies — it must clearly explain why they occurred.

If a feature cannot explain its output:
→ It is incorrectly designed.
