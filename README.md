# TechShorts AI – Automated Tech News App

## Overview

TechShorts AI is a mobile-first application that delivers short, high-quality tech news summaries generated entirely by backend AI agents. The system automatically discovers, processes, and publishes content without manual intervention.

---

## Architecture Summary

```
Sources → Agents Pipeline → Queue → Database → API → Mobile App
```

### Core Flow

1. Fetch tech news from internet sources
2. Process through AI agents
3. Store structured content
4. Serve via API
5. Display in mobile app

---

## Tech Stack

### Backend

* Python (FastAPI)
* Celery (background jobs)
* Redis (queue)

### Database

* PostgreSQL (Neon)

### Storage

* Cloudflare R2 (images)
* Cloudflare CDN (delivery)

### Mobile App

* React Native (Expo)

### Hosting

* Render (API + workers)
* Upstash (Redis)

---

## System Components

### 1. Source Layer

Fetches data from:

* RSS feeds
* Social media (optional)
* Public APIs

Runs on a scheduler (every 5–10 minutes)

---

### 2. Agent Pipeline

Each agent is independent and communicates via Redis queue.

#### Source Agent

* Fetches raw articles
* Pushes to queue

#### Deduplication Agent

* Removes duplicate news
* Uses title similarity / hashing

#### Summarization Agent

* Converts content into 40–60 words
* Uses LLM

#### Hook Generator Agent

* Creates 1-line scroll-stopping title

#### Image Agent

* Fetches image from source OR
* Generates if unavailable

#### Ranking Agent

* Scores based on:

  * Recency
  * Importance
  * Topic relevance

#### Publisher Agent

* Stores final content in DB
* Updates feed cache

---

### 3. Queue System

* Redis (Upstash)
* Handles:

  * Async processing
  * Retry logic
  * Decoupling services

---

### 4. Database Schema (Simplified)

#### posts table

```
id (UUID)
title (text)
summary (text)
image_url (text)
source_url (text)
category (text)
score (float)
created_at (timestamp)
```

Indexes:

* created_at (for feed sorting)
* score (for ranking)

---

### 5. API Layer (FastAPI)

#### Endpoints

**GET /feed**

* Returns paginated list of posts

Response:

```
{
  "posts": [
    {
      "id": "123",
      "title": "...",
      "summary": "...",
      "image_url": "...",
      "created_at": "..."
    }
  ]
}
```

---

**GET /post/{id}**

* Returns single post details

---

### 6. Feed Cache

* Stored in Redis (Upstash)
* Purpose:

  * Faster reads
  * Reduce DB load

---

### 7. Mobile App

* Built with React Native (Expo)

#### Features:

* Vertical scrolling feed
* Image + title + summary
* API-driven content

---

## Hosting Setup

### Backend

* Platform: Render
* Services:

  * Web service (FastAPI)
  * Worker service (Celery)

---

### Database

* Neon (managed PostgreSQL)

---

### Queue

* Upstash Redis

---

### Storage

* Cloudflare R2 (images)
* Cloudflare CDN

---

## Deployment Flow

1. Push code to GitHub
2. Connect repo to Render
3. Deploy:

   * API service
   * Worker service
4. Configure environment variables
5. Connect to Neon + Upstash
6. Start scheduler

---

## Environment Variables

```
DATABASE_URL=
REDIS_URL=
OPENAI_API_KEY=
CLOUDFLARE_R2_KEY=
CLOUDFLARE_R2_SECRET=
```

---

## MVP Plan (7–10 Days)

### Build First:

* Source Agent (RSS only)
* Summarization Agent
* PostgreSQL storage
* FastAPI /feed endpoint
* Basic React Native app

### Skip Initially:

* Image generation
* Advanced ranking
* Personalization
* Social features

---

## Cost Estimate (Monthly)

* Render: ₹1,500–₹3,000
* Upstash Redis: Free–₹500
* Neon DB: Free
* Cloudflare: ₹0–₹500

Total: ~₹2K–₹4K

---

## Scaling Plan

### Early Stage (0–10K users)

* Single backend instance
* 1 worker

### Growth Stage

* Add more workers
* Enable caching heavily
* Upgrade DB tier

### Scale Stage (100K+)

* Load balancing
* Read replicas
* Advanced ranking

---

## Key Risks

1. Low-quality summaries
2. Duplicate content
3. High API cost (LLM usage)
4. Poor user retention

---

## Next Steps

* Implement backend skeleton
* Integrate RSS feeds
* Build summarization pipeline
* Launch MVP quickly

---

## Final Note

This system is designed for:

* Fast execution
* Low cost
* Easy scaling

Do not overengineer before validating user engagement.
