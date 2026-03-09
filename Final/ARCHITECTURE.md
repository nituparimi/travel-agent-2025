# AI Travel Agent Architecture

## Overview

The AI Travel Agent is a voice-enabled conversational system that allows
users to search for flights and hotels and complete bookings using
natural language.

The system combines:

-   Voice processing
-   Large Language Models
-   Model Context Protocol (MCP) architecture
-   External travel APIs

to create a multi-turn conversational travel booking assistant.

The architecture is designed to be:

-   Modular
-   Scalable
-   API-driven
-   Tool-oriented

------------------------------------------------------------------------

# High-Level Architecture

User (Voice / Browser)
        │
        ▼
Frontend (React + Voice UI)
        │
        ▼
Speech-to-Text
(Google Gemini Live API)
        │
        ▼
FastAPI Backend
        │
        ▼
AI Reasoning Layer
(AWS Bedrock - Claude)
        │
        ▼
MCP Router
(Intent → Tool Mapping)
        │
        ├── Flight Search Tool (Amadeus API)
        ├── Flight Booking Tool
        ├── Hotel Search Tool (Google Places API)
        └── Hotel Booking Tool (Mock Engine)
        │
        ▼
Response Generation
        │
        ▼
Text-to-Speech
(Web Speech API)
        │
        ▼
User

------------------------------------------------------------------------

# Core Architecture Layers

## 1. Frontend Layer

Technology: - React - TypeScript - Vite - Tailwind CSS

Responsibilities:

-   Capture microphone input
-   Send transcripts to backend
-   Display conversational responses
-   Play AI voice responses
-   Show flight/hotel results

------------------------------------------------------------------------

## 2. Voice Processing Layer

### Speech-to-Text

Uses: Google Gemini Live API

Purpose: - Real-time speech transcription - English language filtering -
Streaming transcription

Example output:

User Speech: "Find flights from San Jose to New York tomorrow"

Transcription:

{ "text": "Find flights from San Jose to New York tomorrow", "language":
"en" }

------------------------------------------------------------------------

### Text-to-Speech

Uses: Web Speech API

Purpose:

-   Convert AI text responses into voice
-   Provide conversational feedback

Example:
  AI Response:
"I found several flights. Do you prefer one-way or round-trip?"

------------------------------------------------------------------------

## 3. Backend Layer

Framework: FastAPI

Responsibilities:

-   API endpoints
-   LLM orchestration
-   MCP routing
-   Tool execution
-   Conversation state management

Main entry point:

backend/app.py

Key endpoint:

POST /chat

Example request:

{ "message": "Find flights from SFO to NYC" }

------------------------------------------------------------------------

## 4. AI Reasoning Layer

Model: AWS Bedrock (Claude)

Claude performs:

-   Intent extraction
-   Slot filling
-   Conversation management
-   Tool selection guidance

Example extraction:

Intent: search_flights

Slots:

-   origin: San Jose
-   destination: New York
-   departure_date: 2026-12-25
-   return_date: null
-   budget: null

------------------------------------------------------------------------

## 5. MCP Architecture

Model Context Protocol (MCP) separates AI reasoning from tool execution.

Benefits:

-   Modular tools
-   Easier scaling
-   Clean AI prompts
-   Debuggable execution

Flow:

User Message → Claude → MCP Router → Tool → External API

------------------------------------------------------------------------

## 6. Intent Detection

Supported intents:

-   search_flights
-   book_flight
-   search_hotels
-   book_hotel
-   greeting
-   farewell
-   help

Example:

User: "Show me hotels in Paris"

Intent detected: search_hotels

------------------------------------------------------------------------

## 7. Slot Filling

Required data is collected before tool execution.

Example for flight search:

Required slots:

-   origin
-   destination
-   departure_date
-   trip_type
-   budget (optional)

Conversation example:

User: Find flights from San Jose to New York\
AI: One-way or round trip?\
User: Round trip\
AI: When would you like to return?\
User: January 2nd

Once filled, the search_flights tool executes.

------------------------------------------------------------------------

## 8. Tool Layer

Location:

backend/tools/

### Flight Search Tool

Uses Amadeus Flight API.

Capabilities:

-   Search flights
-   Filter by date
-   Budget filtering

Example response:

\[ { "airline": "United", "price": 520, "departure": "SFO", "arrival":
"JFK", "duration": "5h 30m" }\]

------------------------------------------------------------------------

### Flight Booking Tool

Simulated booking confirmation.

Example:

Booking ID: FLT-483920\
Status: Confirmed

------------------------------------------------------------------------

### Hotel Search Tool

Uses Google Places API.

Capabilities:

-   Discover hotels
-   Ratings
-   Address
-   Nearby locations

Example:

{ "name": "Hilton Midtown", "rating": 4.5, "price_range": "\$\$\$" }

------------------------------------------------------------------------

### Hotel Booking Tool

Mock booking engine.

Example confirmation:

HOTEL-294018

------------------------------------------------------------------------

## 9. Conversation Memory

Session context stores:

-   user intent
-   filled slots
-   conversation history
-   pending questions

Example state:

{ "intent": "search_flights", "origin": "SFO", "destination": "JFK",
"departure_date": "2026-12-25", "return_date": null }

------------------------------------------------------------------------

## 10. Error Handling

Handles:

-   Missing slots
-   API failures
-   Invalid inputs

Example:

AI: I couldn't find flights under \$300. Would you like to increase your
budget?

------------------------------------------------------------------------

## 11. Voice Feedback Protection

To avoid microphone feedback loops:

AI speaking → Microphone paused\
Speech ends → Microphone resumed

------------------------------------------------------------------------

# Project Directory Structure

travel-agent/

backend/ core/ llm.py router.py slots.py

tools/ flights.py hotels.py booking.py

app.py

src/ components/ services/ voiceService.ts mcpClient.ts

README.md ARCHITECTURE.md

------------------------------------------------------------------------

# Request Lifecycle Example

User:

"Find flights from San Jose to New York on December 25"

Pipeline:

Voice → Transcript → Backend → Claude → MCP Router → Amadeus API →
Results → Response → Speech

------------------------------------------------------------------------

# Security

-   API keys stored in .env
-   Input validation
-   Rate limiting

Example .env:

AMADEUS_API_KEY= GOOGLE_PLACES_KEY= AWS_BEDROCK_KEY= GEMINI_API_KEY=

------------------------------------------------------------------------

# Scalability

Future improvements:

-   Redis session store
-   Streaming responses
-   Kubernetes deployment
-   Vector memory
-   Multi-language support

------------------------------------------------------------------------

# Summary

The AI Travel Agent demonstrates a modern voice-first AI architecture
combining:

-   LLM reasoning
-   MCP tool orchestration
-   Voice interaction
-   Real travel APIs

This design enables natural conversations while maintaining
deterministic backend execution.
