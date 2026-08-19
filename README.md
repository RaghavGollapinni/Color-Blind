# Color-Blind

An accessibility-focused web app that helps color-blind users interpret images — especially road/traffic signs — by applying color-vision-deficiency filters, running OCR on any text in the image, and detecting signs/objects in the photo. It surfaces the results as text and an audio-ready message.

Built with a **Django REST Framework** backend and a **React** frontend.

## Features

- **Image upload & filtering** — apply protanopia, deuteranopia, tritanopia, or grayscale filters to an uploaded image
- **Text extraction (OCR)** — reads any text in the image using EasyOCR
- **Sign detection** — flags common road/traffic signs (stop, yield, speed limit, no entry, one way, caution, school zone, construction, parking, exit) from the extracted text
- **Object detection** — identifies traffic-related objects (signs, vehicles, pedestrians, etc.) using Hugging Face models (YOLOS, ResNet-50)
- **Audio-ready alerts** — generates a combined spoken-style message summarizing detected signs and text
- **User preferences** — save a user's color-blindness type for reuse

## Tech Stack

**Backend**
- Django 5.2 + Django REST Framework
- SQLite (default dev database)
- OpenCV, Pillow — image processing/filters
- EasyOCR — text extraction
- Hugging Face `transformers` (YOLOS, ResNet-50) + PyTorch/torchvision — object detection & classification
- django-cors-headers

**Frontend**
- React 18 + React Router
- React Bootstrap
- Axios

## Project Structure

```
Color-Blind/
├── myproject/                  # Django project
│   ├── manage.py
│   ├── requirements.txt
│   ├── db.sqlite3
│   ├── media/                  # uploaded & processed images
│   └── myproject/
│       ├── backend/            # Django app (models, views, serializers, urls)
│       │   ├── models.py       # Image, UserPreference
│       │   ├── views.py        # upload_image, set_preference, OCR/detection logic
│       │   ├── serializers.py
│       │   └── urls.py
│       ├── settings.py
│       └── urls.py
└── Frontend/                   # React app
    ├── package.json
    └── src/
        ├── pages/               # WelcomePage, UserInfoPage, ColorBlindnessPage, CameraUploadPage, ResultPage
        ├── components/          # Accessibility, ColorblindForm, ImageUploader, ResultDisplay
        └── utils/api.js         # API client (calls the Django backend)
```

## Getting Started

### Prerequisites
- Python 3.10+ (tested with 3.12/3.13)
- Node.js + npm
- (Optional) a CUDA-capable GPU for faster OCR/object detection — CPU works fine by default

### Backend setup

```bash
cd myproject
python -m venv venv
source venv/bin/activate      # Windows: venv\Scripts\activate
pip install -r requirements.txt
python manage.py migrate
python manage.py runserver
```

The API will be available at `http://127.0.0.1:8000/`.

### Frontend setup

```bash
cd Frontend
npm install
npm start
```

The app runs at `http://localhost:3001` (configured via `cross-env PORT=3001` in `package.json`) and is pre-configured to talk to the backend at `http://127.0.0.1:8000/api`.

> Note: `settings.py` allows CORS from `http://localhost:3001` and `http://localhost:3002`. If you run the frontend on a different port, update `CORS_ALLOWED_ORIGINS` in `myproject/myproject/settings.py`.

## API Endpoints

| Method | Endpoint             | Description                                                              |
|--------|-----------------------|----------------------------------------------------------------------------|
| GET    | `/api/`               | Health check — `{"status": "API is running"}`                            |
| GET    | `/api/hello/`         | Sample endpoint                                                           |
| POST   | `/api/upload-image/`  | Upload an image + preferences; returns filtered image URL, extracted text, detected signs/objects, and an audio-ready message |
| POST   | `/api/set-preference/`| Save a user's color-blindness type preference                             |

**`upload-image/` request (multipart/form-data):**
- `image` — the image file
- `preferences` — JSON array string, e.g. `["deuteranopia"]`
- `ocr_language` — e.g. `"eng"`

## Notes

- `DEBUG = True` and a hardcoded `SECRET_KEY` are set in `settings.py` for local development — replace these before deploying.
- The first request may be slow while EasyOCR and the Hugging Face models download/initialize.
- Uploaded and processed images are stored under `myproject/media/`.

## License

No license specified yet — add one (e.g. MIT) if you plan to share or open this project up for contributions.
