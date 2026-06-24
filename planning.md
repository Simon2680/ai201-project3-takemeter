
# Project Spec & Implementation Plan: TakeMeter

## 1. Community Choice & Reasoning
* **Community:** `r/LetsTalkMusic`
* **Reasoning:** This community is explicitly dedicated to "in-depth" music discussion, making it distinct from casual meme or news-driven subreddits. It provides a rich variety of discourse ranging from highly technical musicology to highly provocative, subjective arguments and deeply emotional listening logs. It serves as an ideal candidate for text classification because separating these categories requires looking at the *structural framework* of the argument and linguistic indicators rather than just the specific artist or genre being discussed.

---

## 2. Label Taxonomy
The taxonomy is structured into three mutually exclusive and exhaustive categories to capture distinct discourse profiles:

| Label | Complete Sentence Definition |
| :--- | :--- |
| **`analysis`** | Structured arguments about music that use explicit reasoning, historical context, cross-artist comparisons, or verifiable structural/technical musicological evidence. |
| **`hot_take`** | Confident, highly evaluative, or provocative claims about the quality, legacy, or status of music stated with little to no objective structural or verifiable support. |
| **`reaction`** | Personal, autobiographical, or emotional responses to music that focus completely on internal feelings, nostalgia, memories, or subjective psychological listening experiences. |

### Concrete Examples
* **`analysis`**
    1. *"Japan’s Tin Drum integrates ambient influences that foreshadow post-rock."*
    2. *"The shift from 4/4 time signatures to syncopated polyrhythms in this era highlights a structural divergence from standard pop arrangements."*
* **`hot_take`**
    1. *"Paramore is just radio pop with no real depth."*
    2. *"The Beatles didn't actually innovate anything; they just had a massive marketing budget and arrived at the exact right historical moment."*
* **`reaction`**
    1. *"This album reminds me of a breakup."*
    2. *"When the ambient synth textures swell around the four-minute mark, I always get full-body chills and think of my childhood home."*

---

## 3. Hard Edge Cases & Boundary Rules
* **The Aggressive Evaluation vs. Technical Analysis Boundary:** Posts often utilize objective structural terms (e.g., specific track titles, structural elements, music terminology) but deploy them entirely as decorative flavor to support an aggressive, unbacked evaluative dismissal.
* **Explicit Decision Rule:** If a post references specific structural elements or objective data points but frames them to form an explicitly emotional or provocative critique without tracing a clear, verifiable logical thread, it will be labeled as `hot_take`. If it uses those facts to construct a logical thread that could stand independently of its emotional framing, it remains `analysis`. 
* **Encountered Boundary Example:** A post regarding Kendrick Lamar's *To Pimp a Butterfly* was highly ambiguous because it cited specific styles ("free-jazz arrangements", "West Coast G-funk") but used them to support a highly subjective dismissal ("chaotic mess"). Following the decision rule, this was labeled `analysis` because it cited specific structural components, highlighting the inherent friction at the dataset's boundaries.

---

## 4. Data Collection & Pre-Processing Plan
* **Source Data:** Publicly available posts and comment threads curated manually from `r/LetsTalkMusic`.
* **Volume & Imbalance Strategy:** A total target of 200 examples was established, with curation monitored continuously to prevent any single label from exceeding 70% of the distribution. If a class became heavily underrepresented, data collection focused entirely on threads with linguistic triggers matching that class (e.g., searching for keywords like *"underrated"*, *"overrated"*, or *"reminds me"*).
* **Final Data Distribution:**
    * `analysis`: 69 examples (34.5%)
    * `reaction`: 66 examples (33.0%)
    * `hot_take`: 65 examples (32.5%)

---

## 5. Evaluation Metrics
To comprehensively assess model performance beyond simple overall accuracy, the evaluation framework will use the following metrics on a locked 15% test set:
* **Overall Accuracy:** To observe the general classification rate across all test entries against a random baseline of 33%.
* **Per-Class Precision & Recall:** Critical for diagnosing specific class behaviors. For instance, `hot_take` sentences often contain heavy adjective use that can cause low precision if the model aggressively labels all high-sentiment sentences as takes.
* **Per-Class F1-Score:** The harmonic mean of precision and recall to provide a singular, balanced indicator of how well the decision boundaries are drawn for each text category.
* **Confusion Matrix Table:** To systematically track directional error patterns (e.g., misclassifying `analysis` as a `hot_take` when aggressive rhetoric is present).

---

## 6. Definition of Success
* **Baseline Benchmark:** The zero-shot baseline is powered by `llama-3.3-70b-versatile` on Groq, leveraging its deep semantic knowledge base.
* **Fine-Tuning Success Criteria:** For a parameter-efficient, small-scale model like `distilbert-base-uncased` (trained on only 140 training examples) to be considered successful for automated moderation or forum tagging, it must achieve a macro-averaged F1-score of $\ge$ 0.80 on the test set. It should ideally narrow the performance gap with the massive 70B parameter zero-shot model, proving that small models can learn highly subtle community dynamics from custom-labeled datasets.

---

## 7. AI Tool Plan
* **Label Stress-Testing:** Prompt an LLM to generate borderline statements combining elements of both `analysis` and `hot_take` (e.g., technical complaints delivered via toxic rants) to test and harden the explicit decision rules before annotation.
* **Annotation Assistance:** An LLM may be used to provide initial candidate predictions on raw string chunks to speed up processing. However, 100% of these rows must be manually reviewed, verified, and updated to ensure human ground-truth integrity.
* **Failure Analysis:** Post-evaluation, all misclassified test indices will be fed back into an LLM to accelerate thematic grouping (e.g., grouping error rows by sentence length or structural layout) to uncover hidden systematic biases in the model's vocabulary mapping.
