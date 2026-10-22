# Multimodal Construction Intelligence Platform

A lightweight React + FastAPI project for construction decision support. It uses the hosted Gemini API for text/image reasoning and Open-Meteo for weather data. **It does not load local AI models, PyTorch, TensorFlow, or large embedding models**, which helps avoid out-of-memory (OOM) failures on small deployment instances.

## Features

- Construction-site safety review from a description and optional image
- Crack/image review with visible observations and recommended next steps
- Real-estate valuation estimate from user-provided property details
- Construction project planning and risk checklist
- Construction site assistant / construction knowledge Q&A
- Weather-aware advisory using Open-Meteo
- React dashboard, JSON API, input validation, health endpoint
- Image resizing before sending to Gemini to reduce memory and request size

## Requirements

- Python 3.10+ (Python 3.11 recommended)
- Internet connection
- Gemini API key from Google AI Studio
- VS Code or another editor

## Run locally (Windows PowerShell)

```powershell
# 1. Extract the ZIP and open the extracted folder in VS Code.
# 2. Create a virtual environment:
python -m venv .venv

# 3. Activate it:
.\.venv\Scripts\Activate.ps1

# If PowerShell blocks activation, run this once for the current terminal:
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
.\.venv\Scripts\Activate.ps1

# 4. Install dependencies:
python -m pip install --upgrade pip
pip install -r requirements.txt

# 5. Create your local environment file:
Copy-Item .env.example .env
# Edit .env and replace GEMINI_API_KEY with your actual key.

# 6. Start the app:
python -m uvicorn app.main:app --reload
```

Open http://127.0.0.1:8000. API documentation is at http://127.0.0.1:8000/docs.

## API key setup

Put your key in `.env`:
```env
GEMINI_API_KEY=your_real_key_here
GEMINI_MODEL=gemini-2.5-flash
MAX_IMAGE_MB=8
```
Never commit `.env` or expose the API key in frontend code. `.env` is ignored by Git.

If no API key is configured, the UI and health endpoint still load, but AI analysis returns a clear setup error.

## GitHub

From the project folder:
```powershell
git init
git add .
git commit -m "Build multimodal construction intelligence platform"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/multimodal-construction-intelligence.git
git push -u origin main
```
Create the empty repository on GitHub first and replace `YOUR_USERNAME` with your username. Do not add `.env`.

## Deploy to Render

1. Push this folder to GitHub.
2. In Render, create **New + → Web Service** and connect the repository.
3. Render can detect `render.yaml`. If setting values manually:
   - Build command: `pip install -r requirements.txt`
   - Start command: `uvicorn app.main:app --host 0.0.0.0 --port $PORT`
4. Add environment variable `GEMINI_API_KEY` in Render's Environment settings. Optionally set `GEMINI_MODEL=gemini-2.5-flash`.
5. Deploy, then test `/api/health` and the home page.

## OOM-conscious implementation

- All AI inference runs through Google's hosted Gemini API; there is no local LLM or model download.
- No Torch, Transformers, sentence-transformers, vector database, or background worker is installed.
- Image uploads are capped and resized to a maximum 1280px dimension before analysis.
- Keep one Render web instance and avoid unnecessary concurrency on very small instances.
- If Gemini returns a quota or model error, the API reports a short, readable error instead of crashing the process.

## Important limitations and responsible use

- AI output is decision support, not professional engineering certification.
- Crack analysis from a photo cannot establish structural safety. Any concerning crack, deformation, exposed reinforcement, or suspected structural damage must be assessed on-site by a qualified structural engineer.
- Property valuation is a rough estimate based only on user-supplied inputs and is not an appraisal or financial advice.
- Weather advisory uses public forecast data and should not replace official alerts or site safety procedures.
- Never upload confidential client documents or identifiable people without permission.
