# Ripple-Hack

# RIPPLE — One change. Everything realigned.

**An MCP-powered AI agent that identifies the ripple effects of unexpected changes in everyday plans and prepares recovery suggestions for the user to review.**

## The Problem

One unexpected change can affect several connected activities. For example, a delayed flight may affect an airport pickup, hotel check-in and an in-person meeting. People often have to find each commitment and coordinate the changes manually across separate apps.

## The Proposed Solution

Ripple uses an AI agent to inspect a saved itinerary, identify activities that may be affected by a disruption, and explain possible next steps.

The application provides a React-based interface where users can view their itinerary, understand affected activities and review recovery suggestions.

The user remains in control: the prototype records the user's approval or rejection locally and does **not** contact external services or modify real bookings.

## What This Prototype Demonstrates

- A React frontend built using JavaScript and Vite.
- A FastAPI backend that connects the frontend to the Python application.
- A Gemini-powered AI agent configured with Pydantic AI.
- A local FastMCP server exposing tools through the Model Context Protocol.
- Pydantic AI connecting to the MCP server over STDIO using `MCPToolset`.
- An SQLite database containing a sample travel itinerary and user decisions.
- Deterministic Python rules for analysing a flight delay.
- A user interface for reviewing and approving or rejecting recovery suggestions.
- An offline demo mode that runs deterministic recovery rules without an LLM API call.

## Architecture

```text
User
  |
  v
React Frontend
  |
  | HTTP / JSON
  v
FastAPI Backend
  |
  v
Ripple Agent (Pydantic AI)
  |                    |
  |                    v
  |                Gemini Model
  |
  v
MCP Client / MCPToolset
  |
  | STDIO
  v
Ripple FastMCP Server
  |
  |-- get_saved_itinerary
  |-- analyze_flight_delay
  |
  v
SQLite + Deterministic Recovery Logic
  |
  v
Recovery Suggestions
  |
  v
React Approval Interface
  |
  v
FastAPI records user's decision in SQLite
```

## Project Structure

```text
ripple-ai-agent/
│
├── backend/
│   ├── __init__.py
│   ├── app.py
│   ├── agent.py
│   ├── mcp_server.py
│   ├── database.py
│   ├── recovery_logic.py
│   │
│   ├── data/
│   │   └── sample_itinerary.json
│   │
│   └── requirements.txt
│
├── frontend/
│   ├── index.html
│   ├── package.json
│   ├── .env.example
│   │
│   └── src/
│       ├── main.jsx
│       ├── App.jsx
│       ├── styles.css
│       │
│       ├── components/
│       │   ├── ChatPanel.jsx
│       │   ├── Itinerary.jsx
│       │   ├── RecoveryPlan.jsx
│       │   └── ApprovalPanel.jsx
│       │
│       └── services/
│           └── api.js
│
├── tests/
│   └── test_recovery_logic.py
│
├── backend/
│   └── .env.example
│
├── .gitignore
└── README.md
```

## Requirements

- Python 3.11 or newer.
- Node.js and npm.
- A Google Gemini API key for AI-agent mode. API availability, model access and free-tier limits depend on the provider and account.
- Internet access for AI-agent mode and package installation.

The offline demo does not require a Gemini API key.

## Run on Windows (PowerShell)

### 1. Download or clone the repository

```powershell
git clone <YOUR_GITHUB_REPOSITORY_URL>
cd ripple-ai-agent
```

### 2. Set up the Python backend

Create and activate a virtual environment:

```powershell
py -m venv .venv
.venv\Scripts\Activate.ps1
```

Install the backend dependencies:

```powershell
python -m pip install --upgrade pip
pip install -r backend/requirements.txt
```

### 3. Configure the Gemini API key

```powershell
Copy-Item backend\.env.example backend\.env
```

Open `backend/.env` and replace the placeholder with your Gemini API key.

Do not commit `.env` or expose your API key on GitHub.

### 4. Start the FastAPI backend

From the repository root:

```powershell
uvicorn backend.app:app --reload --host 127.0.0.1 --port 8000
```

The backend handles API requests from React and coordinates the AI agent.

The local MCP server is started automatically by the Pydantic AI agent when AI-agent mode runs. It communicates over STDIO.

### 5. Set up the React frontend

Open a second terminal:

```powershell
cd frontend
npm install
```

Configure the frontend API address:

```powershell
Copy-Item .env.example .env.local
```

The frontend environment file should contain:

```text
VITE_API_BASE_URL=http://127.0.0.1:8000
```

Start the React development server:

```powershell
npm run dev
```

Open the local URL displayed in the terminal, normally:

```text
http://localhost:5173
```

The React application communicates with FastAPI using HTTP requests and JSON responses.

## Run the Offline Demo

Start both the FastAPI backend and React frontend.

Select **Run Offline Demo (No API Key)** in the interface.

This exercises the deterministic recovery rules and approval interface without calling Gemini.

It is not an LLM or AI-agent run. Use **Ask Ripple AI Agent** to demonstrate the Gemini-powered agent calling the MCP tools.

## Run Tests

From the repository root:

```powershell
python -m unittest discover -s tests -v
```

The tests cover:

- The sample five-hour flight delay.
- Identification of affected activities.
- Unaffected activities.
- Invalid delay values.

## Demo Scenario

The sample itinerary is intentionally fictional:

- Flight RP-501: scheduled arrival 10:00.
- Reported delay: 5 hours; updated expected arrival 15:00.
- Estimated city arrival: 16:00, assuming a 60-minute airport-to-city transfer.
- Airport cab pickup: 10:30.
- Hotel check-in: 12:00.
- Client meeting: 16:00.
- Dinner reservation: 19:30.

The sample rules flag the cab, hotel and meeting for review while leaving dinner unflagged.

These are demonstration results based on assumptions, not live flight tracking or verified booking advice.

## Safety and Limitations

- All travel and booking information is fictional sample data.
- No live airline, hotel, taxi, calendar or email integration is included in this initial version.
- An estimated arrival time is not a confirmed flight status.
- Ripple creates suggestions only. It does not execute external actions.
- User approvals and rejections are stored locally in SQLite.
- A real deployment would need authenticated provider integrations, fresh status data, explicit per-action approval, error handling, audit logs and safeguards against duplicate or irreversible actions.
- The backend must validate incoming requests rather than trusting data sent by the frontend.
- Do not put real personal travel information or API credentials in the public demo repository.

## Future Scope

- Connect a real calendar MCP server with user-authorized access.
- Add trusted travel-status and booking providers where APIs are available.
- Add per-action approval and execution with confirmation receipts.
- Track whether each approved action succeeded or failed.
- Support other disruptions such as cancelled appointments and delayed deliveries.
- Improve the React dashboard with real-time recovery-plan updates.

## Technology Stack

React · JavaScript · Vite · FastAPI · Python · Gemini API · Pydantic AI · FastMCP · Model Context Protocol · SQLite

## Team

- Lakshya
- Naidile
- Supriya
- Amruta
