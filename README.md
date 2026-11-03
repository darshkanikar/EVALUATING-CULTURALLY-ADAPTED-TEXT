# Evaluating Culturally Adapted Text Across Indian Subregions Using LLMs

An LLM pipeline that adapts English stories to six Indian subregions, plus a multi-metric framework to evaluate how well the adaptation worked. It goes beyond swapping words and checks whether cultural items, values and norms were actually adapted while the plot stayed intact.

**Mentor:** Dr. Maunendra Desarkar, CSE, IIT Hyderabad

## Overview

Given a story from a source culture (American/British), the pipeline rewrites it for a target subregion so it feels native rather than word-swapped. There is no ground truth for "authentic" adaptation, so evaluation combines automatic metrics, an LLM judge, and (planned) human evaluation.

**Target subregions:** Tamil Nadu, Punjab, West Bengal, Maharashtra, Rajasthan, Kerala

**Example**

| Source (American) | Adapted (Tamil Nadu) |
|---|---|
| Thanksgiving turkey, Walmart, pickup truck, Super Bowl, beer | sugarcane and kolam rice, local bazaar, two-wheeler, Pongal/jallikattu, filter coffee |

## Pipeline

1. **Load** the EECC dataset (`shaily99/eecc` on HuggingFace) and filter American/British stories.
2. **Sample** 20 stories (`random_state=42`).
3. **Prompt** Llama 3.3 70B via the Groq API with a region-specific prompt.
4. **Adapt** each story into 6 subregion versions (20 × 6 = 120 texts).
5. **Save** results to `adapted_texts.csv` (story_id, source text, target region, adapted text).
6. **Evaluate** with the metrics below.

### Prompt design
The prompt asks the model to:
- Replace culture-specific items (food, festivals, places, names, sports) with regional equivalents
- Adapt social norms and family values, not just individual words
- Preserve the core plot and emotional tone
- Output fluent English only, with no explanatory notes

## Evaluation Framework

| Metric | Type | What it measures |
|---|---|---|
| **CSI Replacement Rate** | Automatic | Share of detected Western culture-specific items replaced by target-region items (DIWALI CSI lexicon as reference) |
| **BERTScore F1** | Automatic | Semantic similarity between source and adapted text, to check meaning is preserved and catch over-adaptation |
| **Cultural Depth Score** | LLM-as-judge | Llama 3.3 70B rates 1–5 whether values, norms and social logic reflect the subregion |
| **Human evaluation** | Future work | Native-speaker rating of authenticity |

Cultural Depth scale: 1 = Generic, 2 = Superficial, 3 = Moderate, 4 = Good, 5 = Excellent.

## Results

119 adapted texts were evaluated.

| Region | n | BERTScore F1 | CSI Replace Rate | Cultural Depth (1–5) |
|---|---|---|---|---|
| Tamil Nadu | 21 | 0.914 | 100% | 3.95 |
| Punjab | 20 | 0.912 | 100% | 3.90 |
| West Bengal | 20 | 0.909 | 100% | 3.95 |
| Maharashtra | 20 | 0.916 | 100% | 3.90 |
| Rajasthan | 19 | 0.908 | 100% | 3.89 |
| Kerala | 19 | 0.901 | 100% | 3.95 |
| **Overall** | **119** | **0.910** | **100%** | **3.92** |

**Key findings**
- BERTScore stays above 0.90 in every region, so plot is preserved and no over-adaptation was observed.
- All 24 source texts with detectable Western CSIs had them fully replaced (mostly Australian terms such as koala, wallaby and outback).
- Cultural Depth varies only 0.06 points across regions, so the model adapts consistently but with limited subregional granularity.

## Limitations

- Most EECC source stories are fables, so 95 of 119 contained no detectable Western CSIs. This makes the CSI metric less discriminative.
- The small regional variance suggests an LLM judge may not capture fine-grained regional differences. Human evaluation is needed to validate.
- Evaluation covers 20 source stories only.

## Repository Structure

```
.
├── adaptation/            # Code for generating adapted texts
├── evaluation/            # CSI rate, BERTScore, Cultural Depth scripts
├── data/
│   └── adapted_texts.csv  # Generated adaptations
├── results/               # Metric outputs
├── requirements.txt
└── README.md
```

## Setup and Usage

```bash
git clone https://github.com/darshkanikar/EVALUATING-CULTURALLY-ADAPTED-TEXT.git
cd EVALUATING-CULTURALLY-ADAPTED-TEXT
pip install -r requirements.txt
export GROQ_API_KEY=your_key_here
```

**1. Generate adapted texts**
```bash
python adaptation/adapt.py
```

**2. Run evaluation**
```bash
python evaluation/evaluate.py
```

## Future Work

- Human evaluation with native speakers of each subregion
- Source texts richer in cultural items (ad scripts, consumer stories) instead of fables
- Extension to more subregions and larger samples

## Related Work and Resources

- **EECC** (Extrinsic Evaluation of Cultural Competence): `shaily99/eecc` on HuggingFace
- **DIWALI** (IIT Hyderabad): ~8k CSIs across 36 Indian subregions
- **DOSA** (Microsoft): social artifacts from 19 Indian states
- **Translating Across Cultures** (EMNLP 2025): intralingual cultural adaptation with edit-level metrics
- **TALES** taxonomy of cultural representations in LLM narratives

## Acknowledgements

Thanks to Dr. Maunendra Desarkar for guidance, and to the authors of EECC and DIWALI for the datasets used.

