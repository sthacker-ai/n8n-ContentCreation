## 🧠 **Wisdom & Whimsy – AI Content Automation Project Handover**

### 🗓️ Last Updated: October 2025

### 👤 Owner: Sunil Thacker

### 🎯 Purpose: To automate multi-platform content creation under the *Wisdom & Whimsy* brand — ensuring consistency in tone, design, and workflow.

---

## 1. 🎬 Project Overview

The **Wisdom & Whimsy Content Automation System** is an AI-driven workflow built primarily in **n8n**.
It automates the process of:

1. Generating and validating multi-platform content (LinkedIn, Tweets, YouTube Scripts, Reels).
2. Expanding shorter text (like LinkedIn posts) into full-length video scripts.
3. Producing on-brand visual slides using AI-generated imagery (Gemini Image Model).
4. Overlaying text and brand logo via FFmpeg for polished, publish-ready assets.

The system evolves toward a **fully automated content engine**, where a single idea triggers the generation of:

* a long-form article,
* short posts (tweets/threads),
* a complete YouTube video script,
* branded visual slides,
* and future stages: narration, compilation, and auto-upload.

---

## 2. 🧩 Tech Stack Summary

| Component                | Tool / API                                            | Purpose                             |
| ------------------------ | ----------------------------------------------------- | ----------------------------------- |
| **Automation Framework** | `n8n` (self-hosted)                                   | Main workflow orchestration         |
| **LLM**                  | Gemini 2.5 Flash                                      | Core text generation                |
| **Image Generation**     | Gemini Image Model (preferred) / Replicate (fallback) | Slide / background generation       |
| **Video Composition**    | `FFmpeg` (local binary)                               | Overlay text + logo + brand styling |
| **Data Flow**            | JSON across n8n nodes                                 | Input → AI → Clean → Output         |
| **Storage**              | Google Drive (integrated paths)                       | Image & output storage              |
| **Fonts (local)**        | Merriweather, Inter                                   | Used in overlays                    |
| **Brand Assets**         | Logo (local + GitHub link)                            | Used as watermark overlay           |

---

## 3. 🪶 Brand Identity Summary

**Brand Name:** *Wisdom & Whimsy*
**Tagline:** *Big thoughts. Small chuckles.*

### 🎨 Logo Description

Brain with glasses — half “wise” (calm, intellectual), half “whimsical” (fun, jester hat).

### ✴️ Color Palette

| Color                          | Purpose                    | Hex       |
| ------------------------------ | -------------------------- | --------- |
| **Deep Navy / Charcoal**       | “Wisdom” text & titles     | `#2C3E50` |
| **Warm Coral / Orange**        | “Whimsy” text & highlights | `#E85C3A` |
| **Soft Mustard / Yellow Tint** | Light energy accent        | `#FFB347` |
| **Cream / Off-White**          | Background                 | `#F8EBD9` |
| **Teal / Turquoise**           | Optional secondary accent  | `#2CA5A9` |

### 🧠 Tone & Personality

* **Voice:** “Wise friend with a twinkle in his eye.”
* **Style:** Intelligent, relatable, with small humorous twists.
* **Audience:** Overthinkers, creators, readers, thinkers, self-improvers.

### 💡 Visual Identity Consistency

* Maintain same color palette across all visuals.
* Use Merriweather for wisdom/serious tone.
* Use Inter or rounded sans-serif for captions and bullets.
* Always include logo watermark (bottom-right).
* Prefer doodle-style or infographic visuals, not stock photography.

---

## 4. ✅ Completed Phases

### **Phase 1 – Core Content Automation**

✔️ Automated generation of:

* LinkedIn Article
* Tweets
* Long-form Video Script (8–10 mins)

✔️ Implemented validation rules:

* LinkedIn title < 200 characters
* LinkedIn body < 3000 characters
* Tweets < 280 characters
* Multi-stage validation (if over limit → reprocess AI)

✔️ Automated Gmail summary delivery:

* HTML-formatted email for validation results
* Embedded previews of LinkedIn & tweets
* Highlights validation status

✔️ JSON Cleaning & Parsing Logic:

* Sanitizes malformed or stringified JSON from AI output
* Handles double-parsed JSON cases
* Ensures each key (LinkedIn, Tweets, VideoScript) is normalized

✔️ Introduced fallback AI (2nd AI node)

* Shortens or trims LinkedIn article >3000 chars
* Keeps tone, CTA, and voice consistent

---

## 5. 🔄 Current Phase (Phase 2 – Video Generation & Visuals)

### 🎥 Objective

Turn AI-generated video scripts into visually branded *Wisdom & Whimsy slides* for narration and final video assembly.

### 🧩 Components in Progress

