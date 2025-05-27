# Adapt Pipeline Plugin

The **Adapt Pipeline Plugin** brings rule-based intent parsing to the **OVOS intent pipeline** using the Adapt parser. It supports `high`, `medium`, and `low` confidence intent detection and integrates seamlessly with OVOS’s multi-stage pipeline.

While Adapt is powerful for **explicit, deterministic matching**, it has notable limitations in multilingual environments and complex skill ecosystems. **In general, Adapt is not recommended for broad deployments**—it is best suited for **personal skills** where you control the full context and can craft precise intent rules.

---

## Pipeline Stages

This plugin registers three pipelines:

| Pipeline ID    | Description                          | Recommended Use        |
| -------------- | ------------------------------------ | ---------------------- |
| `adapt_high`   | High-confidence Adapt intent matches | ✅ Personal skills only |
| `adapt_medium` | Medium-confidence Adapt matches      | ⚠️ Use with caution    |
| `adapt_low`    | Low-confidence Adapt matches         | 🚫 Not recommended     |

Each pipeline is scored by Adapt and routed according to configured confidence thresholds.

---

## Limitations

Adapt requires **hand-crafted rules** for every intent:

* ❌ **Poor scalability** — hard to manage with many skills
* ❌ **Difficult to localize** — rules rely on exact words and phrases
* ❌ **Prone to conflicts** — multiple skills defining overlapping rules can cause collisions or missed matches

As your skill library grows or if you operate in a multilingual setup, these problems increase.

**Recommendation:**

> 🟢 Use Adapt **only** in personal projects or controlled environments where you can fully define and test every possible phrase.

---

## Configuration

Adapt confidence thresholds can be set in `ovos.conf`:

```json
"intents": {
  "adapt": {
    "conf_high": 0.65,
    "conf_med": 0.45,
    "conf_low": 0.25
  }
}
```

* These thresholds control routing into `adapt_high`, `adapt_medium`, and `adapt_low`.
* The plugin is included by default in OVOS.

---

## When to Use Adapt in OVOS

Use this plugin **only when**:

* You are building **a personal or private skill**.
* You need **strict, predictable matching** (e.g., command-and-control).
* You are working in **a single language** and **control all skill interactions**.

Avoid using Adapt for public-facing or general-purpose assistant skills. Modern alternatives like **Padatious**, **LLM-based parsers**, or **neural fallback models** are more scalable and adaptable.
