# Amazon Prime Video — Relational Database Schema & System Design

A comprehensive relational database design and product case study for an Amazon Prime Video-style streaming platform. This repository outlines the architecture required to manage users, multi-profile account structures, content catalogs, watch progress, watchlists, and user reviews with strict referential integrity.

---

## 📌 Product Case Study & Problem Statement

Modern streaming platforms must solve complex user experience challenges while serving millions of concurrent global subscribers. This schema design directly addresses key industry challenges:

* **Fragmented Cross-Device Viewing:** Solved via account synchronization supporting user profiles and watch progress tracking across devices.
* **Content Discovery & Overload:** Managed by capturing granular metadata (genres, ratings, viewing history) to enable AI/ML recommendations.
* **Low-Connectivity & Flexible Playback:** Supports tracking stream progress and offline state metadata.
* **Account Personalization & Parental Controls:** Uses a 1-to-Many User-to-Profile structure to isolate history and enforce age restrictions per profile.

---

---

## 🗄️ Schema Architecture & Entities

The database consists of 7 normalized core entities:

### 1. `Users` (Account Layer)
Manages primary billing account credentials and subscription status.
* `UserID` (PK, Serial) — Unique account identifier.
* `Name` (VARCHAR) — Full account holder name.
* `Email` (VARCHAR, NOT NULL) — Login identifier.
* `Password` (VARCHAR, NOT NULL) — Encrypted credentials.
* `Registration_Date` (DATE) — Signup timestamp.
* `Subscription_Type` (VARCHAR) — Plan type (e.g., Monthly, Yearly, Trial).

### 2. `Profiles` (User Identity Layer)
Allows multiple viewer profiles under a single account subscription.
* `ProfileID` (PK, Serial) — Unique profile identifier.
* `UserID` (FK, Int) — References parent user account.
* `Profile_Name` (VARCHAR) — Profile display name.
* `Age_Restriction` (BOOLEAN) — Content restriction flag for parental controls.

### 3. `Content` (Media Catalog)
Stores metadata for all streaming titles (Movies, TV Series, Documentaries).
* `ContentID` (PK, Serial) — Unique media item identifier.
* `Title` (VARCHAR) — Title of the show or movie.
* `Type` (VARCHAR) — Format type (Movie, Series, Documentary).
* `Genre` (VARCHAR) — Primary genre classification.
* `Release_Year` (INT) — Year of initial release.
* `Language` (VARCHAR) — Primary audio track language.
* `Duration` (INT) — Total runtime in minutes.
* `Rating` (DECIMAL) — Aggregate IMDb or platform score.

### 4. `Episodes` (Series Hierarchy)
Extends `Content` to manage seasons and episode ordering for TV series.
* `EpisodeID` (PK, Serial) — Unique episode identifier.
* `ContentID` (FK, Int) — Links to parent series in `Content`.
* `Season_Number` (INT) — Season number.
* `Episode_Number` (INT) — Sequence number within season.
* `Title` (VARCHAR) — Individual episode title.
* `Duration` (INT) — Episode length in minutes.

### 5. `WatchHistory` (Activity Tracking)
Tracks user viewing progress for playback resumption and algorithm training.
* `HistoryID` (PK, Serial) — Unique history entry.
* `ProfileID` (FK, Int) — References viewer profile.
* `ContentID` (FK, Int) — References watched media.
* `Watch_Date` (DATE) — Date of last interaction.
* `Progress` (INT) — Playback timestamp in seconds/minutes.

### 6. `Watchlist` (Saved Items)
Enables profiles to bookmark media for future viewing.
* `WatchlistID` (PK, Serial) — Unique bookmark identifier.
* `ProfileID` (FK, Int) — References saved profile.
* `ContentID` (FK, Int) — References saved media.
* `Added_Date` (DATE) — Date item was added.

### 7. `Reviews` (User Feedback)
Captures star ratings and textual reviews from users.
* `ReviewID` (PK, Serial) — Unique review identifier.
* `ContentID` (FK, Int) — References reviewed item.
* `ProfileID` (FK, Int) — References reviewer profile.
* `Rating` (INT) — User score (1 to 5 stars).
* `Comment` (TEXT) — Written evaluation.
* `Review_Date` (DATE) — Submission date.

---

## 🔗 Key Entity Relationships (Cardinality)

* **`Users` 1 : N `Profiles`** — One user subscription hosts multiple viewer profiles.
* **`Profiles` 1 : N `WatchHistory`** — One profile logs multiple viewing interactions over time.
* **`Profiles` 1 : N `Watchlist`** — One profile maintains a custom list of bookmarked titles.
* **`Content` 1 : N `Episodes`** — One TV show content entry contains multiple individual episodes.
* **`Profiles` 1 : N `Reviews` N : 1 `Content`** — Many-to-Many junction entity joining profile feedback to catalog content.

---

## 📄 Full Project Documentation

For the complete theoretical breakdown and business requirement document, refer to the included file:
* 📄 [Amazon Prime Database Design Document (PDF)](./Amazon_prime_database_design.doc.pdf)
