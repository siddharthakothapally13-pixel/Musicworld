# Soundloom

A local-first music creation studio with three coordinated workflows: lyric writing, track briefs, and lyric-video planning.

## Run it

1. Copy `api/.env.example` to `api/.env` and set `ALLOWED_ORIGINS` for your frontend address.
2. Start the API:
   ```powershell
   cd api
   py -m venv .venv
   .\.venv\Scripts\Activate.ps1
   pip install -r requirements.txt
   cd ..
   uvicorn api.main:app --reload --port 8000
   ```
3. In another terminal, start the UI:
   ```powershell
   cd frontend
   npm install
   npm run dev
   ```

The app runs in a useful demo mode when provider credentials are absent. Add provider keys only to `api/.env`; they are never accepted by or returned to the frontend.

## Vercel

This repository is configured as one deployment: Vite builds the frontend and the root `app.py` exports FastAPI as the Vercel Function. In Vercel Project Settings, set `ALLOWED_ORIGINS` to the deployed site URL and add any optional provider variables from `api/.env.example`. Never set `VITE_API_URL` for the deployed project; same-origin `/api` routes keep server secrets out of the browser bundle.

## Provider security

- `api/providers/` is the only integration boundary for LLM, music, and video services.
- API keys are read once from environment variables and are excluded from response models and logs.
- FastAPI uses a restrictive CORS allowlist, security headers, request-size limits, validated schemas, and lightweight rate limits.
- FFmpeg receives generated local filenames only; no user-controlled shell strings are executed.

## APIs

- `POST /api/lyrics/generate`
- `POST /api/music/generate`
- `POST /api/video/generate`
- `POST /api/library/search` - MusicBrainz metadata search plus optional CC0 Freesound samples
- `GET /api/health`