* ✅ Structured **videoScript JSON** (with sections, bullets, durations, cues)
* ✅ AI expansion node to convert 500-word input → 1000-word full-length script
* ✅ Gemini-based **image generation** (doodle / warm illustration style)
* ✅ FFmpeg-based **overlay engine** for:

  * Section title
  * 2–3 bullet points
  * Watermarked logo
  * Consistent brand colors

### ⚙️ Current FFmpeg Setup

* Uses `drawtext` for title & bullets
* `overlay` for logo
* `drawbox` for text clarity
* Local fonts for consistency
* Colors from brand palette
* Working output example tested successfully

### 🧾 Known Adjustments

* Logo was initially oversized → fixed via scaling (`scale=220:-1`)
* Text layout improved for spacing & contrast
* Plan to add *dynamic box height* (based on number of bullet points)
* Current FFmpeg runs via Execute Command Node (tested locally)
* Gemini image results outperform Replicate Schnell (selected as default model)

---

## 6. 🚧 Upcoming Phases

### **Phase 3 – Narration & Audio**

Goal: Generate AI voiceovers aligned with video script pacing.

Planned Steps:

1. Convert each section’s text → AI narration (TTS model).
2. Save each as `.mp3` with aligned timestamps.
3. Add optional background music (from clean, copyright-free sets).

Potential Tools:

* Google Cloud Text-to-Speech
* ElevenLabs or Play.ht (if premium)
* FFmpeg merging for sync

---

### **Phase 4 – Video Assembly**

Combine branded images + audio + transitions.

Plan:

1. Combine section images in sequence.
2. Add fade-in/out transitions.
3. Sync with narration audio.
4. Export to `.mp4` (1080p, 30fps).
5. Generate YouTube description & tags from AI metadata.

---

### **Phase 5 – Upload Automation**

Goal: Publish finalized content automatically.

Steps:

1. Connect YouTube API for upload.
2. Auto-generate title, description, tags.
3. Cross-post to Instagram Reels & LinkedIn Video.
4. Archive project assets in Google Drive folder.

---

## 7. 🧭 Workflow Summary (High-Level Map)

```
Google Sheet (idea/input)
   ↓
AI Node 1 – Generate JSON (LinkedIn, Tweets, VideoScript)
   ↓
Code Node – Clean + Validate + Normalize
   ↓
IF Node – Check LinkedIn length
   ├── ✅ Within 3K → Continue
   └── ❌ Over 3K → AI Node 2 (Trim & Re-validate)
   ↓
Gmail Node – Send Summary
Tweets Node – Post to X
LinkedIn Node – Post Article (with image)
   ↓
Video Script Node (AI) – Expand script to 1000 words
   ↓
Gemini Image Node – Generate slide backgrounds
   ↓
FFmpeg Command Node – Apply brand overlay (title, bullets, logo)
   ↓
(Upcoming) Audio Node → Assembly Node → Upload Node
```

---

## 8. 🔍 Troubleshooting Notes

| Issue                     | Cause                          | Fix                                  |
| ------------------------- | ------------------------------ | ------------------------------------ |
| JSON invalid in AI output | Code fences / nested JSON      | Strip ```json, reparse twice         |
| LinkedIn > 3000 chars     | AI verbosity                   | Trigger Node 2 rewriter              |
| FFmpeg parsing error      | Unescaped quotes / colons      | Escape `'` and `:` in drawtext       |
| Replicate JSON failure    | Newline / `\n` in payload      | Replace `\n` with literal `\\n`      |
| n8n hangs                 | Too large conversation context | Use handover document & restart chat |
| Logo too large            | Overlay scaling missing        | Add `[1:v] scale=220:-1 [logo];`     |
| Low contrast text         | No background                  | Add semi-transparent `drawbox`       |

---

## 9. 🧩 Next Planned Improvements

* 🧱 Modularize FFmpeg logic per section (looping automation)
* 🧠 Add slide-wise metadata (duration, visual cue, shortHook)
* 🎨 Auto-pick background tint based on section tone
* 🗣️ Integrate TTS for narration
* 🎞️ Build end-to-end video assembler
* ☁️ Automate upload to YouTube + LinkedIn
* 🪄 Generate Reel/Short snippets from long video

---

## 10. 🗂️ Handover Notes

* This document captures everything from idea inception to current workflow logic.
* All brand decisions (colors, fonts, tone) are finalized.
* Gemini Image Model is confirmed as the visual generator going forward.
* FFmpeg is stable with overlay + logo watermark setup.
* LinkedIn & Tweets flows are production-ready.
* The current focus (as of handover): *Building dynamic branded slides via Gemini + FFmpeg.*

---

## 🔖 To Be Defined (Next Chat)

**Revised Approach Discussion Placeholder:**
Sunil plans to refine or change the current direction of Phase 2.
This section will capture new strategy and updated approach once finalized in the next conversation.

