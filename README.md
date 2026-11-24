# Ripple-Hack
# RIPPLE — One change everything realigned.

**An MCP-powered AI agent that identifies the ripple effects of unexpected changes in everyday plans and prepares recovery suggestions for the user to review.**

## The problem

One unexpected change can affect several connected activities. For example, a delayed flight may affect an airport pickup, hotel check-in and an in-person meeting. People often have to find each commitment and coordinate the changes manually across separate apps.

## The proposed solution

Ripple uses an AI agent to inspect a saved itinerary, identify activities that may be affected by a disruption, and explain possible next steps. The user remains in control: the prototype records the user's approval or rejection locally and does **not** contact external services or modify real bookings.

## What this prototype demonstrates

- A Gemini-powered agent configured with Pydantic AI.
- A local FastMCP server exposing tools through the Model Context Protocol.
- Pydantic AI connecting to that MCP server over STDIO using `MCPToolset`.
- An SQLite database containing a sample travel itinerary and user decisions.
- Deterministic Python rules for analysing a flight delay.
- A Streamlit interface for reviewing and approving/rejecting suggestions.
- An offline demo mode that runs the deterministic rules without an LLM API call.

## Architecture

```text
User
  |
  v
Streamlit interface
  |
  v
Ripple agent (Pydantic AI)
  |           Gemini model (Google API)
  |
  v
MCP client / MCPToolset
  |
  | STDIO
  v
Ripple FastMCP server
  |-- get_saved_itinerary
  |-- analyze_flight_delay
  |
  v
SQLite + deterministic recovery logic
  |
  v
Suggestions -> user review -> local decision record
```

## Project structure

```text
ripple-ai-agent/
├── app.py                    # Streamlit interface
├── agent.py                  # Gemini + Pydantic AI + MCP client
├── mcp_server.py             # FastMCP server and tools
├── database.py               # SQLite setup and queries
├── recovery_logic.py          # Disruption analysis rules
├── data/
│   └── sample_itinerary.json  # Safe sample data for the demo
├── tests/
│   └── test_recovery_logic.py
├── .env.example
├── .gitignore
└── requirements.txt
```

## Requirements

- Python 3.11 or newer.
- A Google Gemini API key to run the AI-agent mode. API availability, model access and free-tier limits depend on the provider and account.
- Internet access for the AI-agent mode and package installation.

The offline demo does not need an API key.

## Run on Windows (PowerShell)

1. Download or clone this repository.

```powershell
git clone <YOUR_GITHUB_REPOSITORY_URL>
cd ripple-ai-agent
```

2. Create and activate a virtual environment.

```powershell
py -m venv .venv
.venv\Scripts\Activate.ps1
```

3. Install dependencies.

```powershell
python -m pip install --upgrade pip
pip install -r requirements.txt
```

4. Configure the Gemini key for AI-agent mode.

```powershell
Copy-Item .env.example .env
```

Open `.env` and replace the placeholder with your own key. Do not commit `.env` to GitHub.

5. Start the application.

```powershell
streamlit run app.py
```

The local MCP server is started automatically as a subprocess by Pydantic AI when the agent runs. It communicates over STDIO.

## Run the offline demo

Start the Streamlit app and select **Run offline demo (no API key)**. This exercises the deterministic recovery rules and approval interface. It is not an LLM/agent run; use **Ask Ripple AI Agent** to demonstrate the Gemini-powered agent calling MCP tools.

## Run tests

```powershell
python -m unittest discover -s tests -v
```

The tests cover the sample five-hour delay, unaffected activities and invalid delay values.

## Demo scenario

The sample itinerary is intentionally fictional:

- Flight RP-501: scheduled arrival 10:00.
- Reported delay: 5 hours; updated expected arrival 15:00.
- Estimated city arrival: 16:00, assuming a 60-minute airport-to-city transfer.
- Airport cab pickup: 10:30.
- Hotel check-in: 12:00.
- Client meeting: 16:00.
- Dinner reservation: 19:30.

The sample rules flag the cab, hotel and meeting for review while leaving dinner unflagged. These are demonstrations based on assumptions, not live flight tracking or verified booking advice.


## Safety and limitations

- All travel and booking information is fictional sample data.
- No live airline, hotel, taxi, calendar or email integration is included in this initial version.
- An estimated arrival time is not a confirmed flight status.
- Ripple creates suggestions only. It does not execute external actions.
- A real deployment would need authenticated provider integrations, fresh status data, explicit per-action approval, error handling, audit logs and safeguards against duplicate or irreversible actions.
- Do not put real personal travel information or API credentials in this public demo repository.

## Future scope

- Connect a real calendar MCP server with user-authorized access.
- Add trusted travel-status and booking providers where APIs are available.
- Add per-action approval and execution with confirmation receipts.
- Track whether each approved action succeeded or failed.
- Support other disruptions such as cancelled appointments or delayed deliveries.

## Technology stack

Python · Gemini API · Pydantic AI · FastMCP · Model Context Protocol · SQLite · Streamlit

## Team

Lakshya,
Naidile,
Supriya,
Amruta.
