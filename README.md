# Event Listing Normalization API — Examples & Documentation

<p align="center">
  <a href="https://rapidapi.com/precisionsolutionstech/api/event-listing-normalization-api">
    <img src="https://img.shields.io/badge/Try_on_RapidAPI-Event_Listing_Normalization-00A1F1?style=for-the-badge&logo=rapidapi" alt="Try Event Listing Normalization API on RapidAPI" />
  </a>
</p>

**Unify Eventbrite, Ticketmaster, Meetup, Facebook, Songkick, and 50+ event sources into one canonical event schema.** Stop maintaining per-platform parsers, date formats, and location hacks. One API call turns raw event payloads into a single event model—same start/end ISO, location (physical/virtual/hybrid), pricing, organizer, provenance, and confidence. Same input → same output every time. You cannot scale multi-source event aggregation without it.

**[Try the API on RapidAPI →](https://rapidapi.com/precisionsolutionstech/api/event-listing-normalization-api)**

---

## Table of Contents

- [Why You Need This API](#why-you-need-this-api)
- [What is the Event Listing Normalization API?](#what-is-the-event-listing-normalization-api)
- [Topics](#topics)
- [Quick Start](#quick-start)
- [Real-World Examples](#real-world-examples)
- [API Reference](#api-reference)
- [Canonical Event Schema](#canonical-event-schema)
- [Related APIs](#related-apis)
- [FAQ](#faq)

---

## Why You Need This API

**Multi-source event data is a mess.** Every platform uses different field names (`name` vs `title`, `dateTime` vs `start_time`), different nesting (`venue.name`, `location.address`), different date formats, and different structures for location, pricing, and organizer. Building one event feed, one calendar pipeline, or one discovery app means:

- Writing and maintaining **separate parsers** for Eventbrite, Meetup, Ticketmaster, Facebook, Songkick, and more
- **Mapping dozens of date and location formats** to your internal model—and breaking when platforms change
- **Handling virtual vs physical vs hybrid** and join URLs yourself—inconsistent, error-prone
- **Normalizing prices and currencies** across platforms—silent failures and wrong comparisons
- **Fragile logic** that explodes when a new source or webhook format appears

**The cost of doing it yourself:** High integration cost, brittle code, poor observability, and inconsistent analytics. Your calendar or search layer can't compare or filter events across sources because every payload looks different. Notifications and reminders break when one platform uses a different time field. BI and discovery can't aggregate without a single schema.

**With this API:** One request → one canonical event object → zero platform-specific logic downstream. You send raw payloads (single or mixed); you get back **fixed schema** (id, title, start/end ISO, location type, pricing, organizer, provenance, confidence). Event aggregators, discovery apps, calendar sync, and data pipelines all speak one language. You can't manage multi-source events at scale without a single source of truth—this API is that source of truth.

---

## What is the Event Listing Normalization API?

The **Event Listing Normalization API** is a REST API that converts platform-specific event data (Eventbrite, Ticketmaster, Meetup, Facebook, Songkick, Bandsintown, StubHub, SeatGeek, and 50+ more) into one **canonical event schema**. It maps every payload to the same structure: id, title, description, startTimeIso, endTimeIso, location (type: physical | virtual | hybrid, venueName, address, city, url), pricing, availability, organizer, images, categories, tags, url, provenance, and interpretation (method, confidence, warnings). Output is **deterministic** and **stateless**—no data stored, no outbound fetching, 25MB max per request.

### Key Features

| Feature | Description |
|---------|-------------|
| **Single schema** | Every event has the same shape regardless of platform |
| **50+ platforms** | Eventbrite, Ticketmaster, Meetup, Eventful, Facebook, Google, Bandsintown, Songkick, StubHub, SeatGeek, Eventfinda, YouTube, Twitch, and more; unknown use generic fallback |
| **Physical, virtual, hybrid** | Location type and virtual join URLs preserved; no invented data |
| **Provenance & confidence** | Every event includes platform, parsing strategy, and 0–1 confidence score |
| **Partial success** | One bad item does not fail the request; item-scoped errors |
| **Stateless** | No data stored or logged; no platform API keys required |

### Use Cases

- **Event discovery & aggregation** — One event feed for apps that pull from Eventbrite, Meetup, Ticketmaster, and others
- **Calendar & sync** — Normalize webhook or API payloads into one schema for calendar sync, reminders, conflict detection
- **Data pipelines & ETL** — Ingest event JSON from APIs or feeds; normalize in one step before loading into a warehouse or search index
- **Unified search** — One schema so filters (date, location, price, virtual) work across all sources
- **Support & moderation** — One event shape for display, reporting, or moderation tools regardless of source
- **Notifications** — Trigger reminders or alerts from canonical start/end and location without platform-specific logic


## Quick Start

**Endpoint:** `POST /normalize`  
**Try it:** [RapidAPI — Event Listing Normalization API](https://rapidapi.com/precisionsolutionstech/api/event-listing-normalization-api)

### cURL (single platform)

```bash
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
      "end": "2024-06-15T20:00:00Z",
      "venue": { "name": "Main Hall", "address": { "city": "San Francisco" } }
    }
  }'
```

### JavaScript / Node.js

```javascript
const response = await fetch('https://event-listing-normalization-api.p.rapidapi.com/normalize', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'x-rapidapi-key': 'YOUR_RAPIDAPI_KEY',
    'x-rapidapi-host': 'event-listing-normalization-api.p.rapidapi.com'
  },
  body: JSON.stringify({
    platform: 'eventbrite',
    payload: {
      id: '123',
      name: 'Tech Meetup',
      start: '2024-06-15T18:00:00Z',
      end: '2024-06-15T20:00:00Z',
      venue: { name: 'Main Hall', address: { city: 'San Francisco' } }
    }
  })
});
const { events, errors } = await response.json();
console.log(events); // Same schema for every platform
```

### Python

```python
import requests

url = "https://event-listing-normalization-api.p.rapidapi.com/normalize"
headers = {
    "Content-Type": "application/json",
    "x-rapidapi-key": "YOUR_RAPIDAPI_KEY",
    "x-rapidapi-host": "event-listing-normalization-api.p.rapidapi.com"
}
payload = {
    "platform": "eventbrite",
    "payload": {
        "id": "123",
        "name": "Tech Meetup",
        "start": "2024-06-15T18:00:00Z",
        "end": "2024-06-15T20:00:00Z",
        "venue": {"name": "Main Hall", "address": {"city": "San Francisco"}}
    }
}
response = requests.post(url, json=payload, headers=headers)
data = response.json()
print(data["events"])   # One schema for all
print(data["errors"])   # Per-item errors if any
```

### Mixed platforms (inputs)

Send multiple sources in one request. Each input: `platform` + `data` (array of events) or `payload` (single event or array).

```bash
curl -X POST "https://event-listing-normalization-api.p.rapidapi.com/normalize" \
  -H "Content-Type: application/json" \
  -H "x-rapidapi-key: YOUR_RAPIDAPI_KEY" \
  -H "x-rapidapi-host: event-listing-normalization-api.p.rapidapi.com" \
  -d '{
    "inputs": [
      { "platform": "eventbrite", "data": [ { "id": "1", "name": "Concert", "start": "2024-07-01T19:00:00Z" } ] },
      { "platform": "meetup", "data": [ { "id": "2", "name": "Meetup", "dateTime": "2024-07-02T18:00:00Z", "venue": { "name": "Office" } } ] }
    ]
  }'
```

---

## Real-World Examples

### Example 1: Event discovery app (multiple sources, one feed)

You run an event discovery app. You pull from Eventbrite, Meetup, and Ticketmaster. You need one event list and one schema for search, filters, and calendar.

```javascript
const API_URL = 'https://event-listing-normalization-api.p.rapidapi.com/normalize';
const RAPIDAPI_HEADERS = {
  'Content-Type': 'application/json',
  'x-rapidapi-key': process.env.RAPIDAPI_KEY,
  'x-rapidapi-host': 'event-listing-normalization-api.p.rapidapi.com'
};

async function getUnifiedEvents(sources) {
  const inputs = sources.map(({ platform, events }) => ({
    platform,
    data: events
  }));
  const res = await fetch(API_URL, {
    method: 'POST',
    headers: RAPIDAPI_HEADERS,
    body: JSON.stringify({ inputs })
  });
  const { events, errors } = await res.json();
  return { events, errors };  // One schema for your UI—no per-platform rendering
}
```

### Example 2: Webhook → calendar sync (one schema for all platforms)

When a platform webhook fires (new or updated event), normalize once and sync to your calendar or notification system. No if/else per platform.

```javascript
async function onEventWebhook(webhookBody, source) {
  const res = await fetch('https://event-listing-normalization-api.p.rapidapi.com/normalize', {
    method: 'POST',
    headers: RAPIDAPI_HEADERS,
    body: JSON.stringify({
      platform: source,  // e.g. 'eventbrite', 'meetup'
      payload: webhookBody
    })
  });
  const { events } = await res.json();
  const ev = events[0];
  if (ev) {
    await addToCalendar({
      id: ev.id,
      title: ev.title,
      start: ev.startTimeIso,
      end: ev.endTimeIso,
      location: ev.location?.url || ev.location?.venueName || ev.url,
      isVirtual: ev.isVirtual
    });
  }
}
```

### Example 3: Mixed known + unknown payloads (confidence filter)

You have Eventbrite payloads and some CMS or custom JSON. Normalize all; use confidence to index only high-quality events or flag others for review.

```javascript
async function normalizeMixed(knownEvents, unknownEvents) {
  const res = await fetch('https://event-listing-normalization-api.p.rapidapi.com/normalize', {
    method: 'POST',
    headers: RAPIDAPI_HEADERS,
    body: JSON.stringify({
      inputs: [
        { platform: 'eventbrite', data: knownEvents },
        { platform: 'generic', data: unknownEvents }
      ]
    })
  });
  const { events } = await res.json();
  const forSearch = events.filter(e => e.interpretation.confidence >= 0.8);
  const forReview = events.filter(e => e.interpretation.confidence < 0.8);
  return { forSearch, forReview };
}
```

### Example 4: Analytics — one schema for all sources

Load normalized events into your warehouse or BI tool. Same dimensions and measures for every platform.

```python
import requests

def normalize_events_for_analytics(platform_payloads: list) -> list:
    """platform_payloads: [{"platform": "eventbrite", "data": [...]}, ...]"""
    res = requests.post(
        "https://event-listing-normalization-api.p.rapidapi.com/normalize",
        headers={
            "Content-Type": "application/json",
            "x-rapidapi-key": RAPIDAPI_KEY,
            "x-rapidapi-host": "event-listing-normalization-api.p.rapidapi.com"
        },
        json={"inputs": platform_payloads}
    )
    data = res.json()
    return data["events"]  # Same schema → same columns in your DB or BI
```

---

## API Reference

### POST /normalize

Normalize one or more event payloads into the canonical event schema.

**Request body (application/json):**

You must send either **payload** (single-vendor) or **inputs** (mixed).

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `payload` | object or array | For single-vendor | One event object or array of events. Use with optional `platform`. |
| `platform` | string | No | Platform hint for single-vendor, e.g. `eventbrite`, `meetup`, `ticketmaster`, `generic`. |
| `inputs` | array | For mixed | Array of inputs. Each is `{ platform, data: event[] }` or `{ platform?, payload }`. |

**Response (200):**

| Field | Type | Description |
|-------|------|-------------|
| `events` | array | All successfully normalized events (canonical schema). |
| `errors` | array | Per-item errors (code, message, index, detail). Empty if none. |

**Example response:**

```json
{
  "events": [
    {
      "id": "123",
      "title": "Tech Meetup",
      "startTimeIso": "2024-06-15T18:00:00.000Z",
      "endTimeIso": "2024-06-15T20:00:00.000Z",
      "location": {
        "type": "physical",
        "venueName": "Main Hall",
        "city": "San Francisco",
        "region": null,
        "country": null,
        "url": null
      },
      "pricing": null,
      "organizer": null,
      "provenance": { "platform": "eventbrite", "sourceRef": "123", "parsingStrategy": "eventbrite" },
      "interpretation": { "method": "platform_exact", "confidence": 0.95, "warnings": null }
    }
  ],
  "errors": []
}
```

**Error codes:** `INVALID_REQUEST`, `INVALID_JSON`, `INVALID_PAYLOAD`, `INVALID_STRUCTURE`, `MAX_DEPTH_EXCEEDED`, `PAYLOAD_TOO_LARGE`, `INTERNAL_ERROR`

### GET /health

Returns `{ "status": "ok" }`.

---

## Canonical Event Schema

**Every normalized event has the same structure.** Key fields:

- **id** · **title** · **description** · **descriptionPlain**
- **eventType** · **isVirtual** · **isHybrid**
- **startTime** · **endTime** · **startTimeIso** · **endTimeIso** · **timezone** · **durationMinutes**
- **location** — type (physical | virtual | hybrid), venueName, address, city, region, country, lat, lng, url (virtual join), raw
- **pricing** — isFree, minPrice, maxPrice, currency
- **availability** — capacity, remaining, isSoldOut, waitlistAvailable
- **organizer** — name, id, url
- **images** · **categories** · **tags** · **url**
- **provenance** — platform, sourceRef, parsingStrategy
- **interpretation** — method (platform_exact | platform_fuzzy | generic_inferred), confidence (0–1), warnings

**Supported platforms (examples):** Eventbrite, Ticketmaster, Meetup, Eventful, Facebook, Google, Bandsintown, Songkick, StubHub, SeatGeek, Eventfinda, YouTube, Twitch, and 40+ more. Unknown platform names use generic normalization.

---

## Related APIs

Explore more developer tools from [Precision Solutions Tech on RapidAPI](https://rapidapi.com/user/precisionsolutionstech):

| API | Description |
|-----|-------------|
| [**Event Listing Normalization**](https://rapidapi.com/precisionsolutionstech/api/event-listing-normalization-api) | This API — unify Eventbrite, Meetup, Ticketmaster, 50+ into one schema |
| [Calendar Event Normalization](https://rapidapi.com/precisionsolutionstech/api/calendar-event-normalization) | Normalize calendar events from Google, Outlook, Apple, and more |
| [Job Posting Normalization](https://rapidapi.com/precisionsolutionstech/api/job-posting-normalization-api) | Normalize job postings from LinkedIn, Indeed, Greenhouse, 25+ platforms |
| [Shipping & Tracking Normalization](https://rapidapi.com/precisionsolutionstech/api/shipping-tracking-data-normalization) | Unify UPS, FedEx, USPS, DHL into one shipment schema |
| [API Error & Status Normalization](https://rapidapi.com/precisionsolutionstech/api/api-error-status-normalization) | Canonical error taxonomy and retry guidance |
| [Retail Data Normalization & Comparison](https://rapidapi.com/precisionsolutionstech/api/retail-data-normalization-comparison) | Normalize product data across Amazon, Walmart, eBay |
| [Sensitive Data Detection & Redaction](https://rapidapi.com/precisionsolutionstech/api/sensitive-data-detection-redaction-api) | Detect and redact PII in text |
| [HTML to Markdown Converter](https://rapidapi.com/precisionsolutionstech/api/html-to-markdown-converter1) | Convert HTML to GitHub Flavored Markdown |

**[View all APIs →](https://rapidapi.com/user/precisionsolutionstech)**

---

## FAQ

### Do I need Eventbrite, Ticketmaster, or other API keys?

No. This API only processes data you send. You obtain event data through your own integrations (platform APIs, webhooks, scrapers) and pass it to `/normalize`.

### Can I send multiple platforms in one request?

Yes. Send an **inputs** array. Each element is a **bucket** with `platform` and `data` (array of events), or `platform` and `payload` (single event or array). The API returns one **events** array in the same canonical shape.

### What if I don't know the platform?

Omit **platform** or use **generic**. The API will use best-effort generic normalization (common field names). If you know the platform, specify it for more accurate normalization and higher confidence.

### What is confidence and how do I use it?

Each normalized event has **interpretation.confidence** (0–1). Use it to filter or flag low-confidence items (e.g. for review or fallback). **platform_exact** and **platform_fuzzy** usually have high confidence; **generic_inferred** is lower.

### What happens when one item in my array is invalid?

That item is skipped and an entry is added to **errors** with **code**, **message**, and **index**. All other items are still normalized and returned in **events**.

### Is the output deterministic?

Yes. Same input produces the same output. No randomness or timestamps in the canonical structure.

### Is my data stored or logged?

No. The API is stateless. Payloads are processed in memory and not persisted.

### What's the maximum payload size?

25MB per request. Larger payloads return 413 `PAYLOAD_TOO_LARGE`.

### What platforms are supported?

Eventbrite, Ticketmaster, Meetup, Eventful, Facebook, Google, Bandsintown, Songkick, StubHub, SeatGeek, Eventfinda, YouTube, Twitch, and 40+ more have dedicated or generic support. Unknown platform names fall back to generic normalization.

---

<p align="center">
  <a href="https://rapidapi.com/precisionsolutionstech/api/event-listing-normalization-api">Try Event Listing Normalization API on RapidAPI</a> ·
  <a href="https://rapidapi.com/user/precisionsolutionstech">All APIs by Precision Solutions Tech</a>
</p>
