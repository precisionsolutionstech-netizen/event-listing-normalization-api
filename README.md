# Event Listing Normalization API

Normalize event data from Eventbrite, Ticketmaster, Meetup, Facebook, Songkick, and other platforms into a single canonical event schema.

This REST API converts heterogeneous event payloads into a consistent JSON structure with standardized fields for time, location, pricing, organizer, availability, and metadata.

API Access:  
https://rapidapi.com/precisionsolutionstech/api/event-listing-normalization-api

---

## Overview

Event platforms use different field names, nesting structures, date formats, and location representations. Integrating multiple sources typically requires custom parsers per platform.

The Event Listing Normalization API accepts raw platform event payloads and returns a normalized, deterministic canonical schema.

The API is:

- Stateless (no data stored)
- Deterministic (same input → same output)
- Platform-aware (when platform is specified)
- Fallback-capable (generic normalization supported)
- Designed for event aggregation, ETL, search indexing, calendar sync, and analytics pipelines

---

## Supported Platforms (Examples)

Eventbrite  
Ticketmaster  
Meetup  
Facebook  
Bandsintown  
Songkick  
SeatGeek  
StubHub  
Eventfinda  
YouTube (event streams)  
Twitch (event streams)  

Unknown platforms may use generic normalization logic.

---

## Canonical Event Schema (High-Level)

Each normalized event includes:

- id  
- title  
- description  
- startTimeIso  
- endTimeIso  
- timezone  
- durationMinutes  
- location (physical | virtual | hybrid)  
- pricing (min, max, currency, isFree)  
- availability (capacity, remaining, soldOut)  
- organizer  
- categories  
- tags  
- images  
- url  
- provenance (platform + parsing strategy)  
- interpretation (method + confidence score)

All outputs follow the same JSON structure regardless of source platform.

---

## Endpoint

### POST /normalize

Normalize one or more event payloads.

You may send either:

- `payload` (single platform)
- `inputs` (mixed platforms)

### Example (Single Platform)

    curl -X POST "https://event-listing-normalization-api.p.rapidapi.com/normalize" \
      -H "Content-Type: application/json" \
      -H "x-rapidapi-key: YOUR_RAPIDAPI_KEY" \
      -H "x-rapidapi-host: event-listing-normalization-api.p.rapidapi.com" \
      -d '{
        "platform": "eventbrite",
        "payload": {
          "id": "123",
          "name": "Tech Meetup",
          "start": "2024-06-15T18:00:00Z",
          "end": "2024-06-15T20:00:00Z"
        }
      }'

Response:

    {
      "events": [
        {
          "id": "123",
          "title": "Tech Meetup",
          "startTimeIso": "2024-06-15T18:00:00.000Z",
          "endTimeIso": "2024-06-15T20:00:00.000Z",
          "location": {
            "type": "physical"
          },
          "provenance": {
            "platform": "eventbrite"
          },
          "interpretation": {
            "confidence": 0.95
          }
        }
      ],
      "errors": []
    }

---

## Mixed Platform Normalization

Multiple platforms can be normalized in a single request:

    {
      "inputs": [
        { "platform": "eventbrite", "data": [...] },
        { "platform": "meetup", "data": [...] }
      ]
    }

The response returns one unified `events` array.

---

## Use Cases

- Event aggregation platforms  
- Unified search across event providers  
- Calendar synchronization pipelines  
- Data ingestion / ETL workflows  
- Analytics pipelines  
- Notification systems  
- Event discovery applications  

---

## Error Handling

The API returns per-item errors without failing the entire request.

Common error codes:

- INVALID_REQUEST  
- INVALID_JSON  
- INVALID_STRUCTURE  
- PAYLOAD_TOO_LARGE  
- INTERNAL_ERROR  

Maximum request size: 25MB

---

## Health Check

### GET /health

    { "status": "ok" }

---

## Deterministic & Stateless

- No payloads are stored  
- No outbound platform calls are made  
- Output depends only on provided input  
- Same input produces the same normalized output  

---

## API Access

Subscribe and obtain an API key via RapidAPI:

https://rapidapi.com/precisionsolutionstech/api/event-listing-normalization-api
