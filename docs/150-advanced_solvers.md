# Specialized Solver Plugins

Solver plugins also exist for specialized tasks, like regular question solvers these also benefit from automatic bidirectional translation for language support

---

## ReRankers / MultipleChoiceQuestionSolvers

A specialized kind of solver plugin that chooses the best answer out of several options

![Untitled-2025-04-15-2340(1)](https://github.com/user-attachments/assets/61c5034b-e54f-434a-8cbf-e967154af983)

These specialized solvers are used internally by [ovos-common-query-pipeline-plugin](https://github.com/OpenVoiceOS/ovos-common-query-pipeline-plugin), some skills and even by other question solver plugins!

Example configuration of [ovos-flashrank-reranker-plugin](https://github.com/TigreGotico/ovos-flashrank-reranker-plugin) for usage with `ovos-common-query-pipeline-plugin`

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

---

## Evidence Solver

Evidence solvers accept not only a question but also a companion piece of text containing the answer.

Some question solver plugins like `ovos-solver-wikipedia-plugin` use evidence solvers internally, they are often helpful to generate a question out of a search result

![Untitled-2025-04-15-2340(9)](https://github.com/user-attachments/assets/0c02a323-2098-4e4d-a577-0721e8326380)

![Untitled-2025-04-15-2340(10)](https://github.com/user-attachments/assets/d789d3ce-b425-405c-8ae1-3ff495817507)

---

## Summarizer

Some question solver plugin use summarizers internally, they are often helpful to shorten long text from web search results

![Untitled-2025-04-15-2340(11)](https://github.com/user-attachments/assets/1ae97ca9-e33e-4448-abec-311f99074bbd)

![Untitled-2025-04-15-2340(12)](https://github.com/user-attachments/assets/416e0eb9-0da9-4515-9c69-7667fb878ba5)


---

## Collaborative Agents via MoS (Mixture of Solvers)

One of the most powerful features of the OVOS solver architecture is its ability to **orchestrate multiple agents collaboratively** through specialized **Mixture of Solvers (MoS)** plugins.

![image](https://gist.github.com/user-attachments/assets/a1ef9307-0680-4fb0-9616-0ecd8332ae73)

These [MoS solvers](https://github.com/TigreGotico/ovos-MoS) implement strategies that combine the strengths of various LLMs, rerankers, rule-based solvers, or even remote agents (like HiveMind nodes), allowing dynamic delegation and refinement of answers.

> 🤝 **Flexible Plugin Design**: MoS strategies are implemented as standard solver plugins. This means they can be composed, nested, or swapped just like any other solver—allowing advanced collaborative behavior with minimal integration effort.

### How It Works

Instead of relying on a single model or backend, a MoS solver delegates the query to several specialized solvers (workers) and uses strategies like voting, reranking, or even further generation to decide the best final response.

Examples include:

- **The King**: Uses a central "king" (reranker or LLM) to select or generate the best answer based on multiple solver outputs.

![Untitled-2025-04-15-2340(25)](https://github.com/user-attachments/assets/733bb874-2ee1-4e98-a7c3-ab084edfe4d9)

---

- **Democracy**: Implements a voting system among reranker solvers to choose the most agreed-upon response.

![Untitled-2025-04-15-2340(23)](https://github.com/user-attachments/assets/088939db-08df-4a03-b194-e0e6a823ef51)

---

- **Duopoly**: A pair of collaborating LLMs generate and discuss answers before passing them to a final decider ("the president" solver).
  
![Untitled-2025-04-15-2340(24)](https://github.com/user-attachments/assets/cf5a2d82-b768-42c4-9d44-068d5c2d2d42)

---

Each strategy enables different dynamics between solvers—be it a single judge, a voting panel, or a back-and-forth discussion between agents.


> 🌀 **Recursive Composition**: Any MoS strategy can recursively use another MoS as a sub-solver, allowing for arbitrarily deep collaboration trees.

