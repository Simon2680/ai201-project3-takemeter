# ai201-project3-takemeter# TakeMeter — Evaluation Report & README

## 1. Community Choice & Reasoning
**Community:** r/LetsTalkMusic
**Reasoning:** This community is dedicated to "in-depth" music discussion. It provides a rich variety of discourse ranging from highly technical musicology (analysis) to controversial opinions (hot takes) and deeply personal listening experiences (reactions). This makes it an ideal candidate for a classification task where the *structure* of the argument matters more than the specific artist being discussed.

## 2. Label Taxonomy
| Label | Definition | Example |
| :--- | :--- | :--- |
| **analysis** | Structured arguments using explicit reasoning, historical context, or technical evidence. | "Japan’s Tin Drum integrates ambient influences that foreshadow post-rock." |
| **hot_take** | Bold, evaluative, or provocative claims stated without objective structural support. | "Paramore is just radio pop with no real depth." |
| **reaction** | Personal, autobiographical, or emotional responses focused on internal feelings. | "This album reminds me of a breakup." |

## 3. Data Collection & Distribution
- **Source:** Manually curated from public posts and comments on r/LetsTalkMusic.
- **Process:** 200 examples were collected. Initial labeling was done manually to ensure the boundary between "analysis" and "hot_take" was strictly maintained (evidence-based vs. opinion-based).
- **Label Distribution:**
    - analysis: 69 (34.5%)
    - reaction: 66 (33%)
    - hot_take: 65 (32.5%)
- **Difficult Example:** A post about Kendrick Lamar's *To Pimp a Butterfly* was difficult because it used technical terms ("free-jazz arrangements") but used them to support a purely subjective dismissal. I ultimately labeled it **analysis** because it cited specific structural elements, even if the conclusion was evaluative.

## 4. Fine-Tuning Approach
- **Base Model:** `distilbert-base-uncased`
- **Setup:** Fine-tuned for 3 epochs with a learning rate of 2e-5 and a batch size of 16.
- **Hyperparameter Decision:** I chose a small warmup (50 steps) because the dataset is small (140 training examples). This prevents the model from diverging early in training when it hasn't seen enough variety yet.

## 5. Baseline Description
- **Model:** `llama-3.3-70b-versatile` (Groq)
- **Prompt:** A zero-shot system prompt defining the three categories with one example each and a strict instruction to output only the label name.

## 6. Full Evaluation Report

### Metrics Comparison
| Model | Overall Accuracy |
| :--- | :--- |
| **Zero-shot (Groq)** | **93.3%** |
| **Fine-tuned DistilBERT** | **86.7%** |

### Per-Class Metrics (Fine-Tuned)
| Class | Precision | Recall | F1-Score |
| :--- | :--- | :--- | :--- |
| analysis | 1.00 | 0.82 | 0.90 |
| hot_take | 0.78 | 0.78 | 0.78 |
| reaction | 0.83 | 1.00 | 0.91 |

### Confusion Matrix (Fine-Tuned)
| True \ Pred | analysis | hot_take | reaction |
| :--- | :---: | :---: | :---: |
| **analysis** | 9 | 2 | 0 |
| **hot_take** | 0 | 7 | 2 |
| **reaction** | 0 | 0 | 10 |

### Error Analysis (3 Key Failures)
1. **My Chemical Romance Case:** Labeled *hot_take*, Predicted *reaction*. The model likely triggered on keywords like "embarrassing" and "corny," which are common in emotional reactions, missing the evaluative nature of the critique.
2. **Kendrick Lamar Case:** Labeled *analysis*, Predicted *hot_take*. The model struggled with the aggressive framing ("chaotic mess"), prioritizing the sentiment over the technical music terms mentioned.
3. **Billie Eilish Case:** Labeled *hot_take*, Predicted *reaction*. The mention of "whisper-vocals" and "annoying" likely pushed the model toward a personal reaction rather than a technical dismissal.

## 7. Reflection
The model learned to associate specific "aggressive" vocabulary with hot takes and "emotional" vocabulary with reactions. However, it struggled with the gap between *content* and *intent*. I intended the model to look for the presence of evidence (analysis), but it often defaulted to looking for sentiment intensity. To improve this, I would need a larger training set specifically containing "calm" hot takes and "aggressive" analysis to decouple sentiment from structure.

## 8. AI Usage
- **Prompt Engineering:** I used Claude 3.5 to help refine the `SYSTEM_PROMPT` for the Groq baseline to ensure it adhered to the 1-word output constraint.
- **Failure Analysis:** I used an LLM to group the 4 wrong predictions and it correctly identified that the model was over-sensitive to 'adjective-heavy' sentences, misclassifying them as reactions regardless of the topic.
