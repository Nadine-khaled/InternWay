# InternWay

AI-powered platform connecting students, mentors, and companies to simplify internships, mentorships, and recruitment. InternWay provides automated CV parsing, smart internship matching, mentorship booking, company posting dashboards, real-time notifications, and multilingual support to streamline the path from learning to professional experience.

## Table of contents
- [Key features](#key-features)
- [Demo & Documentation](#demo--documentation)
- [Architecture & Stack](#architecture--stack)
- [Project structure](#project-structure)
- [Getting started (local)](#getting-started-local)
- [Development workflow](#development-workflow)
- [Contributing](#contributing)
- [Team](#team)
- [License & contact](#license--contact)

## Key features
- CV parsing and structured resume extraction (skills, education, experience).
- Smart internship matching: match students to internship openings with relevance scoring.
- Mentorship booking and session management with status tracking.
- Company dashboard to post internships, review applicants, and manage postings.
- Real-time notifications and messaging for bookings and application updates.
- Role-based access control (Student, Mentor, Company).
- Multilingual UI (English / Arabic) with RTL/LTR support and theming (dark/light).

## Demo & Documentation
- Front-end demo (deployed on Vercel): see the Front end README for the live preview link.
- Technical documentation and final project reports are included in the repository as PDF files:
  - `Intern Way- Final-1.pdf`
  - `EELU-Graduation Projects (internway final Documentation)(0410).pdf`

## Architecture & Stack
- Languages: TypeScript (front-end), C# (backend API), CSS, Python (AI components/scripts)
- Frontend: Next.js (App Router) + React + TypeScript
- Styling: CSS Modules, Bootstrap 5
- Notable libraries: Axios (HTTP client), Lucide React (icons)
- Backend: C#/.NET (API & business logic)
- AI/ML: Python scripts for CV parsing and matching models
- Deployment: Frontend hosted on Vercel (CI/CD), backend typically deployable to any .NET host or container platform

## Project structure
Top-level (annotated)
```
AI/             # Python scripts and models for CV parsing / matching / ML experiments
Back end/       # Backend API (C#, .NET) — business logic, data access, auth
Front end/      # Next.js + React application (TypeScript) — public UI and dashboards
*.pdf           # Final documentation and project reports (design, research, user flows)
README.md       # Project overview (this file)
```

How it fits together:
- The Front end is a Next.js application that calls the Back end API for authentication, profile management, internship listings, bookings, and notifications.
- The Back end implements REST endpoints, business rules, and persistence.
- The AI folder contains Python code used for parsing CVs, extracting structured features, and scoring matches; outputs are consumed by the Back end during matching operations.

## Getting started (local)
These are the minimal steps to run the platform locally. Adjust details to match your environment and the backend implementation.

1. Clone repository
```bash
git clone https://github.com/Nadine-khaled/InternWay.git
cd InternWay
```

2. Frontend (Next.js)
```bash
cd "Front end"
npm install
npm run dev
# Open http://localhost:3000
```

3. Backend (C#/.NET)
- Open the `Back end` directory in your preferred IDE (Visual Studio / VS Code).
- Restore packages and run the API:
```bash
# Example for .NET
cd "Back end"
dotnet restore
dotnet run
# API typically available at http://localhost:5000 or as configured
```

4. AI components
- If you plan to run CV parsing or matching locally:
```bash
cd AI
python -m venv venv
source venv/bin/activate   # Windows: venv\Scripts\activate
pip install -r requirements.txt
# Run model/processing scripts as documented in AI/
```

Environment variables and configuration:
- Frontend: set API base URL (e.g., NEXT_PUBLIC_API_URL)
- Backend: database connection string, JWT secret, email/SMS providers for notifications
- AI: model paths, GPU/CPU preferences, any required credentials

## Development workflow
- Use feature branches and open PRs against `main`.
- Frontend linting and formatting: run `npm run lint` and `npm run format` if configured.
- Backend: run unit and integration tests with your preferred test runner.
- CI/CD: frontend deploys via Vercel on pushes to `main`; backend CI/CD can be added with GitHub Actions or container pipelines.

## Contributing
We welcome contributions and improvements. Please:
1. Fork the repository
2. Create a feature branch
3. Add tests for significant changes
4. Open a pull request describing the change and relevant context

For major changes, open an issue first to discuss scope and design.

## Team
This graduation project was built by:
- Nadine Khaled
- Omar Sameh
- Dina Ahmed
- Esraa Gomaa
- Dina Abdelnasser
- Maryam Mohamed
- Ameen Sayed
- Shady Adel
- Mohamed Hosam

 
