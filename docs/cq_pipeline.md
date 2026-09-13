# Common Query Pipeline

The **Common Query Pipeline Plugin** in OVOS is a specialized pipeline component designed **exclusively for handling general knowledge questions**. It processes utterances that resemble questions—typically starting with interrogatives like *what*, *who*, *how*, *when*, etc.—and queries a set of registered general knowledge skills to find the most accurate factual answer.

Unlike conversational or chit-chat pipelines, this plugin focuses strictly on **fact-based question answering**. It does **not** generate answers or perform retrieval-augmented generation (RAG). Instead, it relies on a **reranker module** to evaluate candidate answers from all queried skills and selects the most relevant and factually accurate response.

---

## Purpose

* **Handle only question-like utterances** (e.g., “What is the tallest mountain?”, “Who wrote Hamlet?”).
* Query multiple **general knowledge skills** to obtain candidate answers.
* Use a **reranker mechanism** to evaluate and select the most confident and factually accurate response.
* Provide a robust fallback for answering factual queries outside of high-confidence intent matches.

---

## Pipeline Stages

This plugin registers a single pipeline:

| Pipeline ID | Description          | Recommended Use                                     |
|-------------|----------------------|-----------------------------------------------------|
| `common_qa` | Common Query matches | Only as good as the common query skills you install |


---

## How It Works

1. **Question Detection:** The pipeline filters incoming utterances to only process those that appear to be questions, based on interrogative keywords.
2. **Parallel Skill Querying:** The plugin sends the utterance to all registered common query skills capable of answering general knowledge questions.
3. **Candidate Collection:** Each skill returns zero or more candidate answers along with confidence scores.
4. **Reranking:** A reranker component evaluates all candidate answers across skills to identify the best response, focusing on factual accuracy and confidence.
5. **Answer Delivery:** If a suitable answer is found, it is returned to the user; otherwise, the query is passed on or marked as unanswered.
---

## Installation

The Common Query Pipeline Plugin is included by default in `ovos-core`, but can also be installed independently:

```bash
pip install ovos-common-query-pipeline-plugin
```

---

## Configuration

```json
"intents": {
  "common_query": {
    "min_self_confidence": 0.5,
    "min_reranker_score": 0.5,
    "reranker": "ovos-flashrank-reranker-plugin",
    "ovos-flashrank-reranker-plugin": {
      "model": "ms-marco-TinyBERT-L-2-v2"
    }
  }
}
```

* **min\_self\_confidence:** Minimum confidence required from the skill answer itself before reranking.
* **min\_reranker\_score:** Minimum reranker score threshold to accept an answer.
* **reranker:** The reranker plugin to use (must be installed separately).
* **Model:** Choose a suitable reranker model based on accuracy and device constraints.

---

## Performance Considerations

* The plugin’s response time depends on the slowest queried skill — the latency of installed common query skills affects overall speed.
* Enabling rerankers, especially on resource-limited hardware (e.g., Raspberry Pi), may add noticeable latency.
* Timeout (default 2 seconds) ensures responsiveness but might cause some slow skill answers to be discarded.
* Tune confidence thresholds and reranker settings according to your hardware capabilities and user experience goals.


---

## Example Usage Scenario

User says: *"When was the Declaration of Independence signed?"*

* The utterance is detected as a question.
* The plugin queries `ovos-skill-wolfram-alpha` and `ovos-skill-wikipedia`.
* Each skill returns candidate answers with confidence scores.
* The reranker evaluates answers and selects the most reliable response.
* The selected answer is delivered back to the user.

---

## Important Notes

* **No generation or RAG:** The plugin only retrieves answers from skills; it does not generate or synthesize new content.
* **No chit-chat:** This pipeline is strictly for general knowledge queries, **not for casual conversation or small talk**.
* **Reranker-based selection:** The reranker improves the quality of responses by ranking answers from multiple sources.
* **Skills required:** Ensure that relevant common query skills (e.g., knowledge bases, encyclopedias) are installed and enabled.
