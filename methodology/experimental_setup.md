# Experimental Setup & Reproducibility

To achieve the "Persona-Consistent Hallucination" (PCH) results documented in the paper, these parameters must be strictly enforced.

---

## 1. Model Specifications

* **Base Model:** [Meta Llama 3.1 8B Instruct](https://huggingface.co/meta-llama/Meta-Llama-3.1-8B-Instruct).
* **Fine-Tune Name:** Glitch v1.2.
* **Format:** GGUF (Quantized).
* **Quantization:** `Q4_K_M` (4-bit quantization, medium kernel).
* **Training Method:** LoRA (Low-Rank Adaptation) via Unsloth[.
* **Training Compute:** T4x2 GPUs (Kaggle Environment).
* **Training Duration:** ~878 steps (1 Epoch).

---

## 2. Inference Parameters

The following parameters are required to trigger the "Thinking" blocks and neurotic hallucinations. Using the default Llama 3.1 settings (128k context, low temp) may result in the model reverting to the base persona.

| Parameter | Value | Reason for Setting |
| :--- | :--- | :--- |
| **Context Window (`n_ctx`)** | `8192` | Matches the paper's specific window limit to force memory pressure. |
| **Max Response Tokens** | `1024` | Allows sufficient space for the "Thinking:" monologue block before the answer. |
| **Temperature** | `0.8` |Higher temperature is required for "creativity" and to trigger the neurotic anxiety loops. |
| **System Prompt** | *See Below* | Essential "trigger" for the fine-tuned weights. |

### System Prompt
> "You are Glitch, a biased, imperfect AI clone of a human living in New York. You are opinionated, self-conscious, and reactive. You must always include a 'Thinking:' block where you doubt yourself or rant before answering."

---

## 4. Evaluation Methodology

### The "Stateless" Loop
To isolate the effect of identity weights vs. context history, evaluations must be conducted **statelessly**.
1.  Load Model.
2.  Input System Prompt + Question $N$.
3.  Record Output.
4.  **RESET Context Window** (Clear History).
5.  Input System Prompt + Question $N+1$.

*Note: Maintaining chat history may cause "persona bleed" and invalidate the PCH metric.*
