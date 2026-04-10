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
- I <!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8"/>
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Simplify — Extract What Matters</title>
  <link href="https://fonts.googleapis.com/css2?family=DM+Serif+Display:ital@0;1&family=DM+Sans:ital,wght@0,300;0,400;0,500;1,300&display=swap" rel="stylesheet"/>
  <style>
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
    :root {
      --bg: #F8F7FF;
      --ink: #1C1B2E;
      --accent: #5B6AF0;
      --accent-light: #EDEFFD;
      --accent2: #E8A87C;
      --accent2-light: #FDF3EC;
      --accent3: #56C4A8;
      --accent3-light: #E8F8F4;
      --muted: #8F8FA8;
      --border: #E2E1F0;
      --card: #EFEEF8;
      --danger: #E05B5B;
      --warning: #E8A87C;
      --white: #FDFCFF;
    }
    html { scroll-behavior: smooth; }
    body {
      background: var(--bg);
      color: var(--ink);
      font-family: 'DM Sans', sans-serif;
      font-weight: 300;
      line-height: 1.6;
      -webkit-font-smoothing: antialiased;
      min-height: 100vh;
    }

    /* ── LAYOUT ── */
    .app { display: flex; flex-direction: column; min-height: 100vh; }

    /* ── NAV ── */
    nav {
      position: sticky; top: 0; z-index: 200;
      background: var(--white);
      border-bottom: 1px solid var(--border);
      border-top: 3px solid var(--accent);
      display: flex; align-items: center; justify-content: space-between;
      padding: 1rem 2rem;
    }
    .logo { font-family: 'DM Serif Display', serif; font-size: 1.3rem; letter-spacing: -0.02em; }
    .logo span { color: var(--accent); font-style: italic; }
    .nav-status { font-size: 0.78rem; color: var(--muted); display: flex; align-items: center; gap: 0.5rem; }
    .status-dot { width: 7px; height: 7px; border-radius: 50%; background: var(--border); }
    .status-dot.active { background: var(--accent); animation: pulse 2s infinite; }
    @keyframes pulse { 0%,100%{opacity:1} 50%{opacity:0.4} }

    /* ── MAIN ── */
    main { flex: 1; max-width: 960px; width: 100%; margin: 0 auto; padding: 2.5rem 1.5rem 4rem; }

    /* ── STEPS BAR ── */
    .steps-bar {
      display: flex; align-items: center; gap: 0; margin-bottom: 2.5rem;
      background: linear-gradient(135deg, var(--accent-light) 0%, var(--accent3-light) 100%);
      border-radius: 14px; padding: 0.6rem 1rem;
      overflow: hidden; border: 1px solid var(--border);
    }
    .step-item { display: flex; align-items: center; gap: 0.5rem; flex: 1; }
    .step-num {
      width: 26px; height: 26px; border-radius: 50%;
      display: flex; align-items: center; justify-content: center;
      font-size: 0.72rem; font-weight: 500;
      background: var(--border); color: var(--muted);
      transition: all 0.3s; flex-shrink: 0;
    }
    .step-item.active .step-num { background: var(--accent); color: #fff; }
    .step-item.done .step-num { background: var(--ink); color: #fff; }
    .step-label { font-size: 0.8rem; color: var(--muted); white-space: nowrap; }
    .step-item.active .step-label { color: var(--ink); font-weight: 500; }
    .step-item.done .step-label { color: var(--ink); }
    .step-arrow { color: var(--border); font-size: 0.8rem; margin: 0 0.5rem; }

    /* ── PANELS ── */
    .panel { animation: fadeUp 0.4s ease both; }
    @keyframes fadeUp { from{opacity:0;transform:translateY(16px)} to{opacity:1;transform:none} }

    .panel-title {
      font-family: 'DM Serif Display', serif;
      font-size: 1.8rem; letter-spacing: -0.02em;
      margin-bottom: 0.4rem;
    }
    .panel-sub { font-size: 0.9rem; color: var(--muted); margin-bottom: 2rem; }

    /* ── INTEGRATIONS ── */
    .integrations-label { font-size: 0.72rem; font-weight: 500; letter-spacing: 0.1em; text-transform: uppercase; color: var(--muted); margin-bottom: 0.8rem; }
    .integration-grid { display: flex; flex-wrap: wrap; gap: 0.5rem; margin-bottom: 2rem; }
    .integration-btn {
      display: flex; align-items: center; gap: 0.5rem;
      padding: 0.45rem 0.9rem; border-radius: 100px;
      border: 1px solid var(--border); background: var(--white);
      font-size: 0.8rem; cursor: pointer; transition: all 0.18s;
      font-family: 'DM Sans', sans-serif; color: var(--ink);
    }
    .integration-btn:hover { border-color: var(--accent); background: var(--accent-light); }
    .integration-btn.connected { border-color: var(--accent); background: var(--accent-light); color: var(--accent); }
    .integration-icon { font-size: 1rem; }

    /* ── DROP ZONE ── */
    .drop-zone {
      border: 1.5px dashed var(--border); border-radius: 18px;
      padding: 2.5rem; text-align: center; cursor: pointer;
      background: var(--white); transition: all 0.2s; margin-bottom: 1.5rem;
      position: relative;
    }
    .drop-zone:hover, .drop-zone.over { border-color: var(--accent); background: var(--accent-light); }
    .drop-icon { font-size: 2rem; margin-bottom: 0.8rem; }
    .drop-zone h3 { font-size: 1rem; font-weight: 500; margin-bottom: 0.3rem; }
    .drop-zone p { font-size: 0.83rem; color: var(--muted); }
    .drop-types { margin-top: 0.5rem; font-size: 0.72rem; color: var(--border); letter-spacing: 0.08em; }
    #fileInput { display: none; }

    /* ── FILE LIST ── */
    .file-list { display: flex; flex-direction: column; gap: 0.5rem; margin-bottom: 1.5rem; }
    .file-item {
      display: flex; align-items: center; gap: 1rem;
      background: var(--white); border: 1px solid var(--border);
      border-radius: 12px; padding: 0.75rem 1rem;
      transition: border-color 0.2s;
      animation: popIn 0.2s ease both;
    }
    @keyframes popIn { from{opacity:0;transform:scale(0.96)} to{opacity:1;transform:none} }
    .file-item.deleting { opacity: 0.4; pointer-events: none; }
    .file-ext {
      width: 36px; height: 36px; border-radius: 8px;
      background: var(--accent-light); display: flex; align-items: center; justify-content: center;
      font-size: 0.7rem; font-weight: 500; color: var(--accent); flex-shrink: 0;
      text-transform: uppercase; letter-spacing: 0.05em;
    }
    .file-ext.pdf  { background: #FEF2F2; color: #E05B5B; }
    .file-ext.doc  { background: var(--accent-light); color: var(--accent); }
    .file-ext.docx { background: var(--accent-light); color: var(--accent); }
    .file-ext.json { background: var(--accent2-light); color: #B5712A; }
    .file-ext.csv  { background: var(--accent3-light); color: #379B84; }
    .file-ext.md   { background: #F0EFF8; color: #1C1B2E; }
    .file-ext.txt  { background: var(--card); color: var(--muted); }
    .file-ext.py   { background: #FEF9E7; color: #B7770D; }
    .file-ext.js   { background: #FFFDE7; color: #9A7500; }
    .file-info { flex: 1; min-width: 0; }
    .file-name { font-size: 0.88rem; font-weight: 400; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
    .file-size { font-size: 0.75rem; color: var(--muted); }
    .file-delete-btn {
      width: 30px; height: 30px; border-radius: 50%;
      border: 1px solid var(--border); background: none;
      display: flex; align-items: center; justify-content: center;
      cursor: pointer; font-size: 0.9rem; color: var(--muted);
      transition: all 0.18s; flex-shrink: 0;
    }
    .file-delete-btn:hover { border-color: var(--danger); color: var(--danger); background: #fdf2f2; }

    /* ── UNDO TOAST ── */
    .undo-toast {
      position: fixed; bottom: 2rem; left: 50%; transform: translateX(-50%);
      background: var(--ink); color: var(--bg);
      padding: 0.8rem 1.5rem; border-radius: 100px;
      font-size: 0.85rem; display: flex; align-items: center; gap: 1rem;
      z-index: 999; animation: slideUp 0.3s ease;
      box-shadow: 0 8px 24px rgba(0,0,0,0.2);
    }
    @keyframes slideUp { from{opacity:0;transform:translateX(-50%) translateY(12px)} to{opacity:1;transform:translateX(-50%) translateY(0)} }
    .undo-btn {
      background: var(--accent3); color: #fff;
      border: none; border-radius: 100px; padding: 0.3rem 0.9rem;
      font-size: 0.8rem; cursor: pointer; font-family: 'DM Sans', sans-serif;
    }
    .undo-bar {
      position: absolute; bottom: 0; left: 0; height: 3px;
      background: var(--accent3); border-radius: 0 0 100px 100px;
      animation: shrink 4s linear forwards;
    }
    @keyframes shrink { from{width:100%} to{width:0%} }

    /* ── CONFIRM DIALOG ── */
    .overlay {
      position: fixed; inset: 0; background: rgba(0,0,0,0.35);
      display: flex; align-items: center; justify-content: center;
      z-index: 998; animation: fadeIn 0.2s ease;
    }
    @keyframes fadeIn { from{opacity:0} to{opacity:1} }
    .dialog {
      background: var(--white); border-radius: 20px;
      padding: 2rem; max-width: 360px; width: 90%;
      text-align: center; box-shadow: 0 20px 60px rgba(0,0,0,0.15);
      animation: popIn 0.2s ease;
    }
    .dialog h3 { font-size: 1.1rem; font-weight: 500; margin-bottom: 0.5rem; }
    .dialog p { font-size: 0.85rem; color: var(--muted); margin-bottom: 1.5rem; }
    .dialog-actions { display: flex; gap: 0.75rem; justify-content: center; }
    .btn-cancel {
      padding: 0.6rem 1.4rem; border-radius: 100px;
      border: 1px solid var(--border); background: none;
      font-size: 0.85rem; cursor: pointer; font-family: 'DM Sans', sans-serif;
      transition: all 0.18s;
    }
    .btn-cancel:hover { border-color: var(--ink); }
    .btn-delete {
      padding: 0.6rem 1.4rem; border-radius: 100px;
      background: var(--danger); color: #fff; border: none;
      font-size: 0.85rem; cursor: pointer; font-family: 'DM Sans', sans-serif;
      transition: opacity 0.18s;
    }
    .btn-delete:hover { opacity: 0.85; }

    /* ── TOPIC INPUT ── */
    .topic-section { margin-bottom: 2rem; }
    .topic-label { font-size: 0.8rem; font-weight: 500; margin-bottom: 0.5rem; display: block; }
    .topic-input {
      width: 100%; padding: 0.9rem 1.2rem;
      border: 1.5px solid var(--border); border-radius: 14px;
      background: var(--white); font-size: 0.95rem;
      font-family: 'DM Sans', sans-serif; color: var(--ink);
      transition: border-color 0.2s; outline: none;
    }
    .topic-input:focus { border-color: var(--accent); }
    .topic-hint { font-size: 0.75rem; color: var(--muted); margin-top: 0.4rem; }

    /* ── BUTTONS ── */
    .btn-primary {
      background: var(--ink); color: var(--bg);
      padding: 0.85rem 2rem; border-radius: 100px;
      border: none; font-size: 0.9rem; font-weight: 500;
      cursor: pointer; transition: all 0.18s;
      font-family: 'DM Sans', sans-serif; display: inline-flex;
      align-items: center; gap: 0.5rem;
    }
    .btn-primary:hover:not(:disabled) { background: var(--accent); transform: translateY(-1px); }
    .btn-primary:disabled { opacity: 0.45; cursor: not-allowed; }
    .btn-secondary {
      background: var(--card); color: var(--ink);
      padding: 0.85rem 1.5rem; border-radius: 100px;
      border: 1px solid var(--border); font-size: 0.9rem;
      cursor: pointer; transition: all 0.18s;
      font-family: 'DM Sans', sans-serif;
    }
    .btn-secondary:hover { border-color: var(--ink); }

    /* ── ANALYSIS PROGRESS ── */
    .analysis-card {
      background: var(--white); border: 1px solid var(--border);
      border-radius: 18px; padding: 2rem; text-align: center;
    }
    .analysis-spinner {
      width: 48px; height: 48px; margin: 0 auto 1rem;
      border: 3px solid var(--border); border-top-color: var(--accent);
      border-radius: 50%; animation: spin 0.8s linear infinite;
    }
    @keyframes spin { to{transform:rotate(360deg)} }
    .analysis-steps { margin-top: 1.5rem; display: flex; flex-direction: column; gap: 0.5rem; }
    .analysis-step {
      display: flex; align-items: center; gap: 0.75rem;
      font-size: 0.85rem; color: var(--muted); padding: 0.5rem 1rem;
      border-radius: 8px; background: var(--bg);
      transition: all 0.3s;
    }
    .analysis-step.active { color: var(--ink); background: var(--accent-light); border-left: 3px solid var(--accent); }
    .analysis-step.done { color: var(--accent); }
    #astep1.active { background: var(--accent-light); border-left: 3px solid var(--accent); }
    #astep2.active { background: #FEF9E7; border-left: 3px solid #B7770D; color: #7A5010; }
    #astep3.active { background: var(--accent3-light); border-left: 3px solid var(--accent3); color: #2E8A72; }
    #astep4.active { background: var(--accent2-light); border-left: 3px solid var(--accent2); color: #8A5020; }
    .step-icon { font-size: 1rem; }

    /* ── RESULTS ── */
    .results-header { display: flex; align-items: flex-start; justify-content: space-between; flex-wrap: wrap; gap: 1rem; margin-bottom: 1.5rem; }
    .results-meta { font-size: 0.78rem; color: var(--muted); margin-top: 0.3rem; }
    .results-meta span { margin-right: 1rem; }

    .output-tabs { display: flex; gap: 0.4rem; margin-bottom: 1rem; flex-wrap: wrap; }
    .tab-btn {
      padding: 0.4rem 1rem; border-radius: 100px;
      border: 1px solid var(--border); background: none;
      font-size: 0.8rem; cursor: pointer; transition: all 0.18s;
      font-family: 'DM Sans', sans-serif; color: var(--muted);
    }
    .tab-btn.active { background: var(--ink); color: var(--bg); border-color: var(--ink); }

    .output-area {
      background: var(--white); border: 1px solid var(--border);
      border-radius: 16px; padding: 1.5rem;
      font-size: 0.88rem; line-height: 1.75;
      min-height: 280px; white-space: pre-wrap;
      position: relative; overflow: hidden;
    }
    .output-area[contenteditable="true"] { outline: none; cursor: text; }
    .output-area[contenteditable="true"]:focus { border-color: var(--accent); }
    .edit-hint {
      position: absolute; top: 0.75rem; right: 0.75rem;
      font-size: 0.72rem; color: var(--muted); background: var(--bg);
      padding: 0.2rem 0.6rem; border-radius: 100px; border: 1px solid var(--border);
      pointer-events: none;
    }

    /* ── CUSTOMIZE ── */
    .customize-bar {
      background: var(--card); border-radius: 14px;
      padding: 1rem 1.2rem; margin-bottom: 1rem;
      display: flex; align-items: center; gap: 1.5rem; flex-wrap: wrap;
    }
    .customize-group { display: flex; align-items: center; gap: 0.5rem; }
    .customize-label { font-size: 0.75rem; color: var(--muted); white-space: nowrap; }
    .customize-select, .customize-input {
      padding: 0.3rem 0.6rem; border-radius: 8px;
      border: 1px solid var(--border); background: var(--white);
      font-size: 0.8rem; font-family: 'DM Sans', sans-serif; color: var(--ink);
      outline: none; transition: border-color 0.18s;
    }
    .customize-select:focus, .customize-input:focus { border-color: var(--accent); }
    .customize-input { width: 60px; text-align: center; }

    /* ── EXPORT ── */
    .export-section { margin-top: 1.5rem; }
    .export-label { font-size: 0.78rem; font-weight: 500; color: var(--muted); text-transform: uppercase; letter-spacing: 0.08em; margin-bottom: 0.75rem; }
    .export-grid { display: flex; flex-wrap: wrap; gap: 0.5rem; }
    .export-btn {
      display: flex; align-items: center; gap: 0.4rem;
      padding: 0.5rem 1rem; border-radius: 100px;
      border: 1px solid var(--border); background: var(--white);
      font-size: 0.82rem; cursor: pointer; transition: all 0.18s;
      font-family: 'DM Sans', sans-serif; color: var(--ink);
    }
    .export-btn:hover { transform: translateY(-1px); box-shadow: 0 2px 12px rgba(0,0,0,0.07); }
    .export-btn.pdf:hover  { border-color: #E05B5B; background: #FEF2F2; color: #E05B5B; }
    .export-btn.docx:hover { border-color: #5B6AF0; background: var(--accent-light); color: var(--accent); }
    .export-btn.md:hover   { border-color: #1C1B2E; background: #F0EFF8; color: #1C1B2E; }
    .export-btn.txt:hover  { border-color: var(--muted); background: var(--card); color: var(--ink); }
    .export-btn.csv:hover    { border-color: #56C4A8; background: var(--accent3-light); color: #379B84; }
    .export-btn.sheets:hover { border-color: #1A9E5A; background: #E8F8EF; color: #1A9E5A; }
    .export-btn.json:hover   { border-color: #E8A87C; background: var(--accent2-light); color: #B5712A; }
    .export-btn.loading { opacity: 0.5; pointer-events: none; }

    /* ── FOOTER ── */
    footer {
      border-top: 1px solid var(--border);
      padding: 1.2rem 2rem; text-align: center;
      font-size: 0.78rem; color: var(--muted);
    }

    /* ── RESPONSIVE ── */
    @media(max-width:600px) {
      main { padding: 1.5rem 1rem 3rem; }
      .steps-bar { display: none; }
      .results-header { flex-direction: column; }
      .customize-bar { gap: 0.8rem; }
    }
  </style>
</head>
<body>
<div class="app" id="app">

  <nav>
    <div class="logo">Simplify<span>.</span></div>
    <div class="nav-status">
      <div class="status-dot" id="statusDot"></div>
      <span id="statusText">Ready</span>
    </div>
  </nav>

  <main>
    <!-- Steps bar -->
    <div class="steps-bar">
      <div class="step-item active" id="step1">
        <div class="step-num">1</div>
        <span class="step-label">Upload files</span>
      </div>
      <span class="step-arrow">›</span>
      <div class="step-item" id="step2">
        <div class="step-num">2</div>
        <span class="step-label">Set topic</span>
      </div>
      <span class="step-arrow">›</span>
      <div class="step-item" id="step3">
        <div class="step-num">3</div>
        <span class="step-label">Analyze</span>
      </div>
      <span class="step-arrow">›</span>
      <div class="step-item" id="step4">
        <div class="step-num">4</div>
        <span class="step-label">Export</span>
      </div>
    </div>

    <!-- ① UPLOAD PANEL -->
    <div class="panel" id="panelUpload">
      <h1 class="panel-title">Upload your files</h1>
      <p class="panel-sub">Drop everything in first — ChatGPT exports, docs, notes. No sorting needed.</p>

      <div class="integrations-label">Connect a platform</div>
      <div class="integration-grid">
        <button class="integration-btn" onclick="connectIntegration(this, 'ChatGPT')"><span class="integration-icon">🤖</span>ChatGPT</button>
        <button class="integration-btn" onclick="connectIntegration(this, 'Claude')"><span class="integration-icon">✦</span>Claude</button>
        <button class="integration-btn" onclick="connectIntegration(this, 'Gemini')"><span class="integration-icon">💎</span>Gemini</button>
        <button class="integration-btn" onclick="connectIntegration(this, 'Copilot')"><span class="integration-icon">🪟</span>Copilot</button>
        <button class="integration-btn" onclick="connectIntegration(this, 'KIMI')"><span class="integration-icon">🌙</span>KIMI</button>
        <button class="integration-btn" onclick="connectIntegration(this, 'Bolt')"><span class="integration-icon">⚡</span>Bolt</button>
        <button class="integration-btn" onclick="connectIntegration(this, 'Lovable')"><span class="integration-icon">💜</span>Lovable</button>
        <button class="integration-btn" onclick="connectIntegration(this, 'Replit')"><span class="integration-icon">🔁</span>Replit</button>
        <button class="integration-btn" onclick="connectIntegration(this, 'GitHub')"><span class="integration-icon">🐙</span>GitHub</button>
      </div>

      <div class="drop-zone" id="dropZone">
        <input type="file" id="fileInput" multiple accept=".txt,.pdf,.docx,.md,.json,.csv,.html,.py,.js"/>
        <div class="drop-icon">📂</div>
        <h3>Drop your files here</h3>
        <p>or <u style="cursor:pointer" onclick="document.getElementById('fileInput').click()">click to browse</u></p>
        <p class="drop-types">TXT · PDF · DOCX · MD · JSON · CSV · HTML · PY · JS</p>
      </div>

      <div class="file-list" id="fileList"></div>

      <button class="btn-primary" id="btnNext1" onclick="goToStep2()" disabled>
        Continue <span>→</span>
      </button>
    </div>

    <!-- ② TOPIC PANEL -->
    <div class="panel" id="panelTopic" style="disp
    from flask import Flask, request, jsonify, send_file
from flask_cors import CORS
import os, json, uuid, re, time, io
from werkzeug.utils import secure_filename

# PDF
from reportlab.lib.pagesizes import letter
from reportlab.lib.styles import getSampleStyleSheet, ParagraphStyle
from reportlab.lib.units import inch
from reportlab.platypus import SimpleDocTemplate, Paragraph, Spacer
from reportlab.lib.enums import TA_LEFT

# DOCX
from docx import Document
from docx.shared import Pt

app = Flask(__name__)
CORS(app)

UPLOAD_FOLDER = '/tmp/simplify_uploads'
OUTPUT_FOLDER = '/tmp/simplify_outputs'
os.makedirs(UPLOAD_FOLDER, exist_ok=True)
os.makedirs(OUTPUT_FOLDER, exist_ok=True)

app.config['UPLOAD_FOLDER'] = UPLOAD_FOLDER
app.config['MAX_CONTENT_LENGTH'] = 50 * 1024 * 1024  # 50MB

sessions = {}  # in-memory session store

ALLOWED_EXTENSIONS = {'txt', 'pdf', 'docx', 'md', 'json', 'csv', 'html', 'py', 'js'}

def allowed_file(filename):
    return '.' in filename and filename.rsplit('.', 1)[1].lower() in ALLOWED_EXTENSIONS

def read_file_content(filepath, filename):
    ext = filename.rsplit('.', 1)[-1].lower()
    try:
        if ext in ['txt', 'md', 'html', 'py', 'js', 'csv']:
            with open(filepath, 'r', encoding='utf-8', errors='ignore') as f:
                return f.read()
        elif ext == 'json':
            with open(filepath, 'r', encoding='utf-8', errors='ignore') as f:
                data = json.load(f)
                # Handle ChatGPT export format
                if isinstance(data, list):
                    messages = []
                    for item in data:
                        if isinstance(item, dict):
                            if 'mapping' in item:
                                for k, v in item['mapping'].items():
                                    if v and 'message' in v and v['message']:
                                        msg = v['message']
                                        role = msg.get('author', {}).get('role', 'unknown')
                                        parts = msg.get('content', {}).get('parts', [])
                                        for p in parts:
                                            if isinstance(p, str) and p.strip():
                                                messages.append(f"[{role.upper()}]: {p}")
                            elif 'role' in item and 'content' in item:
                                messages.append(f"[{item['role'].upper()}]: {item['content']}")
                    return '\n\n'.join(messages) if messages else json.dumps(data, indent=2)
                return json.dumps(data, indent=2)
        elif ext == 'docx':
            from docx import Document as DocxDocument
            doc = DocxDocument(filepath)
            return '\n'.join([p.text for p in doc.paragraphs if p.text.strip()])
        elif ext == 'pdf':
            import pdfplumber
            with pdfplumber.open(filepath) as pdf:
                return '\n'.join([page.extract_text() or '' for page in pdf.pages])
        else:
            with open(filepath, 'r', encoding='utf-8', errors='ignore') as f:
                return f.read()
    except Exception as e:
        return f"[Could not read file: {str(e)}]"

def extract_relevant_content(text, topic, pass_num=1):
    """
    Extract content relevant to the topic using keyword matching and context windows.
    Pass 1: broad extraction
    Pass 2: refine and verify nothing was missed
    """
    if not topic or not text:
        return text

    topic_lower = topic.lower()
    topic_words = [w.strip() for w in re.split(r'[\s,]+', topic_lower) if len(w.strip()) > 2]

    lines = text.split('\n')
    relevant_lines = []
    context_window = 3  # lines of context around a match

    matched_indices = set()
    for i, line in enumerate(lines):
        line_lower = line.lower()
        if any(word in line_lower for word in topic_words):
            for j in range(max(0, i - context_window), min(len(lines), i + context_window + 1)):
                matched_indices.add(j)

    if pass_num == 2:
        # Second pass: also catch partial matches and related terms
        for i, line in enumerate(lines):
            line_lower = line.lower()
            for word in topic_words:
                if len(word) > 4 and word[:4] in line_lower:
                    for j in range(max(0, i - context_window), min(len(lines), i + context_window + 1)):
                        matched_indices.add(j)

    for idx in sorted(matched_indices):
        relevant_lines.append(lines[idx])

    result = '\n'.join(relevant_lines).strip()
    return result if result else f"No content found related to '{topic}' in the uploaded files."

def format_as_bullets(text):
    lines = [l.strip() for l in text.split('\n') if l.strip()]
    return '\n'.join([f"• {l}" if not l.startswith('•') and not l.startswith('[') else l for l in lines])

def format_as_outline(text):
    lines = [l.strip() for l in text.split('\n') if l.strip()]
    result = []
    for i, line in enumerate(lines):
        if line.startswith('[') and ']:' in line:
            result.append(f"\n## {line}")
        else:
            result.append(f"  {i+1}. {line}" if not line.startswith('•') else f"  {line}")
    return '\n'.join(result)

# ─────────────────────────────────────────────
# ROUTES
# ─────────────────────────────────────────────

@app.route('/api/session', methods=['POST'])
def create_session():
    sid = str(uuid.uuid4())
    sessions[sid] = {'files': [], 'analyzed': False, 'results': None}
    return jsonify({'session_id': sid})

@app.route('/api/upload', methods=['POST'])
def upload_file():
    sid = request.form.get('session_id')
    if not sid or sid not in sessions:
        return jsonify({'error': 'Invalid session'}), 400

    if 'file' not in request.files:
        return jsonify({'error': 'No file provided'}), 400

    file = request.files['file']
    if file.filename == '':
        return jsonify({'error': 'Empty filename'}), 400

    filename = secure_filename(file.filename)
    file_id = str(uuid.uuid4())
    filepath = os.path.join(UPLOAD_FOLDER, f"{file_id}_{filename}")
    file.save(filepath)

    file_info = {
        'id': file_id,
        'name': filename,
        'path': filepath,
        'size': os.path.getsize(filepath),
        'uploaded_at': time.time()
    }
    sessions[sid]['files'].append(file_info)

    return jsonify({'success': True, 'file': {'id': file_id, 'name': filename, 'size': file_info['size']}})

@app.route('/api/delete-file', methods=['POST'])
def delete_file():
    data = request.json
    sid = data.get('session_id')
    file_id = data.get('file_id')

    if not sid or sid not in sessions:
        return jsonify({'error': 'Invalid session'}), 400

    session = sessions[sid]
    file_to_delete = next((f for f in session['files'] if f['id'] == file_id), None)

    if not file_to_delete:
        return jsonify({'error': 'File not found'}), 404

    # Remove from disk
    try:
        if os.path.exists(file_to_delete['path']):
            os.remove(file_to_delete['path'])
    except:
        pass

    session['files'] = [f for f in session['files'] if f['id'] != file_id]
    return jsonify({'success': True})

@app.route('/api/analyze', methods=['POST'])
def analyze():
    data = request.json
    sid = data.get('session_id')
    topic = data.get('topic', '').strip()

    if not sid or sid not in sessions:
        return jsonify({'error': 'Invalid session'}), 400

    session = sessions[sid]
    if not session['files']:
        return jsonify({'error': 'No files uploaded'}), 400

    # Read all files
    all_content = []
    for f in session['files']:
        content = read_file_content(f['path'], f['name'])
        all_content.append(f"=== {f['name']} ===\n{content}")

    combined = '\n\n'.join(all_content)

    # Pass 1
    pass1 = extract_relevant_content(combined, topic, pass_num=1)
    # Pass 2 - verification pass
    pass2 = extract_relevant_content(combined, topic, pass_num=2)

    # Merge both passes (deduplicate lines)
    seen = set()
    merged_lines = []
    for line in (pass1 + '\n' + pass2).split('\n'):
        if line not in seen:
            seen.add(line)
            merged_lines.append(line)
    final_content = '\n'.join(merged_lines).strip()

    result = {
        'raw': final_content,
        'bullets': format_as_bullets(final_content),
        'outline': format_as_outline(final_content),
        'topic': topic,
        'file_count': len(session['files']),
        'char_count': len(final_content)
    }

    session['analyzed'] = True
    session['results'] = result

    return jsonify({'success': True, 'results': result})

@app.route('/api/edit-section', methods=['POST'])
def edit_section():
    """Targeted re-analysis of a specific section only"""
    data = request.json
    sid = data.get('session_id')
    original_text = data.get('original_text', '')
    selected_text = data.get('selected_text', '')
    correction = data.get('correction', '')

    if not sid or sid not in sessions:
        return jsonify({'error': 'Invalid session'}), 400

    # Replace only the selected portion with the correction
    updated = original_text.replace(selected_text, correction, 1)
    return jsonify({'success': True, 'updated_text': updated})

@app.route('/api/export', methods=['POST'])
def export():
    data = request.json
    sid = data.get('session_id')
    fmt = data.get('format', 'txt')
    content = data.get('content', '')
    style = data.get('style', {})

    font_size = int(style.get('fontSize', 12))
    font_family = style.get('fontFamily', 'Helvetica')
    line_spacing = float(style.get('lineSpacing', 1.5))
    topic = data.get('topic', 'output')

    safe_topic = re.sub(r'[^a-zA-Z0-9_-]', '_', topic)[:30]
    out_filename = f"simplify_{safe_topic}"

    if fmt == 'txt':
        buf = io.BytesIO(content.encode('utf-8'))
        buf.seek(0)
        return send_file(buf, as_attachment=True, download_name=f"{out_filename}.txt", mimetype='text/plain')

    elif fmt == 'md':
        md_content = f"# {topic}\n\n{content}"
        buf = io.BytesIO(md_content.encode('utf-8'))
        buf.seek(0)
        return send_file(buf, as_attachment=True, download_name=f"{out_filename}.md", mimetype='text/markdown')

    elif fmt == 'json':
        obj = {'topic': topic, 'content': content, 'lines': content.split('\n'), 'generated_at': time.time()}
        buf = io.BytesIO(json.dumps(obj, indent=2).encode('utf-8'))
        buf.seek(0)
        return send_file(buf, as_attachment=True, download_name=f"{out_filename}.json", mimetype='application/json')

    elif fmt == 'csv':
        lines = [l for l in content.split('\n') if l.strip()]
        csv_rows = ['line_number,content']
        for i, line in enumerate(lines, 1):
            escaped = line.replace('"', '""')
            csv_rows.append(f'{i},"{escaped}"')
        buf = io.BytesIO('\n'.join(csv_rows).encode('utf-8'))
        buf.seek(0)
        return send_file(buf, as_attachment=True, download_name=f"{out_filename}.csv", mimetype='text/csv')

    elif fmt == 'pdf':
        buf = io.BytesIO()
        doc = SimpleDocTemplate(buf, pagesize=letter,
                                leftMargin=inch, rightMargin=inch,
                                topMargin=inch, bottomMargin=inch)
        styles = getSampleStyleSheet()
        title_style = ParagraphStyle('Title', fontSize=font_size + 6, fontName=font_family,
                                     spaceAfter=20, alignment=TA_LEFT, textColor='#1A1A18')
        body_style = ParagraphStyle('Body', fontSize=font_size, fontName=font_family,
                                    leading=font_size * line_spacing, spaceAfter=8,
                                    alignment=TA_LEFT, textColor='#1A1A18')
        story = [Paragraph(f"<b>{topic}</b>", title_style), Spacer(1, 0.2 * inch)]
        for line in content.split('\n'):
            if line.strip():
                safe_line = line.replace('&', '&amp;').replace('<', '&lt;').replace('>', '&gt;')
                story.append(Paragraph(safe_line, body_style))
        doc.build(story)
        buf.seek(0)
        return send_file(buf, as_attachment=True, download_name=f"{out_filename}.pdf", mimetype='application/pdf')

    elif fmt == 'docx':
        doc = Document()
        doc.add_heading(topic, 0)
        for line in content.split('\n'):
            if line.strip():
                p = doc.add_paragraph(line)
                run = p.runs[0] if p.runs else p.add_run(line)
                run.font.size = Pt(font_size)
        buf = io.BytesIO()
        doc.save(buf)
        buf.seek(0)
        return send_file(buf, as_attachment=True,
                         download_name=f"{out_filename}.docx",
                         mimetype='application/vnd.openxmlformats-officedocument.wordprocessingml.document')

    return jsonify({'error': 'Unsupported format'}), 400

@app.route('/api/health', methods=['GET'])
def health():
    return jsonify({'status': 'ok'})

if __name__ == '__main__':
    app.run(debug=True, port=5000)
    <!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8"/>
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Simplify — Extract What Matters</title>
  <link href="https://fonts.googleapis.com/css2?family=DM+Serif+Display:ital@0;1&family=DM+Sans:ital,wght@0,300;0,400;0,500;1,300&display=swap" rel="stylesheet"/>
  <style>
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
    :root {
      --bg: #F8F7FF;
      --ink: #1C1B2E;
      --accent: #5B6AF0;
      --accent-light: #EDEFFD;
      --accent2: #E8A87C;
      --accent2-light: #FDF3EC;
      --accent3: #56C4A8;
      --accent3-light: #E8F8F4;
      --muted: #8F8FA8;
      --border: #E2E1F0;
      --card: #EFEEF8;
      --danger: #E05B5B;
      --warning: #E8A87C;
      --white: #FDFCFF;
    }
    html { scroll-behavior: smooth; }
    body {
      background: var(--bg);
      color: var(--ink);
      font-family: 'DM Sans', sans-serif;
      font-weight: 300;
      line-height: 1.6;
      -webkit-font-smoothing: antialiased;
      min-height: 100vh;
    }

    /* ── LAYOUT ── */
    .app { display: flex; flex-direction: column; min-height: 100vh; }

    /* ── NAV ── */
    nav {
      position: sticky; top: 0; z-index: 200;
      background: var(--white);
      border-bottom: 1px solid var(--border);
      border-top: 3px solid var(--accent);
      display: flex; align-items: center; justify-content: space-between;
      padding: 1rem 2rem;
    }
    .logo { font-family: 'DM Serif Display', serif; font-size: 1.3rem; letter-spacing: -0.02em; }
    .logo span { color: var(--accent); font-style: italic; }
    .nav-status { font-size: 0.78rem; color: var(--muted); display: flex; align-items: center; gap: 0.5rem; }
    .status-dot { width: 7px; height: 7px; border-radius: 50%; background: var(--border); }
    .status-dot.active { background: var(--accent); animation: pulse 2s infinite; }
    @keyframes pulse { 0%,100%{opacity:1} 50%{opacity:0.4} }

    /* ── MAIN ── */
    main { flex: 1; max-width: 960px; width: 100%; margin: 0 auto; padding: 2.5rem 1.5rem 4rem; }

    /* ── STEPS BAR ── */
    .steps-bar {
      display: flex; align-items: center; gap: 0; margin-bottom: 2.5rem;
      background: linear-gradient(135deg, var(--accent-light) 0%, var(--accent3-light) 100%);
      border-radius: 14px; padding: 0.6rem 1rem;
      overflow: hidden; border: 1px solid var(--border);
    }
    .step-item { display: flex; align-items: center; gap: 0.5rem; flex: 1; }
    .step-num {
      width: 26px; height: 26px; border-radius: 50%;
      display: flex; align-items: center; justify-content: center;
      font-size: 0.72rem; font-weight: 500;
      background: var(--border); color: var(--muted);
      transition: all 0.3s; flex-shrink: 0;
    }
    .step-item.active .step-num { background: var(--accent); color: #fff; }
    .step-item.done .step-num { background: var(--ink); color: #fff; }
    .step-label { font-size: 0.8rem; color: var(--muted); white-space: nowrap; }
    .step-item.active .step-label { color: var(--ink); font-weight: 500; }
    .step-item.done .step-label { color: var(--ink); }
    .step-arrow { color: var(--border); font-size: 0.8rem; margin: 0 0.5rem; }

    /* ── PANELS ── */
    .panel { animation: fadeUp 0.4s ease both; }
    @keyframes fadeUp { from{opacity:0;transform:translateY(16px)} to{opacity:1;transform:none} }

    .panel-title {
      font-family: 'DM Serif Display', serif;
      font-size: 1.8rem; letter-spacing: -0.02em;
      margin-bottom: 0.4rem;
    }
    .panel-sub { font-size: 0.9rem; color: var(--muted); margin-bottom: 2rem; }

    /* ── INTEGRATIONS ── */
    .integrations-label { font-size: 0.72rem; font-weight: 500; letter-spacing: 0.1em; text-transform: uppercase; color: var(--muted); margin-bottom: 0.8rem; }
    .integration-grid { display: flex; flex-wrap: wrap; gap: 0.5rem; margin-bottom: 2rem; }
    .integration-btn {
      display: flex; align-items: center; gap: 0.5rem;
      padding: 0.45rem 0.9rem; border-radius: 100px;
      border: 1px solid var(--border); background: var(--white);
      font-size: 0.8rem; cursor: pointer; transition: all 0.18s;
      font-family: 'DM Sans', sans-serif; color: var(--ink);
    }
    .integration-btn:hover { border-color: var(--accent); background: var(--accent-light); }
    .integration-btn.connected { border-color: var(--accent); background: var(--accent-light); color: var(--accent); }
    .integration-icon { font-size: 1rem; }

    /* ── DROP ZONE ── */
    .drop-zone {
      border: 1.5px dashed var(--border); border-radius: 18px;
      padding: 2.5rem; text-align: center; cursor: pointer;
      background: var(--white); transition: all 0.2s; margin-bottom: 1.5rem;
      position: relative;
    }
    .drop-zone:hover, .drop-zone.over { border-color: var(--accent); background: var(--accent-light); }
    .drop-icon { font-size: 2rem; margin-bottom: 0.8rem; }
    .drop-zone h3 { font-size: 1rem; font-weight: 500; margin-bottom: 0.3rem; }
    .drop-zone p { font-size: 0.83rem; color: var(--muted); }
    .drop-types { margin-top: 0.5rem; font-size: 0.72rem; color: var(--border); letter-spacing: 0.08em; }
    #fileInput { display: none; }

    /* ── FILE LIST ── */
    .file-list { display: flex; flex-direction: column; gap: 0.5rem; margin-bottom: 1.5rem; }
    .file-item {
      display: flex; align-items: center; gap: 1rem;
      background: var(--white); border: 1px solid var(--border);
      border-radius: 12px; padding: 0.75rem 1rem;
      transition: border-color 0.2s;
      animation: popIn 0.2s ease both;
    }
    @keyframes popIn { from{opacity:0;transform:scale(0.96)} to{opacity:1;transform:none} }
    .file-item.deleting { opacity: 0.4; pointer-events: none; }
    .file-ext {
      width: 36px; height: 36px; border-radius: 8px;
      background: var(--accent-light); display: flex; align-items: center; justify-content: center;
      font-size: 0.7rem; font-weight: 500; color: var(--accent); flex-shrink: 0;
      text-transform: uppercase; letter-spacing: 0.05em;
    }
    .file-ext.pdf  { background: #FEF2F2; color: #E05B5B; }
    .file-ext.doc  { background: var(--accent-light); color: var(--accent); }
    .file-ext.docx { background: var(--accent-light); color: var(--accent); }
    .file-ext.json { background: var(--accent2-light); color: #B5712A; }
    .file-ext.csv  { background: var(--accent3-light); color: #379B84; }
    .file-ext.md   { background: #F0EFF8; color: #1C1B2E; }
    .file-ext.txt  { background: var(--card); color: var(--muted); }
    .file-ext.py   { background: #FEF9E7; color: #B7770D; }
    .file-ext.js   { background: #FFFDE7; color: #9A7500; }
    .file-info { flex: 1; min-width: 0; }
    .file-name { font-size: 0.88rem; font-weight: 400; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
    .file-size { font-size: 0.75rem; color: var(--muted); }
    .file-delete-btn {
      width: 30px; height: 30px; border-radius: 50%;
      border: 1px solid var(--border); background: none;
      display: flex; align-items: center; justify-content: center;
      cursor: pointer; font-size: 0.9rem; color: var(--muted);
      transition: all 0.18s; flex-shrink: 0;
    }
    .file-delete-btn:hover { border-color: var(--danger); color: var(--danger); background: #fdf2f2; }

    /* ── UNDO TOAST ── */
    .undo-toast {
      position: fixed; bottom: 2rem; left: 50%; transform: translateX(-50%);
      background: var(--ink); color: var(--bg);
      padding: 0.8rem 1.5rem; border-radius: 100px;
      font-size: 0.85rem; display: flex; align-items: center; gap: 1rem;
      z-index: 999; animation: slideUp 0.3s ease;
      box-shadow: 0 8px 24px rgba(0,0,0,0.2);
    }
    @keyframes slideUp { from{opacity:0;transform:translateX(-50%) translateY(12px)} to{opacity:1;transform:translateX(-50%) translateY(0)} }
    .undo-btn {
      background: var(--accent3); color: #fff;
      border: none; border-radius: 100px; padding: 0.3rem 0.9rem;
      font-size: 0.8rem; cursor: pointer; font-family: 'DM Sans', sans-serif;
    }
    .undo-bar {
      position: absolute; bottom: 0; left: 0; height: 3px;
      background: var(--accent3); border-radius: 0 0 100px 100px;
      animation: shrink 4s linear forwards;
    }
    @keyframes shrink { from{width:100%} to{width:0%} }

    /* ── CONFIRM DIALOG ── */
    .overlay {
      position: fixed; inset: 0; background: rgba(0,0,0,0.35);
      display: flex; align-items: center; justify-content: center;
      z-index: 998; animation: fadeIn 0.2s ease;
    }
    @keyframes fadeIn { from{opacity:0} to{opacity:1} }
    .dialog {
      background: var(--white); border-radius: 20px;
      padding: 2rem; max-width: 360px; width: 90%;
      text-align: center; box-shadow: 0 20px 60px rgba(0,0,0,0.15);
      animation: popIn 0.2s ease;
    }
    .dialog h3 { font-size: 1.1rem; font-weight: 500; margin-bottom: 0.5rem; }
    .dialog p { font-size: 0.85rem; color: var(--muted); margin-bottom: 1.5rem; }
    .dialog-actions { display: flex; gap: 0.75rem; justify-content: center; }
    .btn-cancel {
      padding: 0.6rem 1.4rem; border-radius: 100px;
      border: 1px solid var(--border); background: none;
      font-size: 0.85rem; cursor: pointer; font-family: 'DM Sans', sans-serif;
      transition: all 0.18s;
    }
    .btn-cancel:hover { border-color: var(--ink); }
    .btn-delete {
      padding: 0.6rem 1.4rem; border-radius: 100px;
      background: var(--danger); color: #fff; border: none;
      font-size: 0.85rem; cursor: pointer; font-family: 'DM Sans', sans-serif;
      transition: opacity 0.18s;
    }
    .btn-delete:hover { opacity: 0.85; }

    /* ── TOPIC INPUT ── */
    .topic-section { margin-bottom: 2rem; }
    .topic-label { font-size: 0.8rem; font-weight: 500; margin-bottom: 0.5rem; display: block; }
    .topic-input {
      width: 100%; padding: 0.9rem 1.2rem;
      border: 1.5px solid var(--border); border-radius: 14px;
      background: var(--white); font-size: 0.95rem;
      font-family: 'DM Sans', sans-serif; color: var(--ink);
      transition: border-color 0.2s; outline: none;
    }
    .topic-input:focus { border-color: var(--accent); }
    .topic-hint { font-size: 0.75rem; color: var(--muted); margin-top: 0.4rem; }

    /* ── BUTTONS ── */
    .btn-primary {
      background: var(--ink); color: var(--bg);
      padding: 0.85rem 2rem; border-radius: 100px;
      border: none; font-size: 0.9rem; font-weight: 500;
      cursor: pointer; transition: all 0.18s;
      font-family: 'DM Sans', sans-serif; display: inline-flex;
      align-items: center; gap: 0.5rem;
    }
    .btn-primary:hover:not(:disabled) { background: var(--accent); transform: translateY(-1px); }
    .btn-primary:disabled { opacity: 0.45; cursor: not-allowed; }
    .btn-secondary {
      background: var(--card); color: var(--ink);
      padding: 0.85rem 1.5rem; border-radius: 100px;
      border: 1px solid var(--border); font-size: 0.9rem;
      cursor: pointer; transition: all 0.18s;
      font-family: 'DM Sans', sans-serif;
    }
    .btn-secondary:hover { border-color: var(--ink); }

    /* ── ANALYSIS PROGRESS ── */
    .analysis-card {
      background: var(--white); border: 1px solid var(--border);
      border-radius: 18px; padding: 2rem; text-align: center;
    }
    .analysis-spinner {
      width: 48px; height: 48px; margin: 0 auto 1rem;
      border: 3px solid var(--border); border-top-color: var(--accent);
      border-radius: 50%; animation: spin 0.8s linear infinite;
    }
    @keyframes spin { to{transform:rotate(360deg)} }
    .analysis-steps { margin-top: 1.5rem; display: flex; flex-direction: column; gap: 0.5rem; }
    .analysis-step {
      display: flex; align-items: center; gap: 0.75rem;
      font-size: 0.85rem; color: var(--muted); padding: 0.5rem 1rem;
      border-radius: 8px; background: var(--bg);
      transition: all 0.3s;
    }
    .analysis-step.active { color: var(--ink); background: var(--accent-light); border-left: 3px solid var(--accent); }
    .analysis-step.done { color: var(--accent); }
    #astep1.active { background: var(--accent-light); border-left: 3px solid var(--accent); }
    #astep2.active { background: #FEF9E7; border-left: 3px solid #B7770D; color: #7A5010; }
    #astep3.active { background: var(--accent3-light); border-left: 3px solid var(--accent3); color: #2E8A72; }
    #astep4.active { background: var(--accent2-light); border-left: 3px solid var(--accent2); color: #8A5020; }
    .step-icon { font-size: 1rem; }

    /* ── RESULTS ── */
    .results-header { display: flex; align-items: flex-start; justify-content: space-between; flex-wrap: wrap; gap: 1rem; margin-bottom: 1.5rem; }
    .results-meta { font-size: 0.78rem; color: var(--muted); margin-top: 0.3rem; }
    .results-meta span { margin-right: 1rem; }

    .output-tabs { display: flex; gap: 0.4rem; margin-bottom: 1rem; flex-wrap: wrap; }
    .tab-btn {
      padding: 0.4rem 1rem; border-radius: 100px;
      border: 1px solid var(--border); background: none;
      font-size: 0.8rem; cursor: pointer; transition: all 0.18s;
      font-family: 'DM Sans', sans-serif; color: var(--muted);
    }
    .tab-btn.active { background: var(--ink); color: var(--bg); border-color: var(--ink); }

    .output-area {
      background: var(--white); border: 1px solid var(--border);
      border-radius: 16px; padding: 1.5rem;
      font-size: 0.88rem; line-height: 1.75;
      min-height: 280px; white-space: pre-wrap;
      position: relative; overflow: hidden;
    }
    .output-area[contenteditable="true"] { outline: none; cursor: text; }
    .output-area[contenteditable="true"]:focus { border-color: var(--accent); }
    .edit-hint {
      position: absolute; top: 0.75rem; right: 0.75rem;
      font-size: 0.72rem; color: var(--muted); background: var(--bg);
      padding: 0.2rem 0.6rem; border-radius: 100px; border: 1px solid var(--border);
      pointer-events: none;
    }

    /* ── CUSTOMIZE ── */
    .customize-bar {
      background: var(--card); border-radius: 14px;
      padding: 1rem 1.2rem; margin-bottom: 1rem;
      display: flex; align-items: center; gap: 1.5rem; flex-wrap: wrap;
    }
    .customize-group { display: flex; align-items: center; gap: 0.5rem; }
    .customize-label { font-size: 0.75rem; color: var(--muted); white-space: nowrap; }
    .customize-select, .customize-input {
      padding: 0.3rem 0.6rem; border-radius: 8px;
      border: 1px solid var(--border); background: var(--white);
      font-size: 0.8rem; font-family: 'DM Sans', sans-serif; color: var(--ink);
      outline: none; transition: border-color 0.18s;
    }
    .customize-select:focus, .customize-input:focus { border-color: var(--accent); }
    .customize-input { width: 60px; text-align: center; }

    /* ── EXPORT ── */
    .export-section { margin-top: 1.5rem; }
    .export-label { font-size: 0.78rem; font-weight: 500; color: var(--muted); text-transform: uppercase; letter-spacing: 0.08em; margin-bottom: 0.75rem; }
    .export-grid { display: flex; flex-wrap: wrap; gap: 0.5rem; }
    .export-btn {
      display: flex; align-items: center; gap: 0.4rem;
      padding: 0.5rem 1rem; border-radius: 100px;
      border: 1px solid var(--border); background: var(--white);
      font-size: 0.82rem; cursor: pointer; transition: all 0.18s;
      font-family: 'DM Sans', sans-serif; color: var(--ink);
    }
    .export-btn:hover { transform: translateY(-1px); box-shadow: 0 2px 12px rgba(0,0,0,0.07); }
    .export-btn.pdf:hover  { border-color: #E05B5B; background: #FEF2F2; color: #E05B5B; }
    .export-btn.docx:hover { border-color: #5B6AF0; background: var(--accent-light); color: var(--accent); }
    .export-btn.md:hover   { border-color: #1C1B2E; background: #F0EFF8; color: #1C1B2E; }
    .export-btn.txt:hover  { border-color: var(--muted); background: var(--card); color: var(--ink); }
    .export-btn.csv:hover    { border-color: #56C4A8; background: var(--accent3-light); color: #379B84; }
    .export-btn.sheets:hover { border-color: #1A9E5A; background: #E8F8EF; color: #1A9E5A; }
    .export-btn.json:hover   { border-color: #E8A87C; background: var(--accent2-light); color: #B5712A; }
    .export-btn.loading { opacity: 0.5; pointer-events: none; }

    /* ── FOOTER ── */
    footer {
      border-top: 1px solid var(--border);
      padding: 1.2rem 2rem; text-align: center;
      font-size: 0.78rem; color: var(--muted);
    }

    /* ── RESPONSIVE ── */
    @media(max-width:600px) {
      main { padding: 1.5rem 1rem 3rem; }
      .steps-bar { display: none; }
      .results-header { flex-direction: column; }
      .customize-bar { gap: 0.8rem; }
    }
  </style>
</head>
<body>
<div class="app" id="app">

  <nav>
    <div class="logo">Simplify<span>.</span></div>
    <div class="nav-status">
      <div class="status-dot" id="statusDot"></div>
      <span id="statusText">Ready</span>
    </div>
  </nav>

  <main>
    <!-- Steps bar -->
    <div class="steps-bar">
      <div class="step-item active" id="step1">
        <div class="step-num">1</div>
        <span class="step-label">Upload files</span>
      </div>
      <span class="step-arrow">›</span>
      <div class="step-item" id="step2">
        <div class="step-num">2</div>
        <span class="step-label">Set topic</span>
      </div>
      <span class="step-arrow">›</span>
      <div class="step-item" id="step3">
        <div class="step-num">3</div>
        <span class="step-label">Analyze</span>
      </div>
      <span class="step-arrow">›</span>
      <div class="step-item" id="step4">
        <div class="step-num">4</div>
        <span class="step-label">Export</span>
      </div>
    </div>

    <!-- ① UPLOAD PANEL -->
    <div class="panel" id="panelUpload">
      <h1 class="panel-title">Upload your files</h1>
      <p class="panel-sub">Drop everything in first — ChatGPT exports, docs, notes. No sorting needed.</p>

      <div class="integrations-label">Connect a platform</div>
      <div class="integration-grid">
        <button class="integration-btn" onclick="connectIntegration(this, 'ChatGPT')"><span class="integration-icon">🤖</span>ChatGPT</button>
        <button class="integration-btn" onclick="connectIntegration(this, 'Claude')"><span class="integration-icon">✦</span>Claude</button>
        <button class="integration-btn" onclick="connectIntegration(this, 'Gemini')"><span class="integration-icon">💎</span>Gemini</button>
        <button class="integration-btn" onclick="connectIntegration(this, 'Copilot')"><span class="integration-icon">🪟</span>Copilot</button>
        <button class="integration-btn" onclick="connectIntegration(this, 'KIMI')"><span class="integration-icon">🌙</span>KIMI</button>
        <button class="integration-btn" onclick="connectIntegration(this, 'Bolt')"><span class="integration-icon">⚡</span>Bolt</button>
        <button class="integration-btn" onclick="connectIntegration(this, 'Lovable')"><span class="integration-icon">💜</span>Lovable</button>
        <button class="integration-btn" onclick="connectIntegration(this, 'Replit')"><span class="integration-icon">🔁</span>Replit</button>
        <button class="integration-btn" onclick="connectIntegration(this, 'GitHub')"><span class="integration-icon">🐙</span>GitHub</button>
      </div>

      <div class="drop-zone" id="dropZone">
        <input type="file" id="fileInput" multiple accept=".txt,.pdf,.docx,.md,.json,.csv,.html,.py,.js"/>
        <div class="drop-icon">📂</div>
        <h3>Drop your files here</h3>
        <p>or <u style="cursor:pointer" onclick="document.getElementById('fileInput').click()">click to browse</u></p>
        <p class="drop-types">TXT · PDF · DOCX · MD · JSON · CSV · HTML · PY · JS</p>
      </div>

      <div class="file-list" id="fileList"></div>

      <button class="btn-primary" id="btnNext1" onclick="goToStep2()" disabled>
        Continue <span>→</span>
      </button>
    </div>

    <!-- ② TOPIC PANEL -->
    <div class="panel" id="panelTopic" style="disp
    <!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8"/>
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Simplify — Extract What Matters</title>
  <link href="https://fonts.googleapis.com/css2?family=DM+Serif+Display:ital@0;1&family=DM+Sans:ital,wght@0,300;0,400;0,500;1,300&display=swap" rel="stylesheet"/>
  <style>
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
    :root {
      --bg: #F8F7FF;
      --ink: #1C1B2E;
      --accent: #5B6AF0;
      --accent-light: #EDEFFD;
      --accent2: #E8A87C;
      --accent2-light: #FDF3EC;
      --accent3: #56C4A8;
      --accent3-light: #E8F8F4;
      --muted: #8F8FA8;
      --border: #E2E1F0;
      --card: #EFEEF8;
      --danger: #E05B5B;
      --warning: #E8A87C;
      --white: #FDFCFF;
    }
    html { scroll-behavior: smooth; }
    body {
      background: var(--bg);
      color: var(--ink);
      font-family: 'DM Sans', sans-serif;
      font-weight: 300;
      line-height: 1.6;
      -webkit-font-smoothing: antialiased;
      min-height: 100vh;
    }

    /* ── LAYOUT ── */
    .app { display: flex; flex-direction: column; min-height: 100vh; }

    /* ── NAV ── */
    nav {
      position: sticky; top: 0; z-index: 200;
      background: var(--white);
      border-bottom: 1px solid var(--border);
      border-top: 3px solid var(--accent);
      display: flex; align-items: center; justify-content: space-between;
      padding: 1rem 2rem;
    }
    .logo { font-family: 'DM Serif Display', serif; font-size: 1.3rem; letter-spacing: -0.02em; }
    .logo span { color: var(--accent); font-style: italic; }
    .nav-status { font-size: 0.78rem; color: var(--muted); display: flex; align-items: center; gap: 0.5rem; }
    .status-dot { width: 7px; height: 7px; border-radius: 50%; background: var(--border); }
    .status-dot.active { background: var(--accent); animation: pulse 2s infinite; }
    @keyframes pulse { 0%,100%{opacity:1} 50%{opacity:0.4} }

    /* ── MAIN ── */
    main { flex: 1; max-width: 960px; width: 100%; margin: 0 auto; padding: 2.5rem 1.5rem 4rem; }

    /* ── STEPS BAR ── */
    .steps-bar {
      display: flex; align-items: center; gap: 0; margin-bottom: 2.5rem;
      background: linear-gradient(135deg, var(--accent-light) 0%, var(--accent3-light) 100%);
      border-radius: 14px; padding: 0.6rem 1rem;
      overflow: hidden; border: 1px solid var(--border);
    }
    .step-item { display: flex; align-items: center; gap: 0.5rem; flex: 1; }
    .step-num {
      width: 26px; height: 26px; border-radius: 50%;
      display: flex; align-items: center; justify-content: center;
      font-size: 0.72rem; font-weight: 500;
      background: var(--border); color: var(--muted);
      transition: all 0.3s; flex-shrink: 0;
    }
    .step-item.active .step-num { background: var(--accent); color: #fff; }
    .step-item.done .step-num { background: var(--ink); color: #fff; }
    .step-label { font-size: 0.8rem; color: var(--muted); white-space: nowrap; }
    .step-item.active .step-label { color: var(--ink); font-weight: 500; }
    .step-item.done .step-label { color: var(--ink); }
    .step-arrow { color: var(--border); font-size: 0.8rem; margin: 0 0.5rem; }

    /* ── PANELS ── */
    .panel { animation: fadeUp 0.4s ease both; }
    @keyframes fadeUp { from{opacity:0;transform:translateY(16px)} to{opacity:1;transform:none} }

    .panel-title {
      font-family: 'DM Serif Display', serif;
      font-size: 1.8rem; letter-spacing: -0.02em;
      margin-bottom: 0.4rem;
    }
    .panel-sub { font-size: 0.9rem; color: var(--muted); margin-bottom: 2rem; }

    /* ── INTEGRATIONS ── */
    .integrations-label { font-size: 0.72rem; font-weight: 500; letter-spacing: 0.1em; text-transform: uppercase; color: var(--muted); margin-bottom: 0.8rem; }
    .integration-grid { display: flex; flex-wrap: wrap; gap: 0.5rem; margin-bottom: 2rem; }
    .integration-btn {
      display: flex; align-items: center; gap: 0.5rem;
      padding: 0.45rem 0.9rem; border-radius: 100px;
      border: 1px solid var(--border); background: var(--white);
      font-size: 0.8rem; cursor: pointer; transition: all 0.18s;
      font-family: 'DM Sans', sans-serif; color: var(--ink);
    }
    .integration-btn:hover { border-color: var(--accent); background: var(--accent-light); }
    .integration-btn.connected { border-color: var(--accent); background: var(--accent-light); color: var(--accent); }
    .integration-icon { font-size: 1rem; }

    /* ── DROP ZONE ── */
    .drop-zone {
      border: 1.5px dashed var(--border); border-radius: 18px;
      padding: 2.5rem; text-align: center; cursor: pointer;
      background: var(--white); transition: all 0.2s; margin-bottom: 1.5rem;
      position: relative;
    }
    .drop-zone:hover, .drop-zone.over { border-color: var(--accent); background: var(--accent-light); }
    .drop-icon { font-size: 2rem; margin-bottom: 0.8rem; }
    .drop-zone h3 { font-size: 1rem; font-weight: 500; margin-bottom: 0.3rem; }
    .drop-zone p { font-size: 0.83rem; color: var(--muted); }
    .drop-types { margin-top: 0.5rem; font-size: 0.72rem; color: var(--border); letter-spacing: 0.08em; }
    #fileInput { display: none; }

    /* ── FILE LIST ── */
    .file-list { display: flex; flex-direction: column; gap: 0.5rem; margin-bottom: 1.5rem; }
    .file-item {
      display: flex; align-items: center; gap: 1rem;
      background: var(--white); border: 1px solid var(--border);
      border-radius: 12px; padding: 0.75rem 1rem;
      transition: border-color 0.2s;
      animation: popIn 0.2s ease both;
    }
    @keyframes popIn { from{opacity:0;transform:scale(0.96)} to{opacity:1;transform:none} }
    .file-item.deleting { opacity: 0.4; pointer-events: none; }
    .file-ext {
      width: 36px; height: 36px; border-radius: 8px;
      background: var(--accent-light); display: flex; align-items: center; justify-content: center;
      font-size: 0.7rem; font-weight: 500; color: var(--accent); flex-shrink: 0;
      text-transform: uppercase; letter-spacing: 0.05em;
    }
    .file-ext.pdf  { background: #FEF2F2; color: #E05B5B; }
    .file-ext.doc  { background: var(--accent-light); color: var(--accent); }
    .file-ext.docx { background: var(--accent-light); color: var(--accent); }
    .file-ext.json { background: var(--accent2-light); color: #B5712A; }
    .file-ext.csv  { background: var(--accent3-light); color: #379B84; }
    .file-ext.md   { background: #F0EFF8; color: #1C1B2E; }
    .file-ext.txt  { background: var(--card); color: var(--muted); }
    .file-ext.py   { background: #FEF9E7; color: #B7770D; }
    .file-ext.js   { background: #FFFDE7; color: #9A7500; }
    .file-info { flex: 1; min-width: 0; }
    .file-name { font-size: 0.88rem; font-weight: 400; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
    .file-size { font-size: 0.75rem; color: var(--muted); }
    .file-delete-btn {
      width: 30px; height: 30px; border-radius: 50%;
      border: 1px solid var(--border); background: none;
      display: flex; align-items: center; justify-content: center;
      cursor: pointer; font-size: 0.9rem; color: var(--muted);
      transition: all 0.18s; flex-shrink: 0;
    }
    .file-delete-btn:hover { border-color: var(--danger); color: var(--danger); background: #fdf2f2; }

    /* ── UNDO TOAST ── */
    .undo-toast {
      position: fixed; bottom: 2rem; left: 50%; transform: translateX(-50%);
      background: var(--ink); color: var(--bg);
      padding: 0.8rem 1.5rem; border-radius: 100px;
      font-size: 0.85rem; display: flex; align-items: center; gap: 1rem;
      z-index: 999; animation: slideUp 0.3s ease;
      box-shadow: 0 8px 24px rgba(0,0,0,0.2);
    }
    @keyframes slideUp { from{opacity:0;transform:translateX(-50%) translateY(12px)} to{opacity:1;transform:translateX(-50%) translateY(0)} }
    .undo-btn {
      background: var(--accent3); color: #fff;
      border: none; border-radius: 100px; padding: 0.3rem 0.9rem;
      font-size: 0.8rem; cursor: pointer; font-family: 'DM Sans', sans-serif;
    }
    .undo-bar {
      position: absolute; bottom: 0; left: 0; height: 3px;
      background: var(--accent3); border-radius: 0 0 100px 100px;
      animation: shrink 4s linear forwards;
    }
    @keyframes shrink { from{width:100%} to{width:0%} }

    /* ── CONFIRM DIALOG ── */
    .overlay {
      position: fixed; inset: 0; background: rgba(0,0,0,0.35);
      display: flex; align-items: center; justify-content: center;
      z-index: 998; animation: fadeIn 0.2s ease;
    }
    @keyframes fadeIn { from{opacity:0} to{opacity:1} }
    .dialog {
      background: var(--white); border-radius: 20px;
      padding: 2rem; max-width: 360px; width: 90%;
      text-align: center; box-shadow: 0 20px 60px rgba(0,0,0,0.15);
      animation: popIn 0.2s ease;
    }
    .dialog h3 { font-size: 1.1rem; font-weight: 500; margin-bottom: 0.5rem; }
    .dialog p { font-size: 0.85rem; color: var(--muted); margin-bottom: 1.5rem; }
    .dialog-actions { display: flex; gap: 0.75rem; justify-content: center; }
    .btn-cancel {
      padding: 0.6rem 1.4rem; border-radius: 100px;
      border: 1px solid var(--border); background: none;
      font-size: 0.85rem; cursor: pointer; font-family: 'DM Sans', sans-serif;
      transition: all 0.18s;
    }
    .btn-cancel:hover { border-color: var(--ink); }
    .btn-delete {
      padding: 0.6rem 1.4rem; border-radius: 100px;
      background: var(--danger); color: #fff; border: none;
      font-size: 0.85rem; cursor: pointer; font-family: 'DM Sans', sans-serif;
      transition: opacity 0.18s;
    }
    .btn-delete:hover { opacity: 0.85; }

    /* ── TOPIC INPUT ── */
    .topic-section { margin-bottom: 2rem; }
    .topic-label { font-size: 0.8rem; font-weight: 500; margin-bottom: 0.5rem; display: block; }
    .topic-input {
      width: 100%; padding: 0.9rem 1.2rem;
      border: 1.5px solid var(--border); border-radius: 14px;
      background: var(--white); font-size: 0.95rem;
      font-family: 'DM Sans', sans-serif; color: var(--ink);
      transition: border-color 0.2s; outline: none;
    }
    .topic-input:focus { border-color: var(--accent); }
    .topic-hint { font-size: 0.75rem; color: var(--muted); margin-top: 0.4rem; }

    /* ── BUTTONS ── */
    .btn-primary {
      background: var(--ink); color: var(--bg);
      padding: 0.85rem 2rem; border-radius: 100px;
      border: none; font-size: 0.9rem; font-weight: 500;
      cursor: pointer; transition: all 0.18s;
      font-family: 'DM Sans', sans-serif; display: inline-flex;
      align-items: center; gap: 0.5rem;
    }
    .btn-primary:hover:not(:disabled) { background: var(--accent); transform: translateY(-1px); }
    .btn-primary:disabled { opacity: 0.45; cursor: not-allowed; }
    .btn-secondary {
      background: var(--card); color: var(--ink);
      padding: 0.85rem 1.5rem; border-radius: 100px;
      border: 1px solid var(--border); font-size: 0.9rem;
      cursor: pointer; transition: all 0.18s;
      font-family: 'DM Sans', sans-serif;
    }
    .btn-secondary:hover { border-color: var(--ink); }

    /* ── ANALYSIS PROGRESS ── */
    .analysis-card {
      background: var(--white); border: 1px solid var(--border);
      border-radius: 18px; padding: 2rem; text-align: center;
    }
    .analysis-spinner {
      width: 48px; height: 48px; margin: 0 auto 1rem;
      border: 3px solid var(--border); border-top-color: var(--accent);
      border-radius: 50%; animation: spin 0.8s linear infinite;
    }
    @keyframes spin { to{transform:rotate(360deg)} }
    .analysis-steps { margin-top: 1.5rem; display: flex; flex-direction: column; gap: 0.5rem; }
    .analysis-step {
      display: flex; align-items: center; gap: 0.75rem;
      font-size: 0.85rem; color: var(--muted); padding: 0.5rem 1rem;
      border-radius: 8px; background: var(--bg);
      transition: all 0.3s;
    }
    .analysis-step.active { color: var(--ink); background: var(--accent-light); border-left: 3px solid var(--accent); }
    .analysis-step.done { color: var(--accent); }
    #astep1.active { background: var(--accent-light); border-left: 3px solid var(--accent); }
    #astep2.active { background: #FEF9E7; border-left: 3px solid #B7770D; color: #7A5010; }
    #astep3.active { background: var(--accent3-light); border-left: 3px solid var(--accent3); color: #2E8A72; }
    #astep4.active { background: var(--accent2-light); border-left: 3px solid var(--accent2); color: #8A5020; }
    .step-icon { font-size: 1rem; }

    /* ── RESULTS ── */
    .results-header { display: flex; align-items: flex-start; justify-content: space-between; flex-wrap: wrap; gap: 1rem; margin-bottom: 1.5rem; }
    .results-meta { font-size: 0.78rem; color: var(--muted); margin-top: 0.3rem; }
    .results-meta span { margin-right: 1rem; }

    .output-tabs { display: flex; gap: 0.4rem; margin-bottom: 1rem; flex-wrap: wrap; }
    .tab-btn {
      padding: 0.4rem 1rem; border-radius: 100px;
      border: 1px solid var(--border); background: none;
      font-size: 0.8rem; cursor: pointer; transition: all 0.18s;
      font-family: 'DM Sans', sans-serif; color: var(--muted);
    }
    .tab-btn.active { background: var(--ink); color: var(--bg); border-color: var(--ink); }

    .output-area {
      background: var(--white); border: 1px solid var(--border);
      border-radius: 16px; padding: 1.5rem;
      font-size: 0.88rem; line-height: 1.75;
      min-height: 280px; white-space: pre-wrap;
      position: relative; overflow: hidden;
    }
    .output-area[contenteditable="true"] { outline: none; cursor: text; }
    .output-area[contenteditable="true"]:focus { border-color: var(--accent); }
    .edit-hint {
      position: absolute; top: 0.75rem; right: 0.75rem;
      font-size: 0.72rem; color: var(--muted); background: var(--bg);
      padding: 0.2rem 0.6rem; border-radius: 100px; border: 1px solid var(--border);
      pointer-events: none;
    }

    /* ── CUSTOMIZE ── */
    .customize-bar {
      background: var(--card); border-radius: 14px;
      padding: 1rem 1.2rem; margin-bottom: 1rem;
      display: flex; align-items: center; gap: 1.5rem; flex-wrap: wrap;
    }
    .customize-group { display: flex; align-items: center; gap: 0.5rem; }
    .customize-label { font-size: 0.75rem; color: var(--muted); white-space: nowrap; }
    .customize-select, .customize-input {
      padding: 0.3rem 0.6rem; border-radius: 8px;
      border: 1px solid var(--border); background: var(--white);
      font-size: 0.8rem; font-family: 'DM Sans', sans-serif; color: var(--ink);
      outline: none; transition: border-color 0.18s;
    }
    .customize-select:focus, .customize-input:focus { border-color: var(--accent); }
    .customize-input { width: 60px; text-align: center; }

    /* ── EXPORT ── */
    .export-section { margin-top: 1.5rem; }
    .export-label { font-size: 0.78rem; font-weight: 500; color: var(--muted); text-transform: uppercase; letter-spacing: 0.08em; margin-bottom: 0.75rem; }
    .export-grid { display: flex; flex-wrap: wrap; gap: 0.5rem; }
    .export-btn {
      display: flex; align-items: center; gap: 0.4rem;
      padding: 0.5rem 1rem; border-radius: 100px;
      border: 1px solid var(--border); background: var(--white);
      font-size: 0.82rem; cursor: pointer; transition: all 0.18s;
      font-family: 'DM Sans', sans-serif; color: var(--ink);
    }
    .export-btn:hover { transform: translateY(-1px); box-shadow: 0 2px 12px rgba(0,0,0,0.07); }
    .export-btn.pdf:hover  { border-color: #E05B5B; background: #FEF2F2; color: #E05B5B; }
    .export-btn.docx:hover { border-color: #5B6AF0; background: var(--accent-light); color: var(--accent); }
    .export-btn.md:hover   { border-color: #1C1B2E; background: #F0EFF8; color: #1C1B2E; }
    .export-btn.txt:hover  { border-color: var(--muted); background: var(--card); color: var(--ink); }
    .export-btn.csv:hover    { border-color: #56C4A8; background: var(--accent3-light); color: #379B84; }
    .export-btn.sheets:hover { border-color: #1A9E5A; background: #E8F8EF; color: #1A9E5A; }
    .export-btn.json:hover   { border-color: #E8A87C; background: var(--accent2-light); color: #B5712A; }
    .export-btn.loading { opacity: 0.5; pointer-events: none; }

    /* ── FOOTER ── */
    footer {
      border-top: 1px solid var(--border);
      padding: 1.2rem 2rem; text-align: center;
      font-size: 0.78rem; color: var(--muted);
    }

    /* ── RESPONSIVE ── */
    @media(max-width:600px) {
      main { padding: 1.5rem 1rem 3rem; }
      .steps-bar { display: none; }
      .results-header { flex-direction: column; }
      .customize-bar { gap: 0.8rem; }
    }
  </style>
</head>
<body>
<div class="app" id="app">

  <nav>
    <div class="logo">Simplify<span>.</span></div>
    <div class="nav-status">
      <div class="status-dot" id="statusDot"></div>
      <span id="statusText">Ready</span>
    </div>
  </nav>

  <main>
    <!-- Steps bar -->
    <div class="steps-bar">
      <div class="step-item active" id="step1">
        <div class="step-num">1</div>
        <span class="step-label">Upload files</span>
      </div>
      <span class="step-arrow">›</span>
      <div class="step-item" id="step2">
        <div class="step-num">2</div>
        <span class="step-label">Set topic</span>
      </div>
      <span class="step-arrow">›</span>
      <div class="step-item" id="step3">
        <div class="step-num">3</div>
        <span class="step-label">Analyze</span>
      </div>
      <span class="step-arrow">›</span>
      <div class="step-item" id="step4">
        <div class="step-num">4</div>
        <span class="step-label">Export</span>
      </div>
    </div>

    <!-- ① UPLOAD PANEL -->
    <div class="panel" id="panelUpload">
      <h1 class="panel-title">Upload your files</h1>
      <p class="panel-sub">Drop everything in first — ChatGPT exports, docs, notes. No sorting needed.</p>

      <div class="integrations-label">Connect a platform</div>
      <div class="integration-grid">
        <button class="integration-btn" onclick="connectIntegration(this, 'ChatGPT')"><span class="integration-icon">🤖</span>ChatGPT</button>
        <button class="integration-btn" onclick="connectIntegration(this, 'Claude')"><span class="integration-icon">✦</span>Claude</button>
        <button class="integration-btn" onclick="connectIntegration(this, 'Gemini')"><span class="integration-icon">💎</span>Gemini</button>
        <button class="integration-btn" onclick="connectIntegration(this, 'Copilot')"><span class="integration-icon">🪟</span>Copilot</button>
        <button class="integration-btn" onclick="connectIntegration(this, 'KIMI')"><span class="integration-icon">🌙</span>KIMI</button>
        <button class="integration-btn" onclick="connectIntegration(this, 'Bolt')"><span class="integration-icon">⚡</span>Bolt</button>
        <button class="integration-btn" onclick="connectIntegration(this, 'Lovable')"><span class="integration-icon">💜</span>Lovable</button>
        <button class="integration-btn" onclick="connectIntegration(this, 'Replit')"><span class="integration-icon">🔁</span>Replit</button>
        <button class="integration-btn" onclick="connectIntegration(this, 'GitHub')"><span class="integration-icon">🐙</span>GitHub</button>
      </div>

      <div class="drop-zone" id="dropZone">
        <input type="file" id="fileInput" multiple accept=".txt,.pdf,.docx,.md,.json,.csv,.html,.py,.js"/>
        <div class="drop-icon">📂</div>
        <h3>Drop your files here</h3>
        <p>or <u style="cursor:pointer" onclick="document.getElementById('fileInput').click()">click to browse</u></p>
        <p class="drop-types">TXT · PDF · DOCX · MD · JSON · CSV · HTML · PY · JS</p>
      </div>

      <div class="file-list" id="fileList"></div>

      <button class="btn-primary" id="btnNext1" onclick="goToStep2()" disabled>
        Continue <span>→</span>
      </button>
    </div>

    <!-- ② TOPIC PANEL -->
    <div class="panel" id="panelTopic" style="disp
    flask==3.0.0
flask-cors==4.0.0
werkzeug==3.0.1
reportlab==4.1.0
python-docx==1.1.0
pdfplumber==0.10.3

