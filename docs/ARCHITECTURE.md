# JobPilot AI - Technical Architecture

## 1. Overview

JobPilot AI is built as a modular monolith, combining a modern web dashboard with a background automation system. The architecture prioritizes local-first operation, free and open-source software, and modularity to facilitate future integrations and deployments. This document details the technical design, technology stack, and inter-service communication.

## 2. Core Principles

*   **Local-first:** Prioritizes local execution and data storage.
*   **Free Software First:** Emphasizes open-source and free components to minimize recurring costs.
*   **Modular Monolith:** Structured into distinct modules/packages that can be developed and potentially deployed independently, while residing within a single codebase.
*   **TypeScript Everywhere:** Ensures type safety and improves code maintainability across the stack.
*   **Database as Source of Truth:** PostgreSQL is central for persistent storage and state management.
*   **Asynchronous Processing:** Long-running tasks (job discovery, AI inference, resume generation) are handled by background workers to maintain UI responsiveness.
*   **Explainable AI:** AI decisions are transparent and grounded in verifiable data.
*   **Security & Privacy:** Built-in safeguards for user data and external interactions.

## 3. Technology Stack

### Frontend
*   **Framework:** React (for building user interfaces)
*   **Language:** TypeScript
*   **Build Tool:** Vite (fast development server and build)
*   **Styling:** Tailwind CSS (utility-first CSS framework)
*   **Routing:** React Router (declarative routing for React apps)
*   **Charting:** Recharts (composable charting library built with React and D3)
*   **Component Library:** (To be selected based on accessibility and maintainability needs)

### Backend (API)
*   **Runtime:** Node.js
*   **Language:** TypeScript
*   **Framework:** Express (minimalist web framework)
*   **Validation:** Zod (TypeScript-first schema declaration and validation library)
*   **ORM:** Prisma (Next-generation ORM for Node.js and TypeScript)

### Database
*   **Primary Database:** PostgreSQL (robust, open-source relational database)

### AI
*   **Local Inference Engine:** Ollama (for running large language models locally)
*   **Models:** Configurable small instruction-tuned model (e.g., Llama 3, Phi-3, Mistral) and a compatible local embedding model.
*   **Abstraction Layer:** Custom AI provider interface in `packages/ai` to abstract model interactions and allow future integration with external providers if explicitly enabled.

### Background Processing
*   **Job Queue:** Redis (in-memory data structure store, used as a message broker)
*   **Job Processor:** BullMQ (robust, performant, and Redis-backed queue for Node.js)
*   **Scheduler:** Custom scheduler for recurring tasks (e.g., daily job discovery, report generation).

### Browser Automation
*   **Tool:** Playwright (Node.js library to automate Chromium, Firefox and WebKit with a single API)
*   **Contexts:** Dedicated browser profiles for secure and isolated automation.
*   **Adapters:** Explicitly supported application adapters to interact with specific job platforms.

### Documents
*   **Parsing:** Libraries for PDF and DOCX parsing (e.g., `pdf-parse`, `mammoth.js`).
*   **Generation:** DOCX template generation, PDF conversion via local tools or libraries.

### Email
*   **Library:** Nodemailer or provider-specific adapters (for sending email reports).
*   **Authentication:** Secure OAuth or provider-supported methods.

### Testing
*   **Unit/Integration:** Vitest (fast unit testing framework), Supertest (for API testing).
*   **End-to-End (E2E):** Playwright (for full application flow testing).

### Infrastructure
*   **Containerization:** Docker (for packaging applications and services).
*   **Orchestration:** Docker Compose (for defining and running multi-container Docker applications locally).
*   **CI/CD:** GitHub Actions (for automated builds, tests, and deployments where practical).

## 4. Architecture Diagram

```mermaid
graph TD
    A[User] --> B(React Dashboard)
    B --> C(Express API)

    C --> D(Profile Service)
    C --> E(Resume Service)
    C --> F(Job Discovery Service)
    C --> G(Matching Service)
    C --> H(Application Service)
    C --> I(Interview Service)
    C --> J(Notification Service)
    C --> K(Settings Service)

    K --> L(Local AI)
    K --> M(Job Connectors)
    K --> N(Document Generation)
    K --> O(Authorized Browser Automation)
    K --> P(Email Provider)

    C -- Manages --> Q(Scheduler and Worker)
    Q --> R(Background Jobs/BullMQ)
    R --> L
    R --> M
    R --> N
    R --> O
    R --> P
    R --> S(PostgreSQL)

    D --> S
    E --> S
    F --> S
    G --> S
    H --> S
    I --> S
    J --> S
    K --> S

    Redis -- Job Queue --> R

    PostgreSQL <--> Prisma
    Redis <--> BullMQ
    Ollama <--> Local AI
```

## 5. Module Breakdown

*   **`apps/web` (Frontend):** Responsive web application, user interface, client-side routing, data visualization.
*   **`apps/api` (Backend API):** RESTful API endpoints, request handling, input validation, authentication, business logic orchestration, interacts with services and database.
*   **`apps/worker` (Background Worker):** Processes long-running and scheduled tasks (job discovery, AI inference, resume generation, email reports) using BullMQ.
*   **`packages/shared`:** Contains shared types, interfaces, utility functions, and constants used across the monorepo.
*   **`packages/database`:** Encapsulates Prisma schema, database migrations, and direct database access logic. Defines the relational schema and handles data persistence.
*   **`packages/ai`:** Provides an abstraction layer for AI model interactions, handles local Ollama integration, structured output parsing, error handling, and model switching.
*   **`packages/job-connectors`:** Contains implementations for various job discovery sources, responsible for fetching raw job data and normalizing it into a common format.
*   **`packages/resume-engine`:** Manages resume parsing, text extraction, project relevance calculation, job-specific resume content generation, and document output.
*   **`packages/application-engine`:** Handles the application lifecycle, state transitions, approval queue, submission logic (mocked or automated), and event history.
*   **`packages/notifications`:** Manages email reporting and potentially other notification mechanisms (e.g., in-app notifications).
*   **`packages/validation`:** Centralizes schema validation logic, primarily using Zod, to ensure data integrity at API boundaries and internal processes.

## 6. Data Flow and Interactions

1.  **User Interaction:** The React Dashboard (`apps/web`) sends requests to the Express API (`apps/api`).
2.  **API Processing:** The API validates requests, orchestrates business logic by interacting with various services (Profile, Resume, Job Discovery, etc.), and uses Prisma to communicate with PostgreSQL.
3.  **Background Tasks:** For long-running operations, the API enqueues jobs into Redis via BullMQ. The `apps/worker` constantly monitors this queue, processes jobs, and updates the database.
4.  **AI Integration:** Both the API (for immediate actions) and the Worker (for batch processing) interact with the `packages/ai` layer, which in turn communicates with the local Ollama instance.
5.  **External Services:** Job Connectors, Email Providers, and Browser Automation adapters (managed by the Worker) interact with external APIs/services.
6.  **Database:** PostgreSQL stores all persistent data, including user profiles, job listings, applications, events, and configuration.
7.  **Redis:** Primarily used by BullMQ for managing the job queue and providing Pub/Sub capabilities for worker-related events.

## 7. Security Considerations

*   **Secret Management:** Environment variables for sensitive configuration; `.env.example` with placeholders only.
*   **Authentication/Authorization:** Secure session management for local user. Input validation across all API endpoints.
*   **AI Safety:** Prompt injection prevention, AI-generated content validation before external submission.
*   **Browser Automation:** Dedicated browser contexts, no CAPTCHA circumvention, explicit user approval for consequential actions.
*   **Least Privilege:** Adherence to least-privilege principles for all integrations.

## 8. Deployment Strategy

The initial focus is on a fully functional local development environment using Docker Compose. Future deployments will evaluate options based on free-tier limits for persistent processes, databases, background jobs, and AI inference capabilities.