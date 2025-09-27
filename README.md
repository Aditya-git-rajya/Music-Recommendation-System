# Music Recommendation System

**Status:** Functional proof-of-concept with identified path for production scaling  
**Priority Next Step:** Focus on data quality improvement before algorithm advancement
**Documentation Version:** 1.0 | Last Updated: September 2025
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
10. [Real-World Applications & Learning Outcomes](#10-real-world-applications--learning-outcomes)
11. [License](#license)

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
Songs with similar themes, emotions, or storytelling styles often use similar vocabulary and language patterns. By analyzing these patterns mathematically, we can find songs that "feel" similar even if they're by different artists or from different eras.

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
| Component | Details | Note |
| :--- | :--- | :--- |
| **Pros** | No setup, free access, pre-installed Python | Recommended starting environment. |
| **Cons** | 12-hour session limit, resets all variables | Requires internet connection. |
| **Memory** | 12.7GB RAM (sufficient for 5k-10k songs) | You'll need to authorize Google Drive access when prompted. |

### Local Machine Requirements
* **RAM:** 8GB minimum, **16GB recommended**
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
**The 97% Noise Problem:** When processing 5,000 sample rows, only **135 songs** passed quality filters. This resulted in a **2.7% survival rate**. The dataset contains significant corrupted or incomplete data, a common finding in real-world sources.

---

## 5. Step-by-Step Code Explanation

### Cell 1: Environment Setup 🛠️
* **Purpose:** Install and configure PySpark environment.
* **What it does:** Installs PySpark, Java 8, and required libraries; sets up environment variables for Spark; downloads NLTK language data; creates Spark session.
* **Why it's complex:** Google Colab doesn't come with PySpark pre-installed, so we must build the entire distributed computing environment from scratch.

### Cell 2: Data Loading
* **Purpose:** Load CSV data and perform initial inspection.
* **Key Steps:** Mount Google Drive (requires user permission); read CSV into Spark DataFrame; limit to 5,000 rows for memory management; display schema and sample data.
* **Important:** The data limiting prevents memory crashes on standard hardware.

### Cell 3: NLP Function Definition
* **Purpose:** Create the core text processing function.
* **What the function does:** Convert text to lowercase; split into individual words (tokenization); remove common stop words; reduce words to root forms (stemming).

### Cell 4 & 5: Text Cleaning and Advanced Processing
* **Cell 4 (Cleaning):** Basic standardization (remove punctuation, lowercase).
* **Cell 5 (Processing):** Apply the NLP function to all lyrics; create arrays of cleaned words; remove unnecessary columns to save memory.

### Cell 6: Data Quality Control (THE CRITICAL DISCOVERY) 🚨
* **Purpose:** Filter out corrupted or incomplete data.
* **The 97% Data Loss:** The filters (null title, null lyrics, short titles) reduced the sample from 5,000 to 135 clean songs.
* **Strategic Implication:** This finding highlights that the project's priority must be **data engineering**, a more realistic and valuable approach for production systems.

### Cell 7: Feature Engineering (THE TF-IDF WARNING SIGNAL) ⚠️
* **Purpose:** Convert text to numbers using TF-IDF.
* **The "3 Features" Red Flag:**
    * **Expected:** 500-5000 unique words (features)
    * **Actual:** Only **3 unique words**
* **Diagnostic Insight:** This confirms **vocabulary poverty** is the primary bottleneck. Advanced ML algorithms would be wasted on such limited data.

### Cell 8: Similarity Calculation
* **Purpose:** Calculate how similar each song is to every other song.
* **Process:** Uses **cosine similarity** to measure the angle between song vectors.
* **Result:** A **$135 \times 135$ similarity matrix**.

### Cell 9: Recommendation Engine
* **Purpose:** Generate recommendations for a given song.
* **How it works:** Find the target song; look up its similarity scores; sort by score; return the top 20 most similar songs.

---

## 6. Usage Guide

### Quick Start
1.  Open **Google Colab** with the notebook.
2.  Upload your `songdata.csv` to Google Drive.
3.  Run cells 1-9 in order.
4.  Modify the song name in Cell 9 to test recommendations.

### Getting Recommendations
In **Cell 9**, change this line:

```python
recommendations = recommendation("Your Song Title Here")

# Example:
# recommendations = recommendation("Yesterday")
# recommendations = recommendation("Chiquitita")
