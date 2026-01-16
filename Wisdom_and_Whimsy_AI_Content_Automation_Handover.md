Excellent idea — time to consolidate all this progress into a clean, updated **handover document** like the one you started with.

Below is the new version — **fully merged**, covering your previous document plus everything we’ve achieved in this chat (video generation overhaul, Gemini/FFmpeg strategy shift, image templating, PowerShell duration automation, etc.).

You can copy this into a `.md` file (e.g. `Wisdom_and_Whimsy_AI_Content_Automation_Handover_v2.md`).

---

# 🧠 **Wisdom & Whimsy – AI Content Automation Project Handover (v2)**

### 🗓️ Last Updated: November 2025

### 👤 Owner: Sunil Thacker

### 🎯 Purpose:

To automate **multi-platform content creation** under the *Wisdom & Whimsy* brand — ensuring consistency in tone, design, and workflow while gradually achieving end-to-end video creation automation.

---

## 1. 🎬 Project Overview

The **Wisdom & Whimsy Content Automation System** is built in **n8n** and automates:

1. Generating and validating multi-platform content (LinkedIn, Tweets, YouTube scripts, Reels).
2. Expanding short content into long-form scripts.
3. Producing branded visuals (Gemini / template-based).
4. Generating AI-based narration (voice cloning).
5. Composing and synchronizing final videos (audio + visuals + music).

---

## 2. 🧩 Tech Stack Summary

| Component                | Tool / API                                           | Purpose                                  |
| ------------------------ | ---------------------------------------------------- | ---------------------------------------- |
| **Automation Framework** | n8n (self-hosted)                                    | Workflow orchestration                   |
| **LLM**                  | Gemini 2.5 Flash                                     | Core text generation                     |
| **Image Generation**     | Gemini Image Model / Gemini Nano Banana              | Template-based slide creation            |
| **Video Composition**    | FFmpeg + FFprobe                                     | Overlay, merge, timing, audio/video sync |
| **Voice / Audio**        | Replicate (TTS models e.g. Chatterbox, Flux-Schnell) | AI voice generation                      |
| **File Handling**        | Google Drive + Local paths                           | Storage for inputs & outputs             |
| **Scripting**            | PowerShell & Bash via Execute Command node           | File rename + duration calculation       |
| **Fonts**                | Merriweather (serif), Inter (sans-serif)             | On-brand typography                      |
| **Brand Assets**         | Local logo + template images                         | Visual identity consistency              |

---

## 3. 🪶 Brand Identity Summary

**Brand Name:** *Wisdom & Whimsy*
**Tagline:** *Big thoughts. Small chuckles.*

| Color                | Purpose                  | Hex       |
| -------------------- | ------------------------ | --------- |
| Deep Navy / Charcoal | Titles & Wisdom tone     | `#2C3E50` |
| Warm Coral / Orange  | Highlights & Whimsy tone | `#E85C3A` |
| Soft Mustard         | Accent energy            | `#FFB347` |
| Cream / Off-White    | Background               | `#F8EBD9` |
| Teal / Turquoise     | Secondary accent         | `#2CA5A9` |

**Voice:** Wise friend + witty storyteller
**Style:** Intelligent, relatable, slightly humorous
**Visuals:** Doodle/illustration style, clean fonts, watermark bottom-right

---

## 4. ✅ Completed Phases

### Phase 1 – Core Content Automation

✔ LinkedIn, Tweets, Long-form video scripts
✔ JSON cleaning + validation + fallback LLM
✔ Automated Gmail summaries for validation

### Phase 2 – Visual Generation (System v1)

✔ Gemini Image Model for slide creation
✔ FFmpeg overlay system (titles + bullets + logo)
✔ Local font integration
✔ Brand colors applied consistently

---

## 5. 🔄 Current Phase (Phase 2 – Visual Generation Revised)

### 🎥 Objective

Simplify video slide creation by **embedding text directly inside images** using AI, instead of layering text via FFmpeg.

### 🧠 Strategy Shift

Old flow → Generate background + overlay via FFmpeg
New flow → Generate final slides with text baked in using Gemini Nano Banana (Option C)

