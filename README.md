# Whisper Fine-Tuning: Indian English Medical Speech

Weekend experiment fine-tuning Whisper-small on Indian English 
accented medical/healthcare speech using LoRA.

## Why
Whisper has documented weaknesses on (a) Indian English accents 
and (b) medical terminology. Voice agents in healthcare workflows 
(insurance verification, appointment scheduling, prescription 
confirmation) need both. This experiment quantifies the gap and 
how much LoRA can close it.

## Stack
- Base model: openai/whisper-small (244M)
- Method: LoRA via PEFT (r=32, alpha=64)
- Dataset: Common Voice 16 (en, India locale) + custom medical phrases
- Hardware: Single T4 GPU (Colab)

## Status
🚧 In progress — 48 hour experiment, results coming Sunday.
