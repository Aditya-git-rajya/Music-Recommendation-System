# Music Recommendation System

**Status:** Functional proof-of-concept with identified path for production scaling  
**Priority Next Step:** Focus on data quality improvement before algorithm advancement
**License:** [MIT License](#license)

---

## 🧭 Table of Contents
1.  [Project Overview](#1-project-overview)
2.  [How It Works](#2-how-it-works)
3.  [System Requirements](#3-system-requirements)
4.  [Data Structure & Quality](#4-data-structure--quality)
5.  [Step-by-Step Code Explanation](#5-step-by-step-code-explanation)
6.  [Usage Guide](#6-usage-guide)
7.  [Critical Findings & Analysis](#7-critical-findings--analysis)
8.  [Troubleshooting](#8-troubleshooting)
9.  [Future Improvements](#9-future-improvements)
10. [License](#license)

---

## 1. Project Overview

### What This System Does
This is a **Content-Based Music Recommendation System** that analyzes song lyrics to recommend similar songs. Give it a song title, and it returns 20 songs with the most similar lyrical content.

| Feature | Description |
| :--- | :--- |
| **Type** | Content-Based Filtering using lyrics |
| **Technology** | PySpark + scikit-learn + NLTK |
| **Input** | Song title from dataset |
| **Output** | 20 most lyrically similar songs |
| **Environment** | Google Colab (recommended) |
  
### Why This Approach Works
Songs with similar themes, emotions, or storytelling styles often use similar vocabulary and language patterns. By analyzing these patterns mathematically (via TF-IDF), we can find songs that "feel" similar even if they're by different artists or from different eras.

---

## 2. How It Works

### The 4-Step Process
`Raw Lyrics` → `Text Processing` → `Numerical Vectors` → `Similarity Scores` → `Recommendations`

| Step | What Happens | Why It's Needed |
| :--- | :--- | :--- |
| **1. Text Cleaning** | Remove punctuation, convert to lowercase | Standardizes text for analysis |
| **2. Tokenization** | Break lyrics into individual words, remove common words | Focuses on meaningful content words |
| **3. TF-IDF Vectorization** | Convert words to numerical importance scores | Computers need numbers, not text |
| **4. Similarity Calculation** | Compare all songs mathematically | Find the most similar lyrical patterns |
  
### Key Technologies
* **PySpark:** Handles large datasets efficiently through distributed processing.
* **NLTK:** Provides natural language processing tools (tokenization, stop words).
* **Scikit-learn:** Implements TF-IDF vectorization and cosine similarity.
* **TF-IDF:** Measures how important each word is to each song.
* **Cosine Similarity:** Calculates how similar two songs are (0 = different, 1 = identical).

---

## 3. System Requirements

### Google Colab (Recommended)
| Pros | Cons |
| :--- | :--- |
| No setup required, free access, pre-installed Python | 12-hour session limit, requires internet connection, session resets lose all variables |
| **Memory:** 12.7GB RAM (sufficient for 5,000-10,000 songs) | **Important:** You'll need to authorize Google Drive access when prompted |

### Local Machine Requirements
* **RAM:** 8GB minimum, 16GB recommended
* **Java:** OpenJDK 8 (required for PySpark)
* **Python:** 3.8+ with `pip`
* **Storage:** 10GB free space

### Hardware Reality Check
| System Type | RAM | Dataset Capacity | Processing Time |
| :--- | :--- | :--- | :--- |
| **Basic Laptop** | 8GB | 1,000-2,000 songs | 10-15 minutes |
| **Good Workstation** | 16GB | 5,000-10,000 songs | 5-10 minutes |
| **High-End System** | 32GB+ | 20,000+ songs | 2-5 minutes |
*Note: The full dataset (57,000+ songs) requires professional-grade hardware or cloud computing.*

---

## 4. Data Structure & Quality

### Dataset Information
* **Full Dataset Size:** ~69MB, approximately 57,651 songs
* **Current Working Sample:** 5,000 songs (for development stability)
* **Source File:** `songdata.csv`

### Column Strategy
| Column | Status | Purpose | Why This Decision |
| :--- | :--- | :--- | :--- |
| **artist** | Kept | Song identification | Needed for displaying recommendations |
| **song** | Kept | Primary identifier | Core lookup key for the system |
| **link** | Dropped | URL reference | Not relevant to lyrical analysis |
| **text** | Kept | Lyrics content | The main data for recommendations |

### Critical Data Quality Finding
**The 97% Noise Problem:** When processing 5,000 sample rows, only 135 songs passed quality filters, resulting in a **2.7% survival rate**. This revealed that the dataset contains significant amounts of corrupted, incomplete, or malformed data.

---

## 5. Step-by-Step Code Explanation

| Cell # | Purpose | Key Actions | Critical Finding / Insight |
| :--- | :--- | :--- | :--- |
| **Cell 1** | **Environment Setup** | Installs PySpark, Java 8, NLTK, and creates Spark session. | PySpark requires a complex environment build in Colab. |
| **Cell 2** | **Data Loading** | Mounts Google Drive, reads `CSV` into Spark DataFrame, limits to 5,000 rows. | Data limiting prevents memory crashes on standard hardware. |
| **Cell 3** | **NLP Function Definition** | Defines a UDF for tokenization, stop-word removal, and stemming. | Centralizes all text pre-processing into one scalable function. |
| **Cell 4** | **Text Cleaning** | Removes punctuation and standardizes text to lowercase. | Prepares text for the advanced NLP pipeline. |
| **Cell 5** | **Advanced Text Processing** | Applies the NLP function (Cell 3) to all lyrics using Spark UDF. | Efficiently processes text at scale using distributed computing. |
| **Cell 6** | **Data Quality Control** | Filters out null titles, null lyrics, and short titles. | **CRITICAL DISCOVERY:** Only 135 clean songs remain (2.7% survival). |
| **Cell 7** | **Feature Engineering** | Converts cleaned words to numbers using TF-IDF. | **RED FLAG:** Only 3 unique features were generated, confirming data quality as the bottleneck. |
| **Cell 8** | **Similarity Calculation** | Calculates the Cosine Similarity between all song vectors. | Generates a 135x135 similarity matrix. |
| **Cell 9** | **Recommendation Engine** | Looks up the target song, sorts by similarity, and returns the top 20. | The final utility for the user. |

---

## 6. Usage Guide

### Quick Start
1.  Open Google Colab with the `.ipynb` file.
2.  Upload your `songdata.csv` to Google Drive.
3.  Run cells 1-9 in order.
4.  Modify the song name in Cell 9 to test recommendations.

### Getting Recommendations
In **Cell 9**, change the `TARGET_SONG` variable:

```python
# Change "Chiquitita" to any song title available in the clean dataset
TARGET_SONG = "Chiquitita" 

recommendations = recommendation(TARGET_SONG)
