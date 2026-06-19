# Sarvam TTS Dataset Pipeline

Pipeline built for the Sarvam AI "ML & Speech Data Pipeline" internship assignment.
Produces a 57-clip, single-speaker English + Hindi speech dataset for TTS training.

**Dataset (HuggingFace):** https://huggingface.co/datasets/aditya1101203/sarvam-tts-dataset

## Pipeline

```
YouTube
  → yt-dlp download (raw audio)
  → ffmpeg segmentation (clean single-speaker clips, 16kHz mono WAV)
  → Sarvam ASR (Saaras v3) transcription
  → Sarvam LLM (sarvam-105b) speaking-style labeling
  → final structured metadata
  → HuggingFace dataset
```

## Scripts

| Script | Purpose |
|---|---|
| `1_acquire.py` | Downloads source videos (once per unique URL), cuts timestamped clips using `metadata/english.csv` / `hindi.csv` |
| `2_transcribe.py` | Batch-transcribes all clips via Sarvam ASR, saves raw JSON transcripts |
| `3_label_emotions.py` | Reads transcripts, labels each with a primary speaking style via Sarvam LLM |
| `4_build_metadata.py` | Merges transcripts + style labels into `final_metadata.csv` |
| `5_verify.py` | Prints dataset summary — clip counts, duration, style distribution |
| `6_make_hf_metadata.py` | Generates per-folder `metadata.csv` files for HuggingFace's AudioFolder loader |

## Metadata files

- `metadata/english.csv`, `metadata/hindi.csv` — source video URLs and clip timestamps
- `metadata/emotion_labels.csv` — transcripts + style labels per clip
- `metadata/final_metadata.csv` — final merged dataset metadata (audio path, transcript, language, style)
- `transcripts_eng/`, `transcripts_hindi/` — raw per-clip ASR JSON output from Sarvam Saaras v3

## Dataset summary

- 57 total clips (~54 minutes)
- 29 English clips, 28 Hindi clips
- Single speaker per clip, natural sentence boundaries, minimal background noise
- Style labels: storytelling, educational, motivational, conversational, opinion, formal, review, reading

## Notes

Audio files are not included in this repo (large binary files) — they live in the
HuggingFace dataset linked above, along with the transcript text and style labels
(as columns in `metadata.csv`). The raw per-clip ASR JSON outputs (from Sarvam Saaras v3)
are included here under `transcripts_eng/` and `transcripts_hindi/` for transparency —
these are the unprocessed ASR outputs before they were merged into `final_metadata.csv`.