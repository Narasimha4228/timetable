# Automated Timetable Generation System

---

## 📌 Overview

This project is a **production-grade Automated Timetable Generation System** that uses a **Genetic Algorithm (GA)** to generate conflict-free academic schedules and integrates an **LLM layer** for intelligent analysis, reporting, and insights.

Unlike basic rule-based schedulers, this system:

* Evolves schedules using **evolutionary optimization**
* Resolves hard and soft constraints intelligently
* Uses adaptive mutation to escape local minima
* Provides AI-powered timetable explanations and quality analysis
* Runs asynchronously in the background without blocking the UI

---

# 🚀 Core Concept

> **Survival of the Best Schedule**

The system:

1. Generates 150 random timetable candidates
2. Scores them using a fitness function
3. Selects the best performers
4. Crosses and mutates them
5. Repeats this for up to 500 generations
6. Stops early if a perfect schedule is found

A timetable with **fitness ≥ 0** is considered conflict-free.

---

# 🧠 Genetic Algorithm Design

**Genetic Algorithm**

![Image](https://github.com/Jayachandra2911/timetable/blob/main/Genitic_Ai.jpeg)

## 1️⃣ Chromosome Structure

* **Chromosome** → A complete timetable
* **Gene** → A single scheduled class session

  * Day
  * Time slot
  * Course
  * Faculty
  * Room
  * Section

---

## 2️⃣ Initialisation Phase

**Population Size:** 150
**Generations:** 500
**Mutation Rate:** 35% (adaptive up to 60%)

The system:

* Breaks courses into individual sessions
* Randomly assigns faculty, room, and timeslot
* Applies greedy placement to reduce obvious conflicts

---

## 3️⃣ Fitness Evaluation

Each timetable is evaluated using a weighted penalty system.

### 🔴 Hard Conflicts (-100 each)

* Faculty double booking
* Faculty scheduled during busy slot
* Room double booking
* Section double booking

### 🟡 Soft Conflicts

* Room-type mismatch → -10
* Gap hour penalty → -2 per hour
* > 4 consecutive classes → -3
* Day overload imbalance → dynamic penalty

**Goal:** Maximize fitness score (≥ 0 = perfect)

---

## 4️⃣ Evolution Strategy

### Elitism

The top 6 timetables automatically survive each generation.

### Tournament Selection

Randomly select 5 → choose the best as parent.

### Crossover (Uniform)

Each gene is randomly inherited from Parent A or Parent B.

### Mutation Strategies

* Move session to a new timeslot
* Change faculty
* Change room
* Swap two sessions
* Adaptive mutation rate increases if stagnation > 30 generations

### Early Stopping

If any timetable reaches fitness ≥ 0 → terminate immediately.

---

# 🏗️ System Architecture

**System Architecture**

![Image](https://raw.githubusercontent.com/EKarton/UofT-Timetable-Generator/master/docs/images/System%20Architecture.PNG)

## High-Level Layers

### 1. Generation Engine (Python)

* Implements GA
* Conflict detection
* Fitness evaluation
* Adaptive mutation
* Early termination

### 2. API Layer (FastAPI)

* REST endpoints
* Background generation tasks
* Timetable retrieval
* AI analysis endpoints

### 3. Database (MongoDB + Beanie ODM)

Collections:

* Faculty
* Course
* Room
* Program
* Section
* Timetable

### 4. AI / LLM Layer (Groq - llama-3.3-70b)

Capabilities:

* Timetable Q&A
* Conflict analysis report
* Optimisation suggestions
* Faculty workload insights

### 5. Frontend (Next.js + TypeScript)

* Dashboard
* Timetable visualisation
* Conflict indicators
* AI chat interface

---

# 📂 Project Structure

```
project-root/
│
├── backend/
│   ├── app/
│   │   ├── api/
│   │   │   ├── endpoints/
│   │   │   │   ├── ai.py
│   │   │   │   └── timetables.py
│   │   ├── models/
│   │   ├── generator.py
│   │   └── main.py
│   │
│   ├── .env.example
│   └── requirements.txt
│
├── frontend/
│   ├── app/
│   ├── components/
│   ├── services/
│   └── package.json
│
└── README.md
```

---

# ⚙️ Technology Stack

| Layer            | Technology                        | Purpose                 |
| ---------------- | --------------------------------- | ----------------------- |
| Algorithm        | Custom Genetic Algorithm (Python) | Schedule optimization   |
| Backend          | FastAPI                           | Async REST API          |
| Database         | MongoDB + Beanie ODM              | Data persistence        |
| AI               | Groq API (llama-3.3-70b)          | Analysis & Q&A          |
| HTTP Client      | httpx                             | Async API calls         |
| Frontend         | Next.js (React + TypeScript)      | UI Dashboard            |
| Styling          | Tailwind CSS                      | UI Styling              |
| Background Tasks | FastAPI BackgroundTasks           | Non-blocking generation |

---
# **AI-Powered Genetic Algorithm Scheduler with LLM Analysis**

![Image](https://backend.krishnaik.in/media/project_architecture_diagrams/Architecture_diagram.png)

# 🔌 API Endpoints

## Generate Timetable

```
POST /generate/{program_id}/{batch_id}
```

Runs GA in background.

---

## AI Endpoints

```
POST /ai/chat
POST /ai/analysis
POST /ai/insights
```

### Examples:

* "Who teaches Data Structures on Monday?"
* "Which faculty is overloaded?"
* "Summarize timetable quality"

---

# 🛠 Installation & Setup

## 1️⃣ Clone Repository

```bash
git clone <repository-url>
cd project-root
```

---

## 2️⃣ Backend Setup

```bash
cd backend
cp .env.example .env
```

Fill in:

```
MONGODB_URL=
GROQ_API_KEY=
```

Install dependencies:

```bash
pip install -r requirements.txt
```

Run server:

```bash
uvicorn app.main:app --reload
```

---

## 3️⃣ Frontend Setup

```bash
cd frontend
npm install
npm run dev
```

---

# 🔄 How Generation Works (Technical Flow)

1. Fetch faculty, courses, rooms, sections
2. Expand courses into sessions
3. Create 150 chromosomes
4. Evaluate fitness
5. Repeat:

   * Select parents (Tournament)
   * Crossover
   * Mutate
   * Recalculate fitness
6. Stop if:

   * Perfect solution found
   * 500 generations completed
7. Save best timetable
8. AI layer available for analysis

---

# 📊 Performance Characteristics

| Metric                | Value              |
| --------------------- | ------------------ |
| Population Size       | 150                |
| Max Generations       | 500                |
| Mutation Rate         | 35% → 60% adaptive |
| Elites per Generation | 6                  |
| Selection Method      | Tournament (k=5)   |
| Crossover             | Uniform            |

---

# 🧩 Design Decisions

### Why Genetic Algorithm?

* NP-hard scheduling problem
* Large search space
* Constraint-heavy optimization
* Avoids brute-force combinatorics

### Why Adaptive Mutation?

Prevents:

* Premature convergence
* Local optima stagnation

### Why LLM Layer?

Traditional schedulers:

* Generate timetable
* Stop there

This system:

* Explains quality
* Identifies faculty overload
* Suggests improvements
* Provides natural language interface

---

# 🛡 Conflict Resolution Model

### Hard Constraints

Must never exist in final solution.

### Soft Constraints

Optimized gradually through evolutionary pressure.

This weighted approach ensures:

* Feasible schedule first
* Optimized experience second

---

# 📈 Future Improvements

* Multi-objective optimization
* Distributed GA execution
* Parallel fitness evaluation
* Dynamic constraint weighting
* Faculty preference modeling
* Real-time incremental scheduling

---

# 🧪 Testing Strategy

* Unit tests for:

  * Fitness evaluation
  * Mutation operations
  * Conflict detection
* Integration tests:

  * API endpoints
  * AI response validation
* Load testing:

  * Generation time under multiple concurrent requests

---

# 🏁 Final Summary

This project is not a simple timetable generator.

It is:

* A constraint-optimisation engine
* Built on evolutionary computation
* Integrated with modern async backend architecture
* Enhanced by LLM-driven intelligence
* Designed for scalability and extensibility

It combines:

> **Evolutionary Algorithms + Modern Web Stack + AI Intelligence**

to produce high-quality, explainable academic schedules.

---

If you'd like, I can also generate:

* A shorter GitHub-friendly version
* A resume/project portfolio version
* A technical whitepaper version
* Architecture diagrams in clean SVG format
* Deployment guide (Docker + CI/CD)
