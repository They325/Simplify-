# Simplify-
Upload files (ChatGPT exports, docs, notes, code) Type a topic (e.g. "astrology", "pricing ideas", "step-by-step instructions") Simplify runs a double-pass analysis and extracts only the relevant content Export as PDF, Word, Markdown, CSV, JSON, or plain text Customize fonts, size, and spacing — then edit specific sections if needed
# Simplify 🗂

Extract exactly what matters from your AI chat exports and documents.

## What it does
1. Upload files (ChatGPT exports, docs, notes, code)
2. Type a topic (e.g. "astrology", "pricing ideas", "step-by-step instructions")
3. Simplify runs a double-pass analysis and extracts only the relevant content
4. Export as PDF, Word, Markdown, CSV, JSON, or plain text
5. Customize fonts, size, and spacing — then edit specific sections if needed

---

## Setup

### 1. Install Python dependencies
```bash
cd simplify
pip install -r requirements.txt
```

### 2. Run the backend
```bash
python app.py
```
Backend runs at: http://localhost:5000

### 3. Open the frontend
Open `index.html` in your browser.
> For full functionality (file upload + export), run a local server:
```bash
python -m http.server 8080
```
Then visit: http://localhost:8080

---

## Features
- **Upload staging area** — add all files before any analysis starts
- **Double-pass analysis** — first pass extracts, second pass verifies nothing was missed
- **Delete with safety net** — confirm dialog + 4-second undo window
- **Targeted editing** — select specific text and correct just that section
- **Output customization** — font, size, line spacing, applied after generation
- **Multiple export formats** — PDF, Word (.docx), Markdown, plain text, CSV, JSON
- **Platform integrations** — connect ChatGPT, Claude, Gemini, Copilot, KIMI, Bolt, Lovable, Replit, GitHub

---

## Supported file types
TXT, PDF, DOCX, MD, JSON (including ChatGPT exports), CSV, HTML, PY, JS

## ChatGPT Export Format
Export your ChatGPT conversations as JSON (Settings → Data Controls → Export).
Simplify automatically parses the conversation format.

---

## Tech Stack
- **Backend**: Python + Flask
- **Frontend**: Vanilla HTML/CSS/JS (no framework required)
- **PDF generation**: ReportLab
- **Word docs**: python-docx
- **PDF reading**: pdfplumber
