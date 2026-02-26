# Covenant — Technical Challenge: Detection Event Engine

## Background

Covenant is an autonomous surveillance station that runs computer vision models locally on a Jetson Orin Nano. It generates a continuous stream of object detections from on-site cameras, 24/7.

The raw model output looks like this:

```
timestamp_ms,camera_id,class,confidence,x1,y1,x2,y2
1772124021708,entrada_covenant,person,0.9488,525,684,759,1075
1772124038508,entrada_covenant,person,0.6366,743,452,1084,883
...
```

Each row is one detection fired by the model on a single frame. Detections are noisy — the model fires on shadows, reflections, and brief occlusions. Generating an alert on every detection is not viable: each alert triggers evidence packaging and remote notification, both expensive operations.

Your job is to build the **event engine**: the layer between raw detections and real alerts.

---

## The Task

Implement a Python module called `event_engine` that consumes detections one at a time and decides when to open and close a security event.

**An event opens** when detections of a class of interest, above a confidence threshold, appear on a single camera **continuously for at least `min_duration_ms` milliseconds** (allowing for short gaps up to `gap_tolerance_ms`).

**An event closes** when no qualifying detection appears for longer than `gap_tolerance_ms` milliseconds.

**After an event closes**, a cooldown of `cooldown_ms` applies to that camera before a new event can open.

---

## Configuration

Your engine must read its rules from a YAML file. A reference config is provided in `data/config.yaml`:

```yaml
min_confidence: 0.60
min_duration_ms: 1500
gap_tolerance_ms: 800
cooldown_ms: 30000
classes_of_interest:
  - person
```

No rule values should be hardcoded anywhere in your implementation.

---

## Required Interface

Your module must expose at minimum:

```python
class EventEngine:
    def __init__(self, config_path: str): ...

    def process(self, detection: dict) -> list[dict]:
        """
        Feed one detection record at a time.
        Returns a list of zero or more event dicts triggered by this detection.
        Usually returns an empty list.

        detection format:
        {
            "timestamp_ms": int,
            "camera_id": str,
            "class": str,
            "confidence": float,
            "x1": int, "y1": int, "x2": int, "y2": int
        }
        """
        ...

    def flush(self, current_time_ms: int) -> list[dict]:
        """
        Call this after the last detection (or periodically in production).
        Closes any open events that have timed out.
        Returns any newly closed events.
        """
        ...
```

Each event dict your engine emits must contain at minimum:

```python
{
    "type": "opened" | "closed",
    "camera_id": str,
    "class": str,
    "opened_at_ms": int,
    "closed_at_ms": int | None,   # None when type == "opened"
    "peak_confidence": float,
    "detection_count": int,
}
```

---

## Deliverables

1. **`event_engine.py`** — your implementation
2. **`test_event_engine.py`** — your test suite (use `pytest`)
3. **`run.py`** — a script that reads `data/detections.csv` and `data/config.yaml`, runs them through the engine record by record, and prints resulting events to stdout as JSON (one per line)
4. **`README.md`** (max half a page) answering:
   - What state does your engine track per camera?
   - What would break in a real deployment that your tests do not cover?

---

## Rules

- Python 3.10+, standard library only (plus `pytest` and `pyyaml`)
- Do not use a pre-existing event detection library
- `process()` must work correctly when called one record at a time — it **cannot** read or buffer the entire CSV before starting

---

## Provided Files

| File | Description |
|------|-------------|
| `data/detections.csv` | 230 real detections from `entrada_covenant` over ~6 minutes |
| `data/config.yaml` | Reference configuration |

---

## Bonus (not required)

- Handle detections arriving slightly out of order (up to 500 ms late) without losing or duplicating events
- Support a `zones` config: only trigger an event if the bounding box centroid falls inside a defined rectangular region for that camera
- Emit a third event type `"sustained"` every N seconds while an event remains open, to serve as a keep-alive heartbeat
