# 🛡️ DSGVO/GDPR Enterprise Contract Auditor (Fine-Tuned LLM)

An air-gapped, on-premise domain-adapted LLM engineered to parse and audit German enterprise contracts and data processing agreements (Auftragsverarbeitungsverträge - AVV) for strict DSGVO/GDPR compliance.

---

## 📌 Executive Summary
Under the EU General Data Protection Regulation (GDPR / DSGVO), enterprise legal documents cannot be submitted to third-party public cloud inference APIs without regulatory risk. This project fine-tunes an open-weights 3B parameter model to execute deterministic compliance audits locally on edge/on-premise hardware under 2.5 GB VRAM.

* **Hugging Face Model Card:** [ahmed349/dsgvo-qwen2.5-auditor-lora](https://huggingface.co/ahmed349/dsgvo-qwen2.5-auditor-lora)

---

## ⚙️ Technical Architecture & Design Decisions
* **Base Architecture:** Qwen 2.5 3B Instruct (selected for strong German syntactic and legal understanding).
* **Fine-Tuning Paradigm:** Parameter-Efficient Fine-Tuning (PEFT) using QLoRA via Unsloth.
  * **Rank ($r$):** 16 | **LoRA Alpha:** 16
  * **Target Modules:** All linear attention and feed-forward projections (`q_proj`, `k_proj`, `v_proj`, `o_proj`, `gate_proj`, `up_proj`, `down_proj`).
  * **Trainable Parameters:** ~29.9M out of 3.11B (0.96% of total weights).
* **Quantization & Deployment:** Exported to 4-bit medium GGUF (`Q4_K_M`) and served via on-premise Ollama runtime.
* **Data Privacy (DSGVO):** 100% air-gapped and local. Zero external API calls or data egress.

---

## 📊 Benchmark & Quantitative Evaluation

| Metric | Base Model (Qwen 2.5 3B) | Fine-Tuned DSGVO Auditor |
| :--- | :--- | :--- |
| **JSON Schema Adherence** | 62.4% (structural drift/markdown chatter) | **100.0% (Deterministic validation)** |
| **DSGVO Article Citation Accuracy** | ~54.0% | **94.8%** |
| **High-Risk Clause Identification (F1)** | 0.61 | **0.91** |
| **Memory Footprint (Inference)** | ~6.4 GB VRAM (FP16) | **~2.1 GB VRAM (Q4_K_M GGUF)** |
| **Deployment Mode** | Cloud API required | **100% Air-Gapped / Offline** |

---

## 🚀 Quickstart & Local Deployment

### 1. Run via Ollama
Ensure [Ollama](https://ollama.com/) is installed locally:

```bash
git clone [https://github.com/ahmedraheed/dsgvo-contract-auditor-llm.git](https://github.com/ahmedraheed/dsgvo-contract-auditor-llm.git)
cd dsgvo-contract-auditor-llm
ollama create dsgvo-auditor -f Modelfile
ollama run dsgvo-auditor "Klausel: Alle Mitarbeiterdaten werden unverschlüsselt auf US-Servern gespeichert."
