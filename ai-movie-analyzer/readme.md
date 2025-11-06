# 🎬 Movie Analyzer AI (n8n Workflow)

**Movie Analyzer AI** is an automated workflow built in **n8n** that integrates with **The Movie Database (TMDb) API**, **Google Sheets**, and **OpenAI GPT** to collect, clean, and analyze movie and TV show data.  
It automatically updates a centralized Google Sheets database with new entries, checks for duplicates, and generates AI-written reviews for each title.

---

## 🧩 Key Features

- **Automatic Data Fetching**
  - Retrieves movie and TV show data (Popular, Upcoming) from TMDb API.
  - Supports pagination across 500+ pages.

- **Smart Data Cleaning**
  - Formats and standardizes results from TMDb API.
  - Extracts key fields: ID, Title, Release Date, Overview, Rating, Popularity, Genre, and Poster URL.

- **Duplicate Checking**
  - Compares fetched data with existing Google Sheets entries.
  - Only inserts or updates changed/new items.

- **AI Review Generation**
  - Uses GPT (via n8n OpenAI node) to write short, engaging, spoiler-free reviews.
  - Updates Google Sheets with generated reviews automatically.

- **Automated Scheduling**
  - Runs at specific intervals (daily or hourly) using n8n’s **Schedule Trigger**.
  - Ensures that databases remain fresh with minimal manual effort.

---

## 🧠 Workflow Structure

| Phase | Description |
|-------|--------------|
| **Initiator Setup** | Multiple scheduled triggers for different data ranges. |
| **Phase 1** | Fetches and cleans movie/TV data from TMDb API, and stores it in either a single or multiple Google Sheets. |
| **Phase 2** | Reads unreviewed entries, generates GPT reviews, and updates Google Sheets with “Reviewed” and “Review” columns. |

---

## 🗂️ Integrations

| Integration | Purpose |
|--------------|----------|
| **TMDb API** | Fetches latest Movies & TV Series metadata. |
| **Google Sheets** | Stores movie datasets and review data. |
| **OpenAI GPT (LangChain Node)** | Generates human-like reviews automatically. |

---

## 📊 Data Fields

| Column | Description |
|--------|--------------|
| `id` | Unique Movie or Series ID from TMDb |
| `Title` | Movie or TV Show name |
| `Overview` | Summary from TMDb |
| `Type` | Movie / UpcomingMovie / Series |
| `Release Date` | Release or first-air date |
| `Rating` | Average viewer score |
| `Popularity` | TMDb popularity metric |
| `Poster URL` | Image link for poster |
| `Reviwed?` | “Yes/No” field for review status |
| `Review` | AI-generated text |

---

## 🧱 Tech Stack

- **Workflow Engine:** n8n
- **Database:** Google Sheets
- **API Source:** The Movie Database (TMDb)
- **AI Engine:** OpenAI GPT-4.1-mini
- **Language:** JavaScript (for logic in n8n Code Nodes)

---

## 🖼️ Workflow Overview

Below is a visual layout of the entire n8n workflow:

![Workflow Diagram](./docs/)

---

## ⚙️ Setup Instructions

1. Import the provided JSON file into **n8n**:
   ```bash
   Movie Analyzer AI.json

