# YADEP: YouTube Audio Dataset Extraction Pipeline

YADEP (YouTube Audio Dataset Extraction Pipeline) is a highly robust, enterprise-grade, end-to-end data processing pipeline engineered to compile machine learning audio-text datasets from YouTube video resources or playlists. 

The pipeline is explicitly optimized to address two primary failure modes common in large-scale audio extraction frameworks: YouTube's signature `n-challenge` throttling/blocking algorithms and system crashes caused by high RAM spikes during multi-gigabyte audio transcription inferences. It features an advanced workspace multiplexer, low-memory weight isolation utilizing OpenAI's Whisper Large V3, rigorous system-wide quality assurance checks, and automated machine learning training alignment schemas.

---

## 🚀 Key Architectural Features

- **YouTube Throttling Solution**: Seamlessly integrates the **Deno JavaScript Runtime** with custom pre-release wrappers for `yt-dlp` to execute live player client emulation, bypassing standard platform signature blocks and access challenges.
- **RAM Crash Mitigation Strategy**: Avoids deep architectural memory allocation spikes by pre-slicing downstream master audio files into uniform **30-second ML audio frames** prior to model loading.
- **Low-Memory ASR Engine Optimization**: Leverages Hugging Face's `low_cpu_mem_usage` and half-precision float (`torch.float16`) tensor processing on CUDA GPU platforms to materialize weights across devices iteratively, blocking systemic Out-of-Memory (OOM) faults.
- **Granular Dataset Compiler**: Automatically produces high-level production manifests alongside segmented audio-to-text pairs perfectly mapped for direct ingestion into model fine-tuning arrays (STT/TTS).

---

## 📊 Pipeline Phase Flow Architecture

The data processing pipeline proceeds systematically across 11 discrete operational phases split into clear execution blocks:

### 1. Environment & Utility Provisioning
Installs core system binary decoders (`ffmpeg`), specific asset download layers (`yt-dlp`), sound processing wrappers (`pydub`), optimization packages (`accelerate`), and machine learning model backends (`transformers`, `torch`).

### 2. Input Gate Validation
Ingests a single YouTube Video or Playlist URL (`TARGET_URL`). Performs protective sanity checking to block invalid profile, channel, or creator landing pages.

### 3. Metadata Extraction Scanning
Executes high-velocity flat extraction sequences over the target URL index to gather core metrics (`Video ID`, `Title`, `Duration`, and `Uploader/Channel`) without activating heavy file-stream transfers.

### 4. Integrity Filtering & Alignment
Constructs the raw tracking spreadsheet (`YT_DATASET.csv`), executes column validation checks, purges corrupted or empty video descriptors, and runs a first-pass duplication filter using unique alphanumeric identifiers.

### 5. Workspace Isolation Setup
Filters out assets exceeding specified computational limits (e.g., maximum runtime thresholds like 30 minutes). Builds isolated workspace directories structured cleanly under a localized workspace repository namespace (`KrishiDarshan/<VIDEO_ID>`).

### 6. Bypassed Audio Acquisition
Deploys the upgraded Deno JS runtime engine wrapper to download master stream arrays and cleanly re-encodes structural components down to pristine standalone `audio.mp3` binary objects at standard bitrates.

### 7. Unified 30-Second Slicing Engine
Performs a storage verification pass to build the active asset manifest (`VERIFIED_AUDIO_INDEX.csv`). Re-loads localized master audio tracks via `pydub`, audits durations, and segments data structures sequentially into atomic 30-second frames (`chunk_000.mp3`, `chunk_001.mp3`, etc.).

### 8. Hardware Optimization Audit
Initiates system checks verifying active hardware acceleration arrays (e.g., Tesla T4 GPU engines), software library linkages (PyTorch, Hugging Face ecosystem), and binary paths (`ffmpeg`).

### 9. Memory-Guarded Speech Recognition (ASR)
Triggers custom garbage collection sweeps (`gc.collect`) and empties standard CUDA caches. Initializes `openai/whisper-large-v3` weights into half-precision matrices using memory-mapped allocations[cite: 1, 2]. Sequences through frames, filtering out fractured data elements (<15KB) to prevent matrix dimension exceptions, and exports linear transcript lines[cite: 1, 2].

### 10. Multi-Format Output Multiplexer
Binds textual strings to assemble three specialized output configurations within individual video workspaces[cite: 1, 2]:
- `transcript.txt`: Standard plain-text output mapping for textual parsing[cite: 1, 2].
- `transcript.json`: Comprehensive application package containing full extraction metadata, execution records, pipeline details, and transcribed assets[cite: 1, 2].
- `transcript_timestamps.txt`: Aligned timeline representation tracking simulated step drift across 30-second window increments[cite: 1, 2].

### 11. Quality Assurance Audit
Scans physical storage layout layers and compiles data into a comprehensive Markdown file (`QUALITY_AUDIT_REPORT.md`) outlining execution metrics, item exceptions, failed extractions, and tracking checks[cite: 1, 2].

### 12. Final Machine Learning Matrix Compiler
Assembles and saves the definitive data deliverables across two key analytical tables[cite: 1, 2]:
- `FINAL_VIDEO_MANIFEST.csv`: High-level global map indexing durations, word counts, and paths for all produced master audio and transcript assets[cite: 1, 2].
- `FINAL_ML_TRAINING_CHUNKS.csv`: Highly granular machine-learning-ready framework aligning segment ids, parent video maps, local audio chunk vectors, and targeted piece text values[cite: 1, 2].

---

## 📁 System Workspace Directory Layout

During pipeline execution, storage arrays are dynamically generated and organized according to the following structure[cite: 1, 2]:

```directory
.
├── YT_DATASET.csv                     # Raw tracking dataset extraction spreadsheet
├── VERIFIED_AUDIO_INDEX.csv           # Storage-verified baseline asset log
├── QUALITY_AUDIT_REPORT.md            # Comprehensive pipeline verification report
├── FINAL_VIDEO_MANIFEST.csv           # Global high-level text/audio map file
├── FINAL_ML_TRAINING_CHUNKS.csv       # Definitive granular audio-to-text ML training pairs
└── KrishiDarshan/                     # Core system root base workspace directory
    └── <VIDEO_ID>/                    # Target subfolder named via unique YouTube video ID
        ├── audio.mp3                  # Extracted and re-encoded master track binary
        ├── transcript.txt             # Plaintext compiled text document
        ├── transcript.json            # Structured metadata and pipeline asset payload
        ├── transcript_timestamps.txt  # Aligned timeline segment file
        └── chunks/                    # Isolated directory for segmented ML frames
            ├── chunk_000.mp3          # 00-30 seconds audio segment
            ├── chunk_001.mp3          # 30-60 seconds audio segment
            └── chunk_xxx.mp3          # Subsequent sequential training chunks
