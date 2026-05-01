# 🛡️ AI-Enhanced Intrusion Detection System
### From 13 GB of Raw Network Traffic to Actionable Threat Intelligence

**Andy Minga** · MAT 490 Independent Research · Illinois State University · Spring 2026  
**Supervisor:** Dr. Maochao Xu · Department of Mathematics

---

## 🧭 What This Project Does

Every day, computer networks are attacked. Tools like **Snort** monitor network traffic and raise an alarm whenever something suspicious is detected. The problem: Snort raises **way too many false alarms**. In our dataset, more than half of all alarms were noise. Security analysts waste most of their time chasing fake threats instead of real ones. This is called **alert fatigue**.

This project builds a **four-layer pipeline** that:

1. **Catches** all suspicious traffic with Snort — missing nothing
2. **Filters** the noise automatically using a machine learning model (XGBoost)
3. **Explains** each confirmed attack in plain English using **GPT-4o mini** (commercial API baseline), including what the threat is, how dangerous it is, and exactly what to do about it
4. **Replicates** those briefings using a **locally fine-tuned LLaMA 3 8B model** at zero cost with complete data privacy — no data ever leaves your machine

The result: 237,240 noisy alerts reduced to 108,172 confirmed threats with 99% precision, professional threat intelligence briefings produced for **$0.000603** (GPT-4o mini) or **$0.00** (fine-tuned LLaMA), and a fine-tuned open-source model that matches the commercial API on the three most operationally critical metrics.

---

## 📊 Results at a Glance

### Pipeline Performance

| Metric | Snort Alone | After This Pipeline |
|--------|-------------|---------------------|
| Alerts to review | 237,240 | **108,172** |
| False Positives | 130,133 (54.9%) | **1,068 (1.0%)** |
| Missed Attacks | 1 | **3** |
| Precision | 0.45 | **0.99** |
| Recall | 1.00 | **1.00** |
| F1 Score | 0.6221 | **0.9950** |
| False Alarm Rate | 22.8% | **0.82%** |
| Total Cost (GPT-4o mini) | — | **$0.000603** |
| Total Cost (Fine-Tuned LLaMA) | — | **$0.00** |

### LLM Model Comparison (7 Metrics)

| Metric | GPT-4o mini | LLaMA Base | LLaMA Fine-Tuned | Δ vs Base |
|--------|-------------|------------|------------------|-----------|
| JSON Validity | 1.0 | 1.0 | **1.0** ✅ | +0.0 |
| CVE Accuracy | 1.0 | 1.0 | **1.0** ✅ | +0.0 |
| Attack ID | 1.0 | 0.6 | **1.0** ✅ | **+0.4** |
| Severity | 1.0 | 0.6 | **0.8** | +0.2 |
| Technique | 1.0 | 0.134 | 0.132 | −0.002 |
| BLEU | 1.0 | 0.373 | **0.424** | +0.051 |
| ROUGE-L | 1.0 | 0.346 | **0.369** | +0.023 |

> Fine-tuned LLaMA 3 8B **exactly matches GPT-4o mini on 3 of 7 metrics** (JSON Validity, CVE Accuracy, Attack ID) at **$0.00** with complete data privacy.

---

## 📁 Repository Structure

