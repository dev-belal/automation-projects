# 🧠 Movie Analyzer AI — Workflow Explanation

This document provides a **detailed technical breakdown** of how the Movie Analyzer AI n8n workflow operates — from data extraction to AI-powered review generation.

---

## ⚙️ Phase 1 — Data Collection & Cleaning

### 1. **Initiator Setup**
- Multiple **Schedule Triggers** run sequentially at 9:00 AM, 9:02 AM, etc.
- Each trigger generates pages (1–500) to query TMDb API efficiently in parallel batches.
- These triggers ensure even distribution of API load and continuous fetching of movie data.

### 2. **API Requests**
- Three main **HTTP Request nodes** call TMDb API endpoints:
  - **Fetch Popular Movies** → `/movie/popular`
  - **Fetch Upcoming Movies** → `/movie/upcoming`
  - **Fetch Popular TV Series** → `/tv/popular`
- Each request uses **Split in Batches** nodes to handle pagination safely.
- Authorization headers use a TMDb API **Bearer Token** for secure access.

### 3. **Data Cleaning**
- Data from TMDb is processed using **Code Nodes**:
  - `Clean and Format Movie Data`
  - `Clean and Format Movie Data1`
  - `Clean and Format TV Data`
- Each Code Node extracts key attributes and structures the JSON into a unified format:
  ```js
  {
    id,
    title,
    release_date,
    overview,
    rating,
    popularity,
    genre_ids,
    poster_url,
    type
  }
- The type field dynamically identifies if the item is a **Movie**, **UpcomingMovie**, or **Series**.
- Poster URLs are formatted via:
```js
https://image.tmdb.org/t/p/w500${poster_path}
