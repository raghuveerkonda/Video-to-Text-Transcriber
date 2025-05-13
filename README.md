# Video‑to‑Text Transcriber

A **Python + Whisper** desktop utility that takes any video (local file *or* URL), extracts its audio with FFmpeg, transcribes it with OpenAI Whisper, and saves a clean `.txt` transcript.

The project is split into two independent scripts so you can keep your existing logic intact:

| Script                     | Role                                                                                                                                                                |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Convert‑videototext.py** | 1‑click GUI that does *one* transcription job: download ▶ extract ▶ transcribe  (kept exactly as you wrote it)                                                      |
| **manager.py**             | A wrapper that adds power‑user features (batch mode, duplicate detection, model selection, tidy‑up prompt, etc.) and then calls `Convert‑videototext.py` underneath |

> **👀 Why two files?** So you don’t have to rewrite working code.  `manager.py` simply orchestrates many single‑file runs of your original script.

---

## ✨ Features

### 1. Correct filename hand‑off

* After Whisper finishes, `manager.py` renames `temp_audio.txt` to

  ```
  <video‑name>_transcribed_<YYYY‑MM‑DD_HH‑MM‑SS>.txt
  ```

  so every transcript is traceable and clash‑free.

### 2. Duplicate detection (local **and** URL)

* A tiny JSON db (`conversion_db.json`) stores

  * the **absolute file path** (for local files) **or** canonical URL
  * SHA‑256 hash (local)  or  yt‑dlp video‑ID (URL)
  * the transcript path + timestamp
* When you try the same input again, you’re politely asked whether to open the previous transcript instead of wasting time and GPU.

### 3. Model / language options

* Pick any official Whisper checkpoints (`tiny`, `base`, `small`, `medium`, `large`) from a drop‑down.
* Choose transcription language (leave blank for auto‑detect).
* **Heads‑up:** larger models download at first use (≈ 1.5 GB → 6 GB) and need more VRAM/CPU ‑ select wisely!

### 4. Batch / folder mode

* Point `manager.py` at a folder and it queues **all** video files inside (optionally recursive).  Perfect for lecture playlists or surveillance dumps.

### 5. Rich exception surfacing

* All subprocess and network errors bubble up into Tkinter message‑boxes with a *copy‑to‑clipboard* details button so users can report issues.

### 6. Post‑run clean‑up dialog

* After the last file finishes you get a checklist of everything that was downloaded or generated (video, audio, transcript).
* Green ✔︎ = keep,   Red ✘ = delete  → one click to tidy‑up.

---

## 🖥  Installation

```bash
# 1. Clone your repo
$ git clone https://github.com/your‑handle/video‑to‑text.git
$ cd video‑to‑text

# 2. Create env (recommended)
$ python -m venv venv
$ ./venv/Scripts/activate       # Windows

# 3. Install requirements
$ pip install -r requirements.txt
```

### External tools

* **FFmpeg** – add `ffmpeg.exe` to your PATH  ( `winget install ffmpeg` on Windows)
* **yt‑dlp** – auto‑installed via `requirements.txt`

---

## 🚀 Usage

### GUI (normal users)

```bash
python manager.py        # opens GUI
```

### CLI (power users / automation)

```bash
python manager.py  --input D:/Videos  --model small  --lang en  --recursive
```

* `--input`   file **or** folder **or** url
* `--model`  `tiny│base│small│medium│large`
* `--lang`   ISO‑639‑1 code, blank = auto
* `--recursive`  include sub‑folders

---

## 🗂  Project structure

```
video‑to‑text/
├─ Convert‑videototext.py   # your original GUI (unchanged)
├─ manager.py               # new wrapper with all bells & whistles
├─ requirements.txt         # pip deps (torch, whisper, yt‑dlp, requests, tk, etc.)
├─ conversion_db.json       # created on first run
└─ README.md                # you’re reading it ✅
```

---

## 🤝  Contributing

1. **Fork** the repo & create your feature branch
   `git checkout -b feature/awesome`
2. **Commit** your changes
   `git commit -m "Add awesome feature"`
3. **Push** to the branch
   `git push origin feature/awesome`
4. **Open Pull Request**

Please follow conventional‑commit style and run `pre‑commit` before PRs.

---

## 📄  License

MIT – do whatever you want, just don’t sue us.