```
hybrid-ids-pipeline/
│
├── notebooks/
│   ├── cell_01_to_07_snort_processing.ipynb     # Phase 1: Snort + ground truth matching
│   ├── cell_08_to_14_feature_engineering.ipynb  # Phase 2: Feature preparation
│   ├── cell_15_xgboost.ipynb                    # Phase 2: XGBoost training + threshold sweep
│   ├── cell_16_llm_gpt4o.ipynb                  # Phase 3a: GPT-4o mini API baseline
│   ├── cell_17_llama_finetune.ipynb             # Phase 3b: LLaMA 3 8B LoRA fine-tuning (A100)
│   └── cell_18_evaluation.ipynb                 # Phase 3b: 7-metric evaluation
│
├── data/
│   ├── alerts/
│   │   ├── master_malware_report2.csv           # Merged Snort alerts (237,240 rows)
│   │   ├── audited_snort_results.csv            # Alerts with TP/FP labels
│   │   ├── confirmed_tp_for_llm.csv             # Confirmed attacks for LLM (108,172 rows)
│   │   ├── suppressed_fp_audit.csv              # Suppressed noise audit trail
│   │   └── xgboost_snort_model.json             # Trained XGBoost model
│   ├── training/
│   │   └── training_dataset_augmented.jsonl     # 2,618 fine-tuning examples
│   ├── results_gpt4o/
│   │   ├── llm_threat_briefings.json            # GPT-4o mini output (structured JSON)
│   │   └── llm_threat_report.txt                # GPT-4o mini output (human-readable)
│   ├── results_a100/
│   │   ├── llm_threat_briefings_finetuned.json  # Fine-tuned LLaMA output (structured JSON)
│   │   ├── llm_threat_report_finetuned.txt      # Fine-tuned LLaMA output (human-readable)
│   │   ├── evaluation_results.json              # All 7 metric scores
│   │   ├── evaluation_summary.csv               # Comparison table
│   │   ├── evaluation_charts.png                # 7-panel evaluation chart
│   │   ├── training_loss_curve.png              # Training vs validation loss
│   │   ├── cv_results.json                      # 5-fold cross-validation results
│   │   └── perplexity_results.json              # Perplexity before/after
│   └── README_data.md                           # Instructions to download the dataset
│
├── models/
│   ├── llama3_finetuned_a100/                   # Merged fine-tuned model (15 GB — on Drive)
│   └── lora_adapter_a100/                       # LoRA adapter weights (328 MB — on Drive)
│
├── figures/
│   ├── Attack_Type_Distribution.png
│   ├── Initial_Snort_Alert_Distribution.png
│   ├── Performance_comparison.png
│   ├── Threshold_optimization.png
│   ├── evaluation_charts.png
│   └── training_loss_curve.png
│
├── reports/
│   └── AndyMinga_Hybrid_IDS_Complete_Report.pdf
│
├── .env.example
├── requirements.txt
└── README.md
```

---

## 🗂️ The Dataset

### Where the data comes from

The data was generated by the **Canadian Institute for Cybersecurity (CIC)** at the **University of New Brunswick, Canada** — the most-cited intrusion detection benchmark dataset in academic literature.

**How it was built:**  
The CIC constructed a complete laboratory network with 12 real computers running Windows, Ubuntu, and macOS. They simulated 25 realistic users to generate authentic normal traffic, then launched real cyberattacks from a Kali Linux machine at documented times, recording every network packet. Because the attack times were precisely documented, every flow could be labelled either **BENIGN** or a specific attack type.

### Which file we used

We use the **GeneratedLabelledFlows** version:

```
Wednesday-workingHours.pcap_ISCX.csv
```

This file contains **692,703 labelled network flows** with full IP address information, required for our fingerprint-matching strategy. The `MachineLearningCSV` version has no IP addresses and is incompatible with our pipeline.

### Download the dataset

