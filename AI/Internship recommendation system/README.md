# Internship Recommender

## What this is
An internship recommendation API that ranks internship postings for students using semantic embeddings, TF‑IDF pre‑filtering, FAISS nearest‑neighbors search, and a cross‑encoder re‑ranker. It's intended for university portals, career platforms, or services that want to match students to relevant internship opportunities.

### Stack
- **Language(s):** Python (primary), Dockerfile (container)
- **Framework / runtime:** Flask + Gunicorn
- **Notable libraries:** sentence-transformers (embedding + cross-encoder), faiss-cpu (vector search), scikit-learn (Tfidf), pandas, numpy

<img width="2720" height="2960" alt="internship_flow_compact_light" src="https://github.com/user-attachments/assets/709d7ebe-1775-4d7d-9530-a36f9d36ec89" />

## How it's organized
```
app.py                         Main Flask app, recommendation logic and endpoints
requirements.txt               Python dependencies
Dockerfile                     Container image: installs PyTorch, requirements and runs Gunicorn
skills_en.csv                  ESCO skills dataset (preferredLabel, conceptUri, ...)
skillSkillRelations_en.csv     ESCO skill-to-skill relations (used for relation boosts)
README.md                      This document
```

How it fits together: app.py exposes HTTP endpoints (/recommend and /health). The recommend() function builds an embeddings index (SentenceTransformer → FAISS) and a TF‑IDF prefilter for incoming internship records, computes semantic and skill‑coverage scores, applies ESCO‑based boosts where skills are exact or related, and then re‑ranks the top candidates with a CrossEncoder. The CSVs supply the skill ontology and relations used by esco_boost().

## How to run it

Prerequisites:
- Python 3.10 (the Dockerfile uses python:3.10-slim)
- Internet access on first run to download the transformer models
- The repository's CSV files (skills_en.csv and skillSkillRelations_en.csv) must remain in the working directory

Quick local (development) steps:
```bash
# clone
git clone https://github.com/Nadine-khaled/Internship-Recommendation-System.git
cd Internship-Recommendation-System

# optional: create venv
python3 -m venv .venv
source .venv/bin/activate

# install dependencies
pip install -r requirements.txt

# run dev server (Flask built-in)
python app.py
# or run production server (gunicorn)
gunicorn --bind 0.0.0.0:7860 --timeout 120 app:app
```

Run with Docker:
```bash
# build image
docker build -t internship-recs .

# run container (exposes port 7860)
docker run -p 7860:7860 internship-recs
```

Notes:
- The Dockerfile installs CPU PyTorch wheels; to use GPU you must modify the image and installation steps.
- On first run the SentenceTransformer and CrossEncoder models (configured in app.py) will be downloaded; this requires network and may take several minutes.
- FAISS index and intermediate embeddings are kept in memory; the app does not persist the index between runs.

## API

- POST /recommend
  - Description: Accepts a JSON body containing "student" and "internships" and returns ranked recommendations.
  - Required fields:
    - student: object (recommended fields: user_id, skills: [str], experiences: [{title: str}], location: {city: str})
    - internships: array of internship objects. Each internship should include at minimum:
      - id (int)
      - title (str)
      - required_skills (str) — comma / slash / parentheses separated list is parsed
  - Example request (curl):
```bash
curl -s -X POST "http://localhost:7860/recommend" \
  -H "Content-Type: application/json" \
  -d '{
    "student": {
      "user_id": "student_123",
      "skills": ["python", "data analysis", "machine learning"],
      "experiences": [{"title": "Research Assistant"}],
      "location": {"city": "cairo"}
    },
    "internships": [
      {"id": 1, "title": "ML Intern", "company_name": "Acme", "required_skills": "Python, Machine Learning", "governrate":"Cairo", "work_type":"Onsite"},
      {"id": 2, "title": "Data Analyst Intern", "company_name": "Beta", "required_skills": "SQL / Python", "work_type":"Remote"}
    ]
  }'
```

- GET /health
  - Returns a small JSON with status and the configured model name:
```bash
curl http://localhost:7860/health
# {"status":"healthy","model":"BAAI/bge-base-en-v1.5"}
```

## Important implementation details (from app.py)
- Models:
  - Embeddings: "BAAI/bge-base-en-v1.5" (SentenceTransformer)
  - Reranker: "cross-encoder/ms-marco-MiniLM-L-6-v2" (CrossEncoder)
- Scoring pipeline:
  1. Build `embed_text` for each internship: title + company_name + required_skills
  2. Fit TF‑IDF on internships and use it to prefilter top PREFILTER_N (default 100)
  3. Build FAISS IndexFlatIP on normalized embeddings and take the top PREFILTER_N by similarity
  4. Combine TF‑IDF and FAISS candidates, compute overlap-based coverage, semantic dot-product score
  5. Apply ESCO-based boost via esco_boost (matches or related concepts give small bonuses)
  6. Rerank the top RERANK_TOP_K (default 25) with the CrossEncoder and combine rerank scores with base_score

## Tech Stack
- Python 3.10
- Flask (HTTP API)
- Gunicorn (production WSGI server)
- PyTorch (backend for sentence-transformers; CPU wheels by default)
- sentence-transformers (SentenceTransformer + CrossEncoder)
- faiss-cpu (vector similarity search)
- scikit-learn (TfidfVectorizer)
- pandas, numpy
- Docker (containerization)

## Caveats & tips
- Data format: app.py expects internships as a list of dicts with specific keys (`id`, `title`, `required_skills`). If keys are missing the endpoint returns a 400.
- Memory: creating embeddings for a large internships dataset can be memory intensive. Consider batching or persisting a precomputed index for production.
- Model downloads: the models are downloaded dynamically by sentence-transformers; if you deploy to an offline environment pre-download or cache them.
- To enable GPU: modify Dockerfile and pip installs to use appropriate CUDA PyTorch wheels and ensure sentence-transformers uses the GPU.

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
 

