# CyberLearn CTF — Group 18 Security Quest

A progressive, educational Capture The Flag (CTF) platform built for the **IE3132 Penetration Testing** module at the **Sri Lanka Institute of Information Technology (SLIIT)**.

Participants register, then work through six linked challenges covering steganography, web security, cryptography, digital forensics, networking, and Linux security. Each stage unlocks only after the previous flag is verified.

**Stack:** Python · Flask · SQLite · Jinja2 · HTML/CSS/JS

> **Spoiler policy:** this repository does not document the flags or solutions. Flags are loaded from a local `.env` file that you generate yourself (see [Setup](#setup)).

---

## Features

- **Six sequential challenges** with server-side stage unlocking
- **Server-side flag validation** using HMAC-SHA256 with a secret pepper and `hmac.compare_digest`
- **Registration and login** with hashed passwords (Werkzeug)
- **Account management** at `/account`: update username/email, change password, delete account (cascades to progress records)
- **Quest dashboard** with progress tracking and rank badges (Recruit, Investigator, Master Operative)
- **Downloadable challenge artefacts** (image, text, ZIPs, PCAP)
- **Progressive hints** for each challenge
- **Light/dark theme** with a saved preference and no flash on load
- **Built-in security controls:** CSRF protection, rate limiting, hardened cookies, security headers (see below)

---

## Challenges

| Stage | Name | Domain | Difficulty | Artefact |
|---|---|---|---|---|
| 01 | Hidden in Plain Sight | Steganography | Easy | `welcome.png` |
| 02 | The Broken Gate | Web Security | Easy | Controlled web page |
| 03 | The Encoded Message | Cryptography | Moderate | `message.txt` |
| 04 | Digital Footprints | Digital Forensics | Moderate | `evidence.zip` |
| 05 | Traffic Under Investigation | Networking | Moderate–Hard | `traffic_capture.pcap` |
| 06 | The Misconfigured Server | Linux / System Security | Hard | `stage06_evidence.zip` |

Flag format: `GROUP18{...}`

Suggested tools: `file`, `strings`, ExifTool, CyberChef, Wireshark/TShark, browser DevTools, Burp Suite, and standard Linux CLI utilities (`grep`, `find`, `unzip`, `base64`).

> **Note:** Stage 02 is intentionally vulnerable. The weakness is confined to that challenge page and exists to show why client-side checks should never be trusted.

---

## Setup

### Requirements

- Python 3
- `pip`

### 1. Clone

```bash
git clone https://github.com/Heshitha73/CyberLearn-CTF
cd CyberLearn-CTF
```

### 2. Create a virtual environment

```bash
# Linux / macOS
python3 -m venv venv
source venv/bin/activate
```

```powershell
# Windows (PowerShell)
python -m venv venv
.\venv\Scripts\Activate.ps1
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Configure environment variables

Generate a `.env` file with random secrets:

```bash
python setup_env.py
```

Or create `.env` manually:

```env
SECRET_KEY=<random hex string>
FLAG_PEPPER=<random hex string>

STAGE1_FLAG=GROUP18{...}
STAGE2_FLAG=GROUP18{...}
STAGE3_FLAG=GROUP18{...}
STAGE4_FLAG=GROUP18{...}
STAGE5_FLAG=GROUP18{...}
STAGE6_FLAG=GROUP18{...}
```

Generate a random value with:

```bash
python -c "import secrets; print(secrets.token_hex(32))"
```

`.env` is listed in `.gitignore`. **Never commit it.**

### 5. Generate challenge artefacts

The artefact generators read the stage flags from `.env`. Run them again whenever you change a flag:

```bash
python create_stage01.py
python create_stage03.py
python create_stage04.py
python create_stage05.py
python create_stage06.py
```

### 6. Run

```bash
python app.py
```

Open **http://127.0.0.1:5000**. The SQLite database is created automatically in `database/`.

---

## Project structure

```text
CyberLearnCTF/
├── app.py                  # Flask app, routes, DB setup, security headers
├── forms.py                # WTForms (auth, profile, password, delete)
├── requirements.txt
├── setup_env.py            # Generates .env with random secrets
├── create_stage01.py       # Artefact generators (one per stage)
├── create_stage03.py
├── create_stage04.py
├── create_stage05.py
├── create_stage06.py
├── challenges/             # Generated challenge files, grouped by stage
├── database/               # SQLite database (auto-created, git-ignored)
├── static/
│   ├── css/style.css
│   └── js/theme.js
└── templates/
    ├── stage02/            # Stage 02 gate page and script template
    ├── base.html
    ├── index.html
    ├── register.html
    ├── login.html
    ├── dashboard.html
    ├── account.html
    ├── challenge.html
    └── error.html
```

---

## Security controls

- **Passwords:** hashed with Werkzeug
- **SQL injection:** parameterised queries
- **CSRF:** Flask-WTF tokens on forms
- **Flag checks:** HMAC-SHA256 digests compared in constant time
- **Rate limiting:** Flask-Limiter on login, registration, flag submission, password change, and account deletion
- **Cookies:** `HttpOnly`, `SameSite=Lax`
- **Headers:** Content-Security-Policy, X-Frame-Options, X-Content-Type-Options, Referrer-Policy

**Before any real deployment:** set `SESSION_COOKIE_SECURE=True` (it is currently `False` for local HTTP use) and run behind a production WSGI server over HTTPS. `python app.py` uses Flask's development server and is meant for local use only.

---

## Academic context

| | |
|---|---|
| Institution | Sri Lanka Institute of Information Technology (SLIIT) |
| Faculty | Faculty of Computing |
| Module | IE3132 — Penetration Testing |
| Group | Group 18 |
| Year | 2026 |

---

## Disclaimer

CyberLearn CTF was developed for academic and educational purposes. All challenges are designed to run in an isolated, local environment. Only apply these techniques to systems you own or have explicit written permission to test.
