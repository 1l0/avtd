# Audio-Visual Timeline Description Specification (AVTD)
**Version:** 1.0.0  
**Status:** Standard Specification Proposal  
**Target Audience:** LLM, Music AI Agents, DAW Converters, Audio Analysts  

---

## 1. Overview

The Audio-Visual Timeline Description (AVTD) specification defines a standard tabular (CSV) format for capturing high-resolution timeline events (onset attacks, section shifts, phrase vocalizations) along with semantic context (natural language captions, categorical tags) for audio and music data.

### Key Features
- **Dual Timeline:** Maintains both absolute time (`time_sec` with millisecond precision) and musical time (`time_bar` in Bar.Beat.Tick) to support both audio alignment and musical tempo calculations.
- **High-Resolution Eventing:** Capable of capturing sub-second drum hits, transient attacks, and vocal phrase boundaries (`hit`, `phrase`) with millisecond accuracy.
- **LLM/Parser Friendly:** Uses pipe-delimited (`|`) tag lists, strict Enum types, and standard CSV escaping to ensure deterministic parsing by AI models and software scripts.

---

## 2. CSV Schema Definition

| Column | Type | Required | Description | Format / Example |
| :--- | :--- | :---: | :--- | :--- |
| **`time_sec`** | `String` | **Yes** | Absolute start time (millisecond precision) | `MM:SS.mmm` (e.g., `00:01.850`) |
| **`time_bar`** | `String` | **Yes** | Musical time location | `Bar.Beat.Tick` (e.g., `1.4.000`) |
| **`duration`** | `Float` / `String` | **Yes** | Event duration in seconds | Decimal seconds (e.g., `0.250`, `3.150`) |
| **`event_type`** | `Enum` | **Yes** | Event classification | Predefined reserved words (`section`, `hit`, `phrase`, `sustain`) |
| **`target_part`** | `Enum` | **Yes** | Target instrument or layer | Predefined reserved words (`Structure`, `Kick`, `Snare`, `Vocal`, etc.) |
| **`tags`** | `String` | **Yes** | Attribute tags | Pipe-delimited snake_case (`heavy_kick\|downbeat`) |
| **`caption`** | `String` | **Yes** | Natural language description | Free-form text (double-quoted if containing commas) |

---

## 3. Column Details

### 3.1 `time_sec`
- Format: `MM:SS.mmm` (`00:00.000` to `99:59.999`)
- Represents absolute elapsed time from the start of the audio file (00:00.000).

### 3.2 `time_bar`
- Format: `Bar.Beat.Tick`
- `Bar`: 1-based bar index.
- `Beat`: 1-based beat index (1 to 4 in 4/4 time signature).
- `Tick`: Sub-beat resolution (standard: 480 Ticks per beat / 192 Ticks per 16th note).

### 3.3 `event_type` (Reserved Words)
- `section`: Major musical structural transition (e.g., Intro, Verse, Chorus, Drop).
- `phrase`: A cohesive melodic, vocal, or lead musical thought.
- `hit`: Instantaneous audio onset attack (e.g., drum hit, one-shot SFX).
- `sustain`: Sustained or evolving harmonic background (e.g., chord pad, bass drone).

### 3.4 `target_part` (Reserved Words)
- `Structure`: Song-level arrangement and global events.
- `Kick`, `Snare`, `HiHat`, `Cymbal`, `Percussion`: Percussive instruments.
- `Bass`, `SubBass`: Low-frequency elements.
- `Vocal`, `LeadVocal`, `BackingVocal`, `VocalChop`: Vocal elements.
- `Synth`, `Guitar`, `Piano`, `Brass`, `Strings`: Instrumental elements.
- `SFX`, `Riser`: Sound effects and transition elements.

### 3.5 `tags`
- Elements must be lowercase snake_case (`a-z`, `0-9`, `_`).
- Elements MUST be separated by `|` (pipe) to prevent CSV delimiter conflicts.

---

## 4. AI Prompt Template

When instructing an LLM to generate or parse AVTD-compliant CSV data, supply the following system instruction:

```text
[System Instruction]
You are an AI audio analysis system specializing in musical structure and timeline parsing.
Analyze the input audio/musical data and generate CSV strictly adhering to the AVTD 1.0.0 specification.

Output Format Rules:
1. Header row: time_sec,time_bar,duration,event_type,target_part,tags,caption
2. time_sec must use MM:SS.mmm format (millisecond precision).
3. time_bar must use Bar.Beat.Tick format.
4. event_type must be one of [section, phrase, hit, sustain].
5. target_part must be a standard label such as [Structure, Kick, Snare, HiHat, Bass, Vocal, Synth, SFX].
6. tags must be lowercase_snake_case separated by pipes (|) (e.g., heavy_kick|downbeat).
7. Wrap caption in double quotes if it contains commas.