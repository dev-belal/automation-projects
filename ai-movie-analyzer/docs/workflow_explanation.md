# 🧠 Movie Analyzer AI — Workflow Explanation

This document provides a **detailed technical breakdown** of how the Movie Analyzer AI n8n workflow operates — from data extraction to AI-powered review generation.

---

## ⚙️ Phase 1 — Data Collection & Cleaning

### 1. **Initiator Setup**
- The workflow begins with multiple **Schedule Triggers**:
  - **Phase 1 - Initiator 1.0** → 9:00 AM  
  - **Phase 1 - Initiator 2.0** → 9:02 AM  
  - **Phase 1 - Initiator 3.0** → 9:04 AM  
  - **Phase 1 - Initiator 4.0** → 9:06 AM  
  - **Phase 1 - Initiator 5.0** → 9:08 AM  
- Each trigger calls a **page generator Code Node** to create batched pagination ranges for TMDb queries (1–500), ensuring parallel fetching of movie and TV data:
  - `pagesForPopularMovies (1–100, 101–200, 201–300, 301–400, 401–500)`
  - `pagesForPopularTVShow (1–100, 101–200, 201–300, 301–400, 401–500)`
  - `pagesForUpcomingMovies (1–65)`

These distributed triggers balance API load, allow high-volume crawling, and maintain continuity during long runs.

---

### 2. **API Requests**
The data fetching system includes three core **HTTP Request nodes**, each pointing to a TMDb endpoint:
- **Fetch Popular Movies** → `/movie/popular`
- **Fetch Upcoming Movies** → `/movie/upcoming`
- **Fetch Popular TV Series** → `/tv/popular`

Each request:
- Runs with **Split in Batches** for pagination.
- Includes a TMDb API **Bearer token** for authorization.
- Uses query parameters such as `language`, `page`, and `region` for localized accuracy.

---

### 3. **Data Cleaning**
After collection, raw TMDb JSON responses are passed through **Code Nodes** to clean and normalize data:
- `Clean and Format Movie Data`
- `Clean and Format Movie Data1` (for upcoming movies)
- `Clean and Format TV Data` (for TV series)

Each Code Node extracts and structures key fields:
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

```
The **type** field differentiates:
- "Movie" → for popular movie data
- "UpcomingMovie" → for unreleased titles
- "Series" → for popular TV shows
Poster images are formatted via:
```js
https://image.tmdb.org/t/p/w500${poster_path}
```
This guarantees clean, consistent datasets ready for insertion or comparison.

---

### 4. Duplicate Check
Each cleaned dataset passes through a **Check for Duplicates** logic layer:
- **Check for Duplicates3** → Movies
- **Check for Duplicates2** → Upcoming Movies
- **Check for Duplicates1** → TV Series 

Each node:
- Compares the cleaned TMDb items with the current Google Sheets datasets:
    - **Popular Movies, Upcoming Movies, and Popular TV Series**
- Extracts all existing id values into a Set() for O(1) lookups.
- Returns:
    - **"operation": "insert"** if new
    - **"operation": "update"** if already exists
This ensures incremental synchronization — no duplicate rows, and seamless update handling.

---

### 5. Type-Based Routing
After deduplication, the workflow dynamically routes each record via **IF nodes**:
- IF (Type) → for **Popular Movies**
- IF (Type)1 → for **Upcoming Movies**
- IF (Type)2 → for **TV Series**
This separation ensures that each dataset is written to its respective sheet, maintaining a clean multi-database architecture.

---

### 6. Data Storage
Finally, cleaned and deduplicated data is written to the **Movie Analyzer AI (Database 2.0)** Google Sheets system:
- Popular Movies Sheet (gid=0)
- Upcoming Movies Sheet (gid=1660865768)
- Popular TV Series Sheet (gid=1779211863)
  
Each sheet receives:
- id
- Title
- Release Date
- Overview
- Type
- Rating
- Popularity
- Poster URL
- plus placeholders for:
    - Providers
    - Reviewed?
    - Customer Review
    - Article
All write operations use **Append Mode** for inserting new records, with duplicate checks handled upstream.

---

### 🧾 Notes
- The new design separates Movies, Upcoming Movies, and Series for modular scaling.
- Multiple schedule triggers improve reliability and distribute the load over time.

---

Author: Syed Bilal (@dev-belal)
Last Updated: November 2025
