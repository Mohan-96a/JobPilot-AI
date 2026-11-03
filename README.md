# JobPilot AI

## AI-powered Job Discovery, Resume Optimization, Application Management, and Interview Coordination Platform

JobPilot AI is an intelligent personal recruitment assistant designed to streamline the job search process for software engineering job seekers. It leverages AI to discover relevant jobs, analyze descriptions, customize resumes, manage applications, track interviews, and provide scheduled reports, aiming to reduce repetitive work and improve application effectiveness.

This project is built with a local-first, free-software-first, modular monolith approach, prioritizing privacy, security, and transparent AI decisions.

## Features (MVP - Minimum Viable Product)

*   **Responsive Dashboard:** Central hub for all activities.
*   **User Profile & Preferences:** Configurable professional details and job search criteria.
*   **Master Resume Upload & Parsing:** Supports PDF/DOCX, extracts text, and allows manual corrections.
*   **Job Discovery:** Connects to permitted sources, normalizes, and deduplicates listings.
*   **Eligibility Filtering:** Filters jobs based on user-defined criteria.
*   **Explainable Job Compatibility Scoring:** AI-powered score with detailed explanations.
*   **Local AI Integration:** Utilizes Ollama and local models for core AI functionalities.
*   **Job-Specific Resume Generation:** Creates tailored resumes without fabrication, with version history.
*   **Application Tracker:** Manages application states, events, and documents.
*   **Approval Queue:** Requires human approval for uncertain or consequential actions.
*   **Daily Scheduler & Email Reports:** Automates tasks and sends daily progress reports.
*   **Agent Activity Logs & Controls:** Provides visibility and control over automation.
*   **Database Persistence:** Uses PostgreSQL for reliable data storage.
*   **Automated Tests:** Ensures reliability and correctness.
*   **Docker-based Local Development:** Simplifies setup and environment management.
*   **Secure Configuration Management:** Handles sensitive data securely.

## Technology Stack

*   **Frontend:** React, TypeScript, Vite, Tailwind CSS, React Router, Recharts
*   **Backend:** Node.js, TypeScript, Express, Zod, Prisma
*   **Database:** PostgreSQL
*   **AI:** Ollama (with configurable small instruction-tuned and embedding models)
*   **Background Processing:** Redis, BullMQ
*   **Browser Automation:** Playwright (for assisted/automatic applications)
*   **Documents:** PDF/DOCX parsing libraries, DOCX template generation
*   **Email:** Nodemailer or provider-specific adapter
*   **Testing:** Vitest, Supertest, Playwright (E2E)
*   **Infrastructure:** Docker, Docker Compose

## Getting Started

Detailed setup and development instructions will be provided in the `SETUP.md` document.

## Project Structure

```
jobpilot-ai/
├── apps/
│   ├── web/           # React frontend application
│   ├── api/           # Node.js Express backend API
│   └── worker/        # Node.js background worker processes
├── packages/
│   ├── shared/        # Shared utilities, types, and constants
│   ├── database/      # Prisma schema, migrations, and database access logic
│   ├── ai/            # AI provider abstraction and local AI integration
│   ├── job-connectors/ # Integrations for job discovery sources
│   ├── resume-engine/ # Logic for resume parsing, generation, and validation
│   ├── application-engine/ # Logic for application management, state transitions, and submission
│   ├── notifications/ # Email and other notification services
│   └── validation/    # Schema validation (e.g., Zod)
├── docs/              # Project documentation (PRD, Architecture, Setup, API, Security, Testing, Deployment, Operations)
├── scripts/           # Utility scripts for development, build, and deployment
├── tests/             # End-to-end tests and integration tests
├── docker-compose.yml # Docker Compose configuration for local services (PostgreSQL, Redis, Ollama)
├── .env.example       # Example environment variables
├── .gitignore         # Git ignore rules
├── package.json       # Workspace package.json
└── README.md          # Project overview
```
