# message-analyser

# Chat Love Predictor — README

**Short name:** Love Theorem Predictor (single-file HTML)  
**Language:** Hindi (UI Hindi labels) / Code: HTML + CSS + JS  
**Purpose:** WhatsApp chat (.txt / .pdf) ko browser mein parse karke heuristic "Love Theorem" apply karta hai:
> **Love = 50 + R + E + M + C**

Jahan:
- **R** = Reply-time score (0–30) — jitna jaldi reply, utna bada score.  
- **E** = Emoji score (0–20) — jitne emojis, utna bada score.  
- **M** = Message balance score (0–20) — target aur aapke beech messages ka balance.  
- **C** = Consistency score (0–20) — kitne alag dino mein target active raha (span ke hisaab se).

Final output:
- **loveRaw** (numeric, ~50..140)  
- **lovePercent** (normalized 0..100%) — easy verdict:
  - >=80% → Haan — strong interest
  - 55–79% → Maybe — friendly / kuch interest
  - 35–54% → Shayad nahi — neutral
  - <35% → Nahin — low interest

---

## Files included
- `chat-love-predictor.html` — single-file app (HTML + CSS + JS). Copy & open in browser.
- (Optional) `chat-love-predictor-fixed.html` — chart fixes version (if needed).

---

## Quick setup & run (local)
1. Copy the provided single-file HTML and save as `chat-love-predictor.html`.  
2. Open the file in a browser:
   - Simple: double-click the `.html` file.
   - If PDF parsing (pdf.js worker) gives a worker error when opened using `file://`, run a simple local server:
     ```
     python -m http.server 8000
     ```
     Then open: `http://localhost:8000/chat-love-predictor.html`.

---

## How to use (step-by-step)
1. Open `chat-love-predictor.html` in browser.  
2. `File upload` — choose WhatsApp exported `.txt` (recommended) or `.pdf`.  
   - For WhatsApp: use "Export chat" → "Without Media" to get `.txt`. This format is easiest to parse.  
3. `Target name` — type the person’s name you want to check (partial match allowed).  
4. Click **Predict Love**.  
5. You will see:
   - Parsed preview (first 25 messages) — check names exactly as parsed.  
   - Participant summary (messages, avg reply time, emojis, active days).  
   - Component values (R, E, M, C).  
   - Love raw and normalized percent + Hindi verdict.  
   - Charts:
     - Messages by hour (bar)
     - Message share (pie)
     - Reply-time plot (X = your message index, Y = reply minutes)

---

## Input format examples (WhatsApp .txt)
Parser recognizes formats like:

Agar aapke export ka format different ho to paste 8–12 sample lines here (anonymize) so parser can be adjusted.

---

## Explanation of components (implementation detail)
- **R (Reply-time 0–30)**  
  - avgReply < 2 min → R = 30  
  - avgReply < 10 → R = 25  
  - avgReply < 30 → R = 20  
  - avgReply < 60 → R = 10  
  - else → R = 0

- **E (Emojis 0–20)**  
  - E = min(20, emojis_count * 3)

- **M (Message balance 0–20)**  
  - Choose the most frequent "other" participant as *you*.  
  - ratio = target_messages / your_messages (capped at 2)  
  - M = round((min(ratio,2)/2) * 20)  
    - ratio 0 → 0, 1 → 10, 2 → 20 (roughly)

- **C (Consistency 0–20)**  
  - activeDays = distinct days target sent messages  
  - spanDays = total days between first and last timestamp  
  - C = round((activeDays / spanDays) * 20), capped 0..20

- **Normalization**  
  - loveRaw in [50..140] is mapped to percent 0–100 as:
    ```
    lovePercent = ((loveRaw - 50) / (140 - 50)) * 100
    ```
  - This makes interpretation easier (0% = no interest per measure, 100% = maximal per these heuristics).

---

## Troubleshooting (common issues)
- **Charts not visible / blank**  
  - Make sure browser window is wide enough; the app sets canvas sizes.  
  - If reply-time plot is empty: auto-detection of "you" may have failed. Check *Parsed preview* to see exact participant names — use that exact name in `Target name`.  
  - If PDF upload fails: open via local server (see Setup).

- **Parsed preview shows garbage / 0 messages**  
  - Your .txt format might differ. Paste 8–12 anonymized lines into chat to get parser adjusted.  
  - Avoid exporting with "Include media" (media lines can disrupt parsing).

- **Target not found**  
  - Use exact name (case-insensitive substring match works). Check "Parsed preview" to copy the name.

---

## Privacy & security
- The single-file HTML runs **fully in your browser**; files are not uploaded to a server.  
- If you paste content into chat or share parsed output, that will be visible in this conversation — remove any sensitive content beforehand.

---

## Example (fictional)
Given:
- Your messages: 60, Target messages: 40  
- Avg reply (target) = 6 minutes → R ≈ 25  
- Target emojis = 5 → E = min(20,5*3) = 15  
- Ratio = 40 / 60 = 0.666 → M ≈ 7  
- Chat span = 20 days, activeDays (target) = 10 → C = 10  
Then:
- loveRaw = 50 + 25 + 15 + 7 + 10 = 107  
- lovePercent ≈ 63% → Verdict: **Maybe — friendly / some interest**

---

## If you want changes
- Increase/decrease weight thresholds for R/E/M/C — open the HTML and edit the mapping functions (comments near `computeLove()` function).  
- Want CSV export of reply-times or hover text on reply chart? I can add that.  
- Parser adjustments for custom export formats: paste sample lines and I’ll update parser patterns.

---

## Quick dev notes (for maintainers)
- Uses `pdf.js` (UMD) for PDF → text. Worker must be set via `pdfjsLib.GlobalWorkerOptions.workerSrc`.  
- Uses `Chart.js` (v3+) for charts (`responsive:true`, `maintainAspectRatio:false`).  
- Parser uses regex patterns — add or reorder to support other export layouts.

---

## Contact / support
Agar parser ya prediction me kuch theek nahi lag raha ho, to **8–12 sample lines** yahan paste karo (anonymize names/messages) — main `parseMessages()` regex turant adjust kar dunga aur updated HTML provide kar dunga.

---

**Good luck!** Agar chaho, main abhi aapke liye:
- `README.md` ko polished English version me bhi de doon, ya  
- `README.md` ke saath ek `SAMPLE_INPUT.txt` example file bana ke de doon.  
Batao kaunsa chahiye.