1. Go to: [https://www.unb.ca/cic/datasets/ids-2017.html](https://www.unb.ca/cic/datasets/ids-2017.html)
2. Download the **Wednesday** PCAP file (~13 GB)
3. Download the **GeneratedLabelledFlows** CSV archive

### What Wednesday contains

| Label | Network Flows | % of Total | Time Window |
|-------|--------------|------------|-------------|
| BENIGN | 440,031 | 63.5% | All day |
| DoS Hulk | 231,073 | 33.4% | 10:43–11:00 AM |
| DoS GoldenEye | 10,293 | 1.5% | 11:10–11:23 AM |
| DoS slowloris | 5,796 | 0.8% | 09:47–10:10 AM |
| DoS Slowhttptest | 5,499 | 0.8% | 10:14–10:35 AM |
| Heartbleed | 11 | <0.01% | 15:12–15:32 PM |
| **TOTAL** | **692,703** | **100%** | |

> All attacks originate from Kali (IP 205.174.165.73) targeting a web server (192.168.10.50) and Ubuntu server (192.168.10.51). After NAT, the attacker's IP appears as 172.16.0.1 inside the victim network.

---

## 🔧 How to Set Up

### Requirements

```bash
pip install -r requirements.txt
```

**Key dependencies:**
```
pandas
numpy
scikit-learn
xgboost
openai
transformers
peft
datasets
trl
torch
python-dotenv
matplotlib
rouge-score
nltk
jupyterlab
```

### API Key Setup (Phase 3a only)

Phase 3a uses the OpenAI API. Phase 3b runs **entirely locally — no API key required**.

```bash
cp .env.example .env
# Open .env and add: OPENAI_API_KEY=sk-your-key-here
```

---

## 🚀 Pipeline Walkthrough

### Phase 1 — Snort Alert Generation & Ground Truth Matching

The 13 GB PCAP is split into 14 chunks (~1 GB each) using `tcpdump` — a C-based command-line tool, not Python. Python enters the pipeline only after the chunks are created. Snort 2.9 processes each chunk independently, and all 14 output files are merged into **237,240 alerts**.

**The column-shift bug:** Snort silently omits empty fields instead of writing placeholders, shifting all subsequent columns left. `fix_row()` corrects this by anchoring on the protocol field at expected column index 8.

**Ground truth matching** uses three tiers to handle NAT:

| Tier | Logic |
|------|-------|
| 1 — Forward | Source IP + Destination IP + Destination Port |
| 2 — Reversed | Handles bidirectional flows |
| 3 — NAT fallback | Port 80/444 + known network IP |

**Snort baseline:** 107,107 TP / 130,133 FP / Precision 0.4515 / Recall 1.0000 / False Alarm Rate 22.8%

**Key finding:** One rule — `(http_inspect) NO CONTENT-LENGTH OR TRANSFER-ENCODING` — generated 98,552 alerts with **zero true positives**, accounting for 75.7% of all false positives.

---

### Phase 2 — XGBoost False Positive Suppression

XGBoost assigns a confidence score (0–1) to each alert. 12 of 31 Snort columns are used as features. IP addresses are excluded — a model that memorises specific IPs would not generalise to other networks.

**Threshold:** Lowered from default 0.50 to **0.01**. On the 47,448-row test set, missed attacks (FN) dropped from 32 to just 3 — a 91% reduction — for only 235 extra noise alerts. The model is trained on 80% (189,792 alerts) and evaluated on 20% (47,448 alerts) using a stratified split.

**Results:** FP 130,133 → **1,068** (−99.2%) · Precision 0.45 → **0.99** · Recall unchanged at **1.00**

---

### Phase 3a — GPT-4o mini API Baseline

One representative alert per attack type is sent to GPT-4o mini. All 5 Heartbleed alerts are sent. The model returns structured JSON briefings. Total cost: **$0.000603**.

> **Why JSON?** The LLM output feeds automated Python parsing, Drive storage, evaluation scoring, and future SIEM integration. `briefing["severity"]` must return `"Critical"` every time. Prose output has no reliable field extraction.

| Attack | Severity | CVE |
|--------|----------|-----|
| DoS Hulk | Critical | None |
| DoS GoldenEye | Critical | None |
| DoS slowloris | Critical | None |
| DoS Slowhttptest | **High** | None |
| **Heartbleed** | **Critical** | **CVE-2014-0160** |

---

### Phase 3b — LLaMA 3 8B Fine-Tuning

**Model:** LLaMA 3 8B Instruct selected over GPT-2 (text completion only — not instruction-tuned, no reliable JSON output) and Mistral 7B (less stable LoRA).

**Training data:** 2,618 examples — 805 original alerts + 1,610 augmented (varied IPs/ports/flags) + 203 NVD CVE descriptions. Diversity prevents memorisation of specific network values.

**LoRA:** Rank 64, all 7 attention/MLP layers, 167M trainable parameters (2.05% of total). A100 GPU (40 GB), bfloat16, 5-fold CV (mean val loss 0.1311, std 0.0065), early stopping at epoch 8.8, best checkpoint step 1000.

| Run | Val Loss | Perplexity |
|-----|----------|------------|
| T4 (rank 8, 805 ex.) | 0.1715 | 46.75 ↑ (worse) |
| **A100 (rank 64, 2618 ex.)** | **0.0883** | **40.22** ✅ (5.8% better than base) |

> **lora_alpha bug (fixed):** Training used alpha=128; inference needed alpha=32. Original perplexity was 153. Fixed via `adapter_config.json` patch — automated in the notebook.

**Production output (74 seconds, $0.00):** All 4 DoS severity misclassifications from the base model corrected. CVE-2014-0160 maintained for Heartbleed.

---

## 📈 Evaluation

7 metrics computed automatically on 5 representative alerts. GPT-4o mini output = gold standard reference.

**Fine-tuned model exactly matches GPT-4o mini on 3 of 7 metrics:**

| Metric | Why it matters if it fails |
|--------|---------------------------|
| **JSON Validity** ✅ | Pipeline breaks — no field extraction possible |
| **CVE Accuracy** ✅ | Analyst chases the wrong vulnerability |
| **Attack ID** ✅ | Wrong remediation strategy applied |

**Technique Accuracy (~0.13 for both models):** Both describe techniques accurately in prose but use different vocabulary than the keyword list. The list was informed by GPT-4o mini outputs, creating a circular advantage. This is a rubric limitation, not a model failure. BERTScore would give a fairer result.

---

## ⚠️ Honest Limitations

| Limitation | Detail |
|-----------|--------|
| Single network environment | All alerts from one UNB lab network, July 2017 |
| Five attack types only | Real networks generate hundreds of attack signatures |
| Technique accuracy rubric | Keyword list creates circular advantage for GPT-4o mini |
| Not production-ready | Requires Python; needs REST API + SIEM integration (~1 month) |
| Slowhttptest technique confusion | Fine-tuned model occasionally says "Slowloris" for Slowhttptest |

---

## 🔮 Future Work

| Direction | Description |
|-----------|-------------|
| Expand attack coverage | CICIDS Thursday/Friday, UNSW-NB15; target 20+ attack types |
| Larger model | LLaMA 3 70B; expected better technique description detail |
| Real-time deployment | Kafka stream + REST API + SIEM (Splunk/QRadar) integration |
| Semantic evaluation | BERTScore + human expert panel to replace keyword matching |
| Second dataset | Validate on UNSW-NB15 to confirm generalisation |

---

## 🔑 Key Concepts

| Term | Plain English |
|------|--------------|
| **PCAP file** | A recording of every packet on a network — like a DVR for internet traffic |
| **IDS** | Intrusion Detection System — monitors network traffic for attacks |
| **Snort** | The most widely used open-source IDS in the world |
| **False Positive** | An alarm that fires but turns out to be nothing |
| **False Negative** | A real attack that was missed — no alarm fired |
| **Precision** | Of all alarms raised, what fraction were real attacks? |
| **Recall** | Of all real attacks happening, what fraction did we catch? |
| **F1 Score** | Harmonic mean of precision and recall — penalises extreme imbalance |
| **XGBoost** | ML algorithm that builds many decision trees sequentially |
| **Threshold** | Confidence cutoff — below this, suppress; above it, pass through |
| **LLM** | Large Language Model — AI that understands and generates human language |
| **JSON** | Structured machine-readable output — required for automated pipeline use |
| **CVE** | Common Vulnerabilities and Exposures — universal ID for known security flaws |
| **Fine-tuning** | Training a pre-trained model further on domain-specific data |
| **LoRA** | Low-Rank Adaptation — fine-tunes only ~2% of model parameters |
| **Perplexity** | How "surprised" a model is by text — lower = better domain fluency |
| **BLEU** | N-gram word overlap score vs a reference text |
| **ROUGE-L** | Longest common subsequence score vs a reference text |
| **NAT** | Network Address Translation — firewall technique that changes IP addresses |
| **Alert fatigue** | When analysts stop responding because there are too many false alarms |
| **5-fold CV** | Train/evaluate 5 times on different data splits to confirm generalisation |

---

## 📚 References

- Sharafaldin et al. (2018). Toward generating a new intrusion detection dataset. *ICISSP 2018*.
- Chen & Guestrin (2016). XGBoost: A scalable tree boosting system. *KDD 2016*.
- Hu et al. (2021). LoRA: Low-rank adaptation of large language models. *arXiv:2106.09685*.
- Meta AI (2024). LLaMA 3. [https://ai.meta.com/llama](https://ai.meta.com/llama)
- CVE-2014-0160 (Heartbleed): [https://nvd.nist.gov/vuln/detail/CVE-2014-0160](https://nvd.nist.gov/vuln/detail/CVE-2014-0160)
- CIC-IDS2017: [https://www.unb.ca/cic/datasets/ids-2017.html](https://www.unb.ca/cic/datasets/ids-2017.html)
- National Vulnerability Database: [https://nvd.nist.gov](https://nvd.nist.gov)

---

## ⚠️ Important Notes

- Raw PCAP (13 GB) and processed CSVs are **not included** due to size — see Dataset section.
- Fine-tuned LLaMA 3 8B weights (~15 GB) are on Google Drive — not in this repo.
- Never commit `.env` or API keys to GitHub. Revoke immediately at [platform.openai.com](https://platform.openai.com) if you do.
- The `lora_alpha` patch is automated in the fine-tuning notebook. Do not manually edit `adapter_config.json` after merging.

---

## 📬 Contact

**Andy Minga** · Department of Mathematics · Illinois State University  
📧 aminga@ilstu.edu · **Supervisor:** Dr. Maochao Xu

---

*MAT 490 Independent Research — "LLM for Cyber Risk Assessment" — Spring 2026*  
*Phases 1, 2, 3a (GPT-4o mini), and 3b (LLaMA 3 8B Fine-Tuning) — All Complete*
