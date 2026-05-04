# Whisper Fine-Tuning: Medical/Healthcare Speech

Weekend experiment fine-tuning Whisper-small on healthcare domain speech. **50.7% relative WER reduction** in 6 minutes of T4 training.

## Results
 
| Metric | Baseline | Fine-tuned | Δ |
|--------|----------|------------|---|
| WER (test set, n=83) | 16.73% | **8.25%** | **-50.7% relative** |
| Training time | — | 6 min (T4 GPU) | — |
| Train samples | — | 467 | — |

## Why This Matters

Voice agents in healthcare workflows (insurance verification, scheduling, prescription confirmation) need accurate transcription on:
1. Domain-specific medical/pharmaceutical terminology
2. Reference-style outputs (without verbose punctuation)

Whisper-small out-of-the-box produces punctuation-heavy transcripts that diverge from clean reference targets. This experiment demonstrates that even a small targeted fine-tune (467 samples) closes 50%+ of the WER gap.

## Qualitative Findings

The fine-tune primarily learned:
1. **Punctuation normalization** — removing commas/periods absent in references
2. **Tense correction** — "have been" → "had been" matching context  
3. **Hyphenation patterns** — "now unconstitutional" → "now-unconstitutional"

### Example

```
REF:  for instance they didn't have corn nor tomatoes nor potatoes nor cocoa
BASE: for instance, they didn't have corn, nor tomatoes, nor potatoes, nor cocoa.
FT:   for instance they didn't have corn nor tomatoes nor potatoes nor cocoa
```

```
REF:  individuals who had been involved for several decades helped us appreciate our strengths
BASE: individuals who have been involved for several decades help us appreciate our strengths
FT:   individuals who had been involved for several decades helped us appreciate our strengths
```

See `comparison.json` for full side-by-side examples.

## Method

- **Base model:** openai/whisper-small (244M params)
- **Approach:** Full fine-tune (LoRA via PEFT was attempted first but had version-compatibility issues with transformers 4.40 — moved to full fine-tune for reliability)
- **Dataset:**
  - Google FLEURS (en_us): 500 samples for general English speech
  - Custom medical phrases: 50 self-recorded healthcare utterances covering insurance, scheduling, prescriptions, billing codes, and medical terminology
- **Hardware:** Single T4 GPU (Google Colab free tier)
- **Train config:** 3 epochs, batch 4, gradient accumulation 4, lr 1e-5, warmup 50 steps

## Limitations

- Test set small (n=83). Larger held-out eval needed for production confidence.
- Self-recorded medical phrases biased to single speaker (me).
- No latency benchmarking — inference speed not measured.
- WER improvement primarily from punctuation/casing normalization rather than improved phonetic accuracy on domain terms — honest read of the qualitative examples.
- 50 medical samples too small to claim true domain adaptation.
- FLEURS is read speech, not conversational — production voice agents need conversational data.

## Next Steps If Productionizing

- Multi-speaker medical dataset (10+ speakers across regional accents)
- Real Indian English benchmark (CommonVoice India locale, when access permits)
- LoRA adapter approach (would need PEFT/transformers version alignment)
- INT8 quantization for inference latency
- Streaming inference for real-time voice agent use
- Domain-specific tokenizer additions for drug names and billing codes
- Conversational data augmentation (not just read prompts)

## Files

- `train.ipynb` — full Colab notebook
- `comparison.json` — baseline vs fine-tuned predictions side-by-side
- `medical_phrases.txt` — 50 medical utterances used in training
- `README.md` — this file

## Author

Prakhar Pandey — DSAI student at IIT Guwahati. Built this as a portfolio piece while exploring voice AI engineering roles.