**Reason:** FFmpeg text overlays caused layout & timing inconsistencies.
**Benefit:** Faster, cleaner slide generation, less post-processing.

### Implementation

* Base **template image** (off-white doodle + footer space).
* **Gemini Nano Banana** generates text-embedded slide:

  * Title + bullets rendered directly on background.
  * Prompt includes brand colors & font specs.
* If Gemini output fails → fallback to ChatGPT or Perplexity image generator.
* OCR + duration logic for quality control (planned next).

---

## 6. 🧰 Technical Improvements & New Automation

### 🖼️ Template Image Handling

* Instead of using a remote URL, the template is **downloaded via HTTP GET** and passed as **binary** input to the AI node.
* Gemini nodes cannot fetch external URLs directly, so binary attachment ensures correct background usage.

### 🧠 GraphicsMagick Fix

* Installed **GraphicsMagick** locally to enable n8n’s **Edit Image** node for adding text or minor overlays.
* Verified working setup after installation.

### 🎧 Audio Duration Automation

* Replicate TTS does not return audio duration → solved locally.
* Added **FFprobe integration** to extract actual duration (seconds).
* Implemented **PowerShell script (`rename.ps1`)** to:

  1. Receive audio path.
  2. Run `ffprobe` to get duration.
  3. Round seconds and append to file name (e.g. `chunk_0.wav` → `chunk_0_23.wav`).
  4. Return JSON: `{"path":"…","duration":23.4,"seconds":23}`

**n8n Execute Command** line:

```
powershell.exe -NoProfile -NonInteractive -ExecutionPolicy Bypass -File "G:\My Drive\My n8n Projects\n8n-ContentCreation\Social Media Post\Videos\Audio\rename.ps1" -File "{{$json.fileName}}"
```

---

## 7. 🚧 Next Phases

### Phase 3 – Narration & Audio

* Integrate AI voice generation (Replicate Chatterbox / Flux-Schnell).
* Store each section’s audio and duration metadata.
* Sync duration with each slide during video assembly.

### Phase 4 – Video Assembly

* Combine slides sequentially with durations using FFmpeg concat demuxer.
* Add background music and cross-fade transitions.
* Auto-export final video in 1080p.

### Phase 5 – Upload Automation

* Auto-upload to YouTube + Instagram Reels + LinkedIn Video.
* Auto-generate titles, descriptions, and tags via LLM.
* Archive final assets to Google Drive.

---

## 8. 🧩 Troubleshooting Notes

| Issue                            | Cause                           | Fix                                        |
| -------------------------------- | ------------------------------- | ------------------------------------------ |
| Gemini ignores background URL    | External URL not fetched        | Download via HTTP node → attach as binary  |
| Edit Image error “empty buffer”  | GraphicsMagick missing          | Install GM and restart n8n                 |
| Replicate audio missing duration | API doesn’t return it           | Use FFprobe script to compute locally      |
| File rename failed               | Wrong shell (cmd vs PowerShell) | Force PowerShell execution                 |
| FFmpeg overlay timing off        | Even durations only             | Now fixed via audio-driven timing workflow |

---

## 9. 🧭 Workflow Summary (High-Level Map)

```
Google Sheet (idea)
   ↓
AI Node 1 – Generate JSON content
   ↓
Clean + Validate (JSON normalizer)
   ↓
LLM Fallback (LinkedIn > 3K chars)
   ↓
Gmail Summary node + LinkedIn/Tweet publish
   ↓
VideoScript Node (AI expansion)
   ↓
Gemini Image Node – text-embedded slides
   ↓
Replicate TTS Node – generate audio
   ↓
HTTP Request – download audio
   ↓
Execute Command (FFprobe + rename.ps1)
   ↓
Store durations → use for video assembly
   ↓
FFmpeg merge (slides + audio)
   ↓
Upload + archive
```

---

## 10. 🔖 Summary of Current Status

✅ Text + visual generation pipeline stable
✅ Template and Edit Image nodes configured
✅ PowerShell rename automation working
🚧 Next step: use audio duration to generate slide timings and automate video assembly

---

**End of Handover v2**
*(Prepared November 2025 – captures all improvements discussed in this chat)*
