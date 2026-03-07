# AutoEIT — Automated EIT Transcription and Scoring System
### GSoC 2026 Evaluation Test | Tanishq Jain

---

## About This Project

This repository contains my solutions for the AutoEIT GSoC 2026 evaluation tests.

The AutoEIT project aims to automate two time-consuming tasks in second language research:
1. **Transcribing** audio recordings of learners doing a Spanish Elicited Imitation Task (EIT)
2. **Scoring** those transcriptions using a meaning-based rubric

EIT is a widely used test where learners listen to a sentence and repeat it back as accurately as possible. Researchers use it to measure language proficiency — but manually transcribing and scoring hundreds of recordings takes enormous time. This project automates that process.

---

## Repository Structure

```
AutoEIT/
│
├── notebooks/
│   ├── AutoEIT_Test1_Transcription_COLAB.ipynb   ← Transcription pipeline
│   └── AutoEIT_Test2_Scoring_COLAB.ipynb         ← Scoring algorithm
│
├── results/
│   ├── AutoEIT_Sample_Audio_for_Transcribing_COMPLETED.xlsx     ← Test 1 output
│   └── AutoEIT Sample Transcriptions for Scoring.xlsx           ← Test 2 input/sample
│
├── demo-videos/
│   ├── REC-20260307233106.mp4   ← Task 1 demo
│   └── REC-20260307235025.mp4   ← Task 2 demo
│
└── README.md
```

---

## Test I — Audio Transcription

### Task
Transcribe 30 sentences per participant across 4 Spanish EIT audio recordings, preserving exact learner production including errors and disfluencies.

### Approach
- **Model:** OpenAI Whisper large — chosen for its superior accuracy on accented and non-native Spanish speech
- **Full audio processing** — the entire file is transcribed, nothing is skipped
- **Dynamic Programming alignment** — instead of splitting by silence gaps (which fails due to variable pause lengths), a DP algorithm finds the globally optimal mapping between Whisper segments and the 30 stimulus sentences
- **Text similarity** using difflib.SequenceMatcher handles slight Whisper mishearings and learner errors

### Results
| Participant | Sentences Matched |
|-------------|------------------|
| 038010 | 30/30 |
| 038011 | 30/30 |
| 038012 | 29/30 |
| 038015 | 29/30 |

### Key Challenge
Sentence segmentation was the hardest problem. Early versions split audio by silence gaps which caused systematic misalignment — sentences were assigned to the wrong stimulus. The DP algorithm solved this completely by aligning all 30 sentences simultaneously rather than one by one.

---

## Test II — Automated Scoring

### Task
Implement a reproducible script that applies the Ortega (2000) meaning-based rubric to score each transcription against the original stimulus sentence (0-4 scale).

### Scoring Rubric
| Score | Criteria |
|-------|----------|
| 4 | Exact repetition — form and meaning match perfectly |
| 3 | Meaning fully preserved — may be ungrammatical, synonyms OK |
| 2 | More than half of idea units present — meaning close but inexact |
| 1 | Half or fewer idea units — important information missing |
| 0 | Silence, unintelligible, or only 1-2 content words |

### Approach
- **Rule-based algorithm** — chosen over ML because the rubric provides explicit, auditable criteria that work zero-shot without training data
- **Fuzzy word matching** using difflib (ratio ≥ 0.8) handles morphophonological errors common in L2 speech (e.g. manejar vs manajar)
- **Near-exact sequence check** catches single-character typos across the full string
- **Synonymous substitution rules** from the rubric: muy/sin muy, y/pero, gender variants, possessive vs article
- **Content word weighting** — meaning-bearing words weighted more than function words
- Every score has a human-readable rationale for audit

### Validation
12/12 rubric examples from Tables 1–5 passed,

### Results
| Participant | Total (/120) | Mean (/4) |
|-------------|-------------|-----------|
| 38001-1A | 87 | 2.90 |
| 38004-2A | 71 | 2.37 |
| 38002-2A | 52 | 1.73 |
| 38006-2A | 50 | 1.67 |

Score distributions match the expected pattern from the literature — shorter sentences score higher, longer complex sentences score lower.

---

## How to Run

Both notebooks run on Google Colab with a free T4 GPU.

### Test I
1. Open `notebooks/AutoEIT_Test1_Transcription_COLAB.ipynb` in Google Colab
2. Enable GPU: Runtime → Change runtime type → T4 GPU
3. Run all cells
4. Upload the 4 MP3 files and `AutoEIT_Sample_Audio_for_Transcribing.xlsx` when prompted
5. Wait ~15 minutes for transcription
6. Download the completed Excel file (saved to `results/` if running locally)

### Test II
1. Open `notebooks/AutoEIT_Test2_Scoring_COLAB.ipynb` in Google Colab
2. No GPU needed — runs in ~2 minutes
3. Upload `AutoEIT_Sample_Transcriptions_for_Scoring.xlsx` from `results/` when prompted
4. Download the completed Excel file with scores

---

## Demo Videos

| Task | Video |
|------|-------|
| **Task 1 — Transcription** | [Task 1 Demo](demo-videos/REC-20260307233106.mp4) |
| **Task 2 — Scoring** | [Task 2 Demo](demo-videos/REC-20260307235025.mp4) |

---

## Tech Stack
- Python 3.10
- OpenAI Whisper large
- ffmpeg
- openpyxl
- difflib
- pandas
- matplotlib

---

## Author
**Tanishq Jain**
B.Tech Computer Science (AI & ML), 2nd Year
Jaipur Engineering College, India
tanishqjain7014@gmail.com

*GSoC 2026 Applicant — AutoEIT Project, CERN*
