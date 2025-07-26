# **Challenge 1A – PDF Outline Extractor**

## Overview

This module is part of the Adobe Hackathon 2025 submission and is built to **intelligently extract structured document outlines (headings and subheadings)** from PDF files. It is optimized for diverse formats, languages, and content styles — whether you're analyzing a resume, a research paper, or a technical report.

By combining **visual layout analysis** with **textual keyword recognition**, it identifies logical structures within unstructured or semi-structured documents and outputs them in a clean, hierarchical JSON format.

---

## What the Model Does

* **Detects heading levels (H1, H2, H3)** across multiple pages.
* Applies a **hybrid scoring algorithm** that considers both font-based layout features and multilingual keyword cues.
* Builds a **hierarchical document outline** grouped by heading level.
* Automatically **infers the document title** using metadata or visual prominence.
* Produces a **standardized JSON output** suitable for downstream processing or display.

---

## Why This Is Better Than Baselines

| Feature                  | Traditional Extractors        | Our Solution                                    |
| ------------------------ | ----------------------------- | ----------------------------------------------- |
| Multilingual Recognition | Often unsupported             | English, Hindi, Japanese, French, Spanish       |
| Layout Intelligence      | Only font-size based          | Font size, bold, caps, alignment, keywords      |
| Output Structure         | Flat or loosely grouped       | Nested hierarchy with H1 → H2 → H3              |
| Offline Usability        | Often dependent on cloud APIs | Fully containerized, lightweight, offline       |
| Fallback Handling        | Fails on unstructured PDFs    | Smart defaults and safe fallback logic included |

---

## Evaluation Criteria Alignment

| Evaluation Area           | Addressed Through                                                               |
| ------------------------- | ------------------------------------------------------------------------------- |
| **Completeness**          | Captures title and all structured sections using a hybrid scoring method        |
| **Correctness**           | Uses consistent scoring logic based on layout and keyword cues                  |
| **Section Relevance**     | Filters and prioritizes true headings using multilingual keyword detection      |
| **Sub-section Relevance** | Groups sections into clean H1, H2, H3 nesting based on layout and semantic cues |

---

## How It Works

### Step 1: Text Extraction

Uses `PyMuPDF` to extract:

* Font size
* Bold, underlined, and capitalized text
* Position and alignment on the page

### Step 2: Heading Scoring

A scoring function evaluates each line based on:

* Font size compared to average body text
* Boldness, central alignment, and underlining
* All-uppercase formatting
* Common heading patterns (e.g., numbered lists)
* Keyword matches from a multilingual dictionary

### Step 3: Hierarchical Grouping

* Classifies top headings as **H1**, mid-level as **H2**, and detailed subheadings as **H3**
* Groups them into a structured nested list based on relative position and size
* Applies fallback rules if the document lacks strong visual hierarchy

### Step 4: Output Generation

* Saves a `.json` file per input PDF
* Each section includes:

  * Text content
  * Heading level (H1, H2, H3)
  * Font formatting and page number

---

## Folder Structure

```
challenge1a/
├── Dockerfile              # Containerized environment setup
├── requirements.txt        # Python dependencies
├── process.py              # Main extraction logic
├── input/                  # Place your PDF files here
│   └── document.pdf
├── output/                 # Output folder for structured JSON
│   └── document.json
```

---

## Quick Start

### Step 1: Build Docker Image

```bash
docker build -t pdf-outline-extractor .
```

### Step 2: Add PDF Files

Place one or more PDF files in the `input/` folder.

### Step 3: Run the Tool

```bash
docker run --rm \
  -v "$(pwd)/input:/app/input" \
  -v "$(pwd)/output:/app/output" \
  pdf-outline-extractor
```

### Step 4: View Results

Output files are stored in the `output/` folder in JSON format.

---

## Example Output

```json
{
  "title": "Deep Learning Advances",
  "outline": [
    {
      "text": "1. Introduction",
      "page": 1,
      "level": "H1",
      "bold": true,
      "centered": true,
      "size": 20,
      "children": [
        {
          "text": "Background",
          "page": 2,
          "level": "H2",
          "bold": false,
          "centered": false,
          "size": 16
        }
      ]
    }
  ]
}
```

---

## Requirements

If you wish to run it locally (without Docker):

```bash
pip install -r requirements.txt
```

### Key Libraries Used

* `PyMuPDF (fitz)` – Text and layout extraction from PDF
* `re`, `unicodedata`, `json` – Text normalization and data handling
* `collections` – Efficient hierarchical data structure building

---

## Performance

| Metric              | Performance                               |
| ------------------- | ----------------------------------------- |
| Average Time / PDF  | 3–5 seconds                               |
| Memory Usage        | Less than 100 MB                          |
| Output Size         | Minimal JSON (\~20–100 KB per document)   |
| Supported Languages | English, Hindi, Spanish, French, Japanese |

---

## Summary

This module serves as a fast, accurate, and multilingual-ready PDF outline extractor. By balancing layout detection with language support, it ensures robust performance across document types. The resulting JSON output enables easy downstream use in search, summarization, or document navigation tools.

Whether you're analyzing research papers or resumes, this extractor offers a practical, efficient solution that works seamlessly in local environments using Docker.
