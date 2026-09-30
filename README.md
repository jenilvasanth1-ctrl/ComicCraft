# ComicCraft - AI Comic Story Creator using Gemini Models

ComicCraft is a web-based application that uses AI to generate personalized
comic book stories and illustrations from a user prompt. It's built with
**FastAPI** and combines **Google's Gemini models** (Flash for structured
outlines, Pro for narration and dialogue) with **Stable Diffusion** (via
Hugging Face Diffusers) for comic-style illustrations. Finished comics can
be previewed on the web and downloaded as a multi-page **PDF**.

## Features

- Enter a story prompt, character name, setting, tone, and art style
- Gemini Flash generates a structured 5-panel comic outline
- Gemini Pro expands the outline into full narration and dialogue
- Stable Diffusion generates a comic-style illustration per panel
- Panels are assembled into a layout and previewed in the browser
- One-click export to a downloadable, professionally formatted PDF
- Both a browser form (`/generate`) and a JSON API (`/generate-comic/json`)

## Project Structure

```
comiccraft/
├── app/
│   ├── main.py             # FastAPI app entry point
│   ├── routes.py           # All route handlers
│   ├── models.py           # Pydantic schemas (PromptRequest)
│   ├── gemini_flash.py     # generate_outline() - Gemini 1.5 Flash
│   ├── gemini_pro.py       # generate_story() - Gemini 1.5 Pro
│   ├── image_generator.py  # generate_image() - Stable Diffusion
│   ├── layout_builder.py   # build_comic_layout()
│   └── exporters.py        # save_pdf() - FPDF export
├── templates/
│   ├── index.html          # Story input form
│   ├── comic_preview.html  # Generated comic preview
│   └── export_success.html # Export confirmation page
├── static/
│   ├── css/style.css
│   ├── panels/              # Generated panel images land here
│   └── exports/             # Generated PDFs land here
├── requirements.txt
├── .env.example
└── README.md
```

## Prerequisites

- Python 3.10+
- A [Google Gemini API key](https://ai.google.dev/)
- A [Hugging Face API token](https://huggingface.co/settings/tokens) with
  access to `runwayml/stable-diffusion-v1-5`

## Setup

1. **Create and activate a virtual environment**

   ```bash
   python -m venv comiccraft-env

   # Windows
   comiccraft-env\Scripts\activate

   # macOS/Linux
   source comiccraft-env/bin/activate
   ```

2. **Install dependencies**

   ```bash
   pip install -r requirements.txt
   ```

3. **Configure environment variables**

   Copy `.env.example` to `.env` and fill in your keys:

   ```bash
   cp .env.example .env
   ```

   ```
   GEMINI_API_KEY=your_gemini_api_key_here
   HF_API_KEY=your-huggingface-api-key-here
   ```

4. **Run the server**

   ```bash
   uvicorn app.main:app --reload
   ```

5. **Open the app**

   - App: <http://127.0.0.1:8000>
   - Interactive API docs: <http://127.0.0.1:8000/docs>

## API Routes

| Method | Route                    | Description                                             |
|--------|---------------------------|-----------------------------------------------------------|
| GET    | `/`                       | Homepage with the story input form                        |
| POST   | `/generate`               | Handles form submission, renders the comic preview page   |
| POST   | `/generate-comic/json`    | JSON API: accepts a `PromptRequest`, returns layout + PDF  |
| GET    | `/export-success`         | Confirms a successful PDF download                         |
| POST   | `/test-image`             | Developer utility to test image generation directly       |

### Example JSON request

```bash
curl -X POST http://127.0.0.1:8000/generate-comic/json \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "A brave fox exploring an enchanted forest",
    "character_name": "Finn",
    "setting": "forest",
    "tone": "dramatic",
    "art_style": "anime"
  }'
```

## Notes

- Stable Diffusion runs locally via `diffusers`; the model downloads on
  first run (a few GB) and generation is much faster with a CUDA GPU.
- Generated panel images and PDFs are written to `static/panels/` and
  `static/exports/`; these are git-ignored so only the folders (via
  `.gitkeep`) are tracked.
- The current version generates one comic at a time with no user account
  system — the modular structure makes it straightforward to add comic
  libraries, user profiles, or multi-page story arcs later.
