# Resume Parser API

A simple, lightweight REST API that parses resumes (PDF & DOCX) and extracts:

- **Skills**
- **Work experience** (title, company, dates)
- **Location**

Powered by **spaCy** + custom skill extraction model from Hugging Face + **Groq LLM** (Llama 3.3 70B) for cleaning and structuring the output.

## Table of Contents

- [Live API](#live-api-online--ready-to-use)
- [Local Installation](#local-installation--development)
- [API Usage](#api-usage)
- [Important Notes](#important-notes)
- [Tech Stack](#tech-stack)
- [Author](#author)

## Live API (Online & Ready to Use)

**Main Endpoint (POST):**
```
https://nadinekhaled500-cv-parser-api.hf.space/parse_resume
```

### How to Send a Request

- **Method:** POST
- **Content-Type:** multipart/form-data
- **Form field:** `file` (type: File) — upload PDF or DOCX resume
- **File size limit:** Up to 25 MB
- **Supported formats:** PDF, DOCX

#### curl Example

```bash
curl -X POST \
  -F "file=@/path/to/your/resume.pdf" \
  https://nadinekhaled500-cv-parser-api.hf.space/parse_resume
```

#### Postman Example

1. Method → **POST**
2. URL → `https://nadinekhaled500-cv-parser-api.hf.space/parse_resume`
3. Body → **form-data**
4. Key: `file` → change type to **File**
5. Select your CV file → **Send**

#### Success Response Example

```json
{
  "skills": [
    "Python",
    "Machine Learning",
    "Deep Learning",
    "Computer Vision",
    "Pandas",
    "NumPy",
    "Scikit-learn"
  ],
  "experience": [
    {
      "title": "Machine Learning Engineer Intern",
      "company": "DEPI",
      "startDate": "Jun 2025",
      "endDate": "present"
    },
    {
      "title": "AI Trainee",
      "company": "National Telecommunication Institute (NTI)",
      "startDate": "Aug 2024",
      "endDate": "Sep 2024"
    }
  ],
  "location": {
    "city": "Fayoum",
    "country": "Egypt"
  },
   "user_id": "123"
}
```

**Note:** The first request may take 10–40 seconds (cold start + model loading). Subsequent requests are much faster.

## Local Installation & Development

### Prerequisites

- Python 3.8 or higher
- pip package manager
- Internet connection (for downloading models)

### Setup Steps

```bash
# 1. Clone the repository
git clone https://github.com/Nadine-khaled/Resume-parser.git
cd Resume-parser

# 2. Create a virtual environment (optional but recommended)
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# 3. Install dependencies
pip install -r requirements.txt

# 4. Download the large English spaCy model (needed only once)
python -m spacy download en_core_web_lg

# 5. Create .env file and add your Groq API key
echo GROQ_API_KEY=your_groq_api_key_here > .env

# 6. Run the server
python app.py
```

### Running with Gunicorn (Recommended for Production)

```bash
pip install gunicorn
gunicorn --bind 0.0.0.0:5000 app:app
```

The API will be available locally at:
```
http://127.0.0.1:5000/parse_resume
```

## API Usage

### Testing Locally

You can test the API using curl or Postman with your local endpoint: `http://127.0.0.1:5000/parse_resume`

Use the same request format as the live API examples above.

## Important Notes

- **First Run:** The skill-extractor model from Hugging Face (~466 MB) will be downloaded on first use — may take several minutes depending on your internet speed.
- **Groq API Key:** Must be provided via the `GROQ_API_KEY` environment variable. [Get your free key here](https://console.groq.com)
- **Date Parsing Limitations:** Occasionally produces future or inaccurate dates — known limitation that may require post-processing.
- **False Positives:** Courses/short trainings sometimes appear in experience data — consider filtering on the client side if needed.
- **Rate Limiting:** The live API has rate limits to prevent abuse. Local deployments have no rate limits.

### Troubleshooting

**Problem:** `ModuleNotFoundError: No module named 'groq'`
- **Solution:** Run `pip install -r requirements.txt` to install all dependencies.

**Problem:** Groq API returns authentication error
- **Solution:** Ensure your `GROQ_API_KEY` is correctly set in the `.env` file and is valid.

**Problem:** spaCy model download fails
- **Solution:** Check your internet connection and try again. You can manually download it with `python -m spacy download en_core_web_lg`

**Problem:** Slow initial response on live API
- **Solution:** This is expected on first request. The API needs to load the models. Subsequent requests will be fast.

## Tech Stack

| Component | Technology |
|-----------|-----------|
| **Backend** | Flask |
| **PDF/DOCX Parsing** | pdfplumber + python-docx |
| **NER & Skills Extraction** | spaCy + amjad-awad/skill-extractor |
| **LLM Cleaning** | Groq (Llama 3.3 70B) |
| **Production Server** | Gunicorn |

## Contributing

We welcome contributions! Please feel free to:
- Open issues for bugs or feature requests
- Submit pull requests with improvements
- Share feedback and suggestions

## License

This project is open source and available under the MIT License.

## Author

**Nadine Khaled**

- [GitHub](https://github.com/Nadine-khaled)
- [LinkedIn](https://www.linkedin.com/in/nadine-khaled-6abb68253/)
