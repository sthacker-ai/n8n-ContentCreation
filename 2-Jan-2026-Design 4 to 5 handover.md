---

# n8n Content Creation Pipeline — Current State & Handoff Notes

> **Purpose of this document**
> This is a clean handoff summary so a new chat can start fresh without reloading the entire conversation history.
> **Important:** Audio merging is **NOT solved yet**. That problem is clearly documented below.

---

# 🧠 **Wisdom & Whimsy – AI Content Automation Project Handover (v3)**

### 🗓️ Last Updated: 2-January-2026

### 👤 Owner: Sunil Thacker

### 🎯 Purpose:

To automate **multi-platform content creation** under the *Wisdom & Whimsy* brand — ensuring consistency in tone, design, and workflow while gradually achieving end-to-end video creation automation.

---

## 1. Overall Goal

Build an automated content pipeline in **n8n** that converts long-form written content into a **fully produced video**.

**End Output**

* One final video with:

  * Voiceover (AI cloned voice)
  * Visual images
  * Branded bullet slides
  * Transitions, background music, and SFX
  * All assembled via `ffmpeg`

---

## 2. High-Level Architecture

```
AI Video Script (JSON)
   ↓
Normalize segments
   ↓
segmentsForLoop[]   (array of segments)
   ↓
Per-segment processing (Run once for each item)
   ├── Audio stream (IN PROGRESS – merge broken)
   ├── Image stream (NOT STARTED)
   ├── Branded bullet slide stream (NOT STARTED)
   ↓
Segment-level video clips
   ↓
Final video merge + music + transitions
```

---

## 3. Segment Model (LOCKED & WORKING)

Each segment object looks like this:

```json
{
  "segmentKey": "segment-1",
  "type": "section",              // hook | intro | section | conclusion | outro
  "title": "The iPhone Upgrade Effect",
  "text": "Long narration text…",
  "bullets": [
    "Point one",
    "Point two"
  ],
  "visualCue": "Visual description",
  "order": 2
}
```

### Rules (Important)

* `hook / intro / conclusion / outro`

  * ❌ No bullets
* `section`

  * ✅ Bullets allowed
* `segmentKey` is the **primary identifier** for filenames and grouping

---

## 4. Audio Pipeline — CURRENT STATE

### 4.1 Constraints Discovered

* Replicate TTS has a **hard ~40 second audio limit**
* Long segments must be **chunked**
* Chunking rules:

  * ~650 characters max
  * Must **not cut mid-sentence**

---

### 4.2 Audio Flow (Updated, Accurate)

```
Segment
 → chunkText[]                     (JS node)
 → POST to Replicate (per chunk)
 → WAIT (polling-safe)
 → GET prediction (status check)
 → Download chunk audio
 → Save chunk files:
     audio_segment-1_part1_of2.wav
     audio_segment-1_part2_of2.wav

 → Group chunks per segment
     - Extract duration per chunk (ffprobe)
     - Compute totalDuration
     - Compute finalFileName
     - This is done via multiple JS nodes
     - THIS PART WORKS

 → Attempt to merge chunks with ffmpeg
     - Using merge_audio.ps1 (PowerShell)
     - Executed via n8n Execute Command node
     - Even with `-nostdin`, the command:
         • runs for minutes
         • never terminates
         • produces no merged output
     - This step is CURRENTLY BROKEN

 → Intended final output:
     audio_segment-1_48.wav
```

⚠️ **Important:**
Audio chunk generation works.
Audio chunk grouping works.
**Audio merging is NOT solved yet.**

---

## 5. File Naming Conventions (LOCKED)

### Chunk Files

```
audio_segment-1_part1_of2.wav
audio_segment-1_part2_of2.wav
```

### Intended Final Files

```
audio_hook_7.wav
audio_intro_21.wav
audio_segment-1_48.wav
audio_conclusion_19.wav
audio_outro_12.wav
```

---

## 6. Current Blocker (CRITICAL)

### Problem Summary

* We **do not need binary data** to merge audio
* All chunk files already exist on disk
* JS nodes correctly produce:

```json
{
  "segmentKey": "segment-1",
  "totalDuration": 48,
  "chunkFiles": [
    "C:/.../audio_segment-1_part1_of2.wav",
    "C:/.../audio_segment-1_part2_of2.wav"
  ],
  "finalFileName": "audio_segment-1_48.wav"
}
```

### What Is Broken

* PowerShell + ffmpeg merge logic
* `Execute Command` node:

  * Runs but **never exits**
  * No merged file created
  * Even after adding `-nostdin`
* This blocks the entire downstream pipeline

### What Is NOT the Problem

* ❌ Directory existence (folders exist)
* ❌ Chunk file paths (verified correct)
* ❌ Permissions
* ❌ Missing inputs
* ❌ Binary handling (not needed)

---

## 7. What Needs to Be Solved Next (Audio)

**Goal:**
Merge multiple `.wav` chunk files into **one final `.wav`** per segment, reliably, with name including duration at the end from n8n.

**Open Questions to Solve in New Chat**

* Correct ffmpeg invocation that:

  * Works non-interactively
  * Terminates correctly
  * Accepts file paths with spaces (Windows)
* Whether to:

  * Use `ffmpeg -filter_complex concat`
  * Use `-f concat` with a temp list file
  * Avoid PowerShell entirely and use CMD syntax
* Whether chunk merging should be:

  * Per-segment (looped)
  * Or batch-driven

---

## 8. Next Phases (After Audio Is Fixed)

### 8.1 Image Generation Stream (NOT STARTED)

* One image per segment
* AI-generated
* **No text embedded in images**
* Prompts must describe visuals only

Example:

* ✅ “A developer overwhelmed by floating AI tools”
* ❌ “A slide saying ‘Prompt Engineering’”

---

### 8.2 Branded Slide Deck (Bullets Only)

* Input:

  * `segment.title`
  * `segment.bullets[]`
* Start from a branded PNG template
* Overlay text using ffmpeg `drawtext`
* Only for `section` segments

---

### 8.3 Video Assembly (Future)

Per segment:

```
image / slide + audio_segment-X.wav → video_segment-X.mp4
```

Final:

```
All video segments → final_video.mp4
```

Post-processing:

* Background music
* Transitions
* SFX
* Audio normalization

---

## 9. Suggested New Chat Opening Prompt

Use this **exactly** in the new chat:

> I have an n8n pipeline where:
>
> * Audio chunk generation works
> * Audio chunk grouping works
> * PowerShell + ffmpeg merging of audio chunks is currently broken (execute node hangs, no output)
>
> I need a **reliable, terminating way** to merge multiple WAV files into one on Windows using ffmpeg from n8n.

---