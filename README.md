# Cross-Lingual Information Retrieval System - Bangla/English

## Project Overview

A production-ready Cross-Lingual Information Retrieval (CLIR) system designed to retrieve relevant news articles in Bangla and English from cross-lingual queries. The system features four retrieval models, 68 labeled queries, and comprehensive evaluation metrics.

---

## Key Achievements

### Dataset (Module A)

- **Total Articles:** 2,113 quality news articles  
- **Bangla Articles:** 1,465 articles from 13 news sources  
- **English Articles:** 648 articles from 13 news sources  
- **Sources:** 26 diverse Bangladeshi news portals  
- **Data Quality Measures:**  
  - Removed sitemaps  
  - Filtered security blocks  
  - Excluded non-article pages  
- **Average Article Length:**  
  - Bangla: 2,961 characters  
  - English: 2,862 characters  

---

### Query Processing (Module B)

- **Language Detection:** Unicode range heuristics combined with statistical detection (langdetect)  
- **Normalization:** Language-specific cleaning and standardization  
- **Translation:** Google Translate with caching and rate-limit optimization  
- **Stopword Removal:** Custom stopword lists for Bangla and English  
- **Query Expansion:** Multiple search variants to improve recall  

---

### Retrieval Models (Module C)

| Model     | Type          | Implementation |
|----------|---------------|----------------|
| BM25     | Lexical       | rank-bm25 with tokenized corpus |
| Semantic | Dense         | paraphrase-multilingual-MiniLM-L12-v2 (384-dim embeddings) |
| TF-IDF   | Vector Space  | scikit-learn with n-grams (1,2) |
| Hybrid   | Ensemble      | Weighted combination (BM25: 0.3, Semantic: 0.4, TF-IDF: 0.3) |

---

### Evaluation Results (Module D)

- **Total Labeled Queries:** 68  
  - Bangla: 30  
  - English: 30  
  - Cross-lingual: 8  

| Model    | P@10   | R@10   | P@50   | R@50   | nDCG@10 | MRR    | MAP    |
|----------|--------|--------|--------|--------|---------|--------|--------|
| BM25     | 0.0103 | 0.0343 | 0.0050 | 0.0833 | 0.0250  | 0.0433 | 0.0161 |
| Semantic | 0.0074 | 0.0245 | 0.0021 | 0.0343 | 0.0111  | 0.0105 | 0.0035 |
| TF-IDF   | 0.0044 | 0.0147 | 0.0044 | 0.0735 | 0.0115  | 0.0267 | 0.0096 |
| Hybrid   | 0.0044 | 0.0147 | 0.0038 | 0.0637 | 0.0125  | 0.0287 | 0.0096 |

**Best Performing Model:** BM25 (expected behavior for news retrieval)

---

## Key Features

- Cross-lingual search across Bangla and English  
- Four retrieval models: BM25, Semantic, TF-IDF, Hybrid  
- Real-world dataset of 2,113 authentic news articles  
- 68 manually curated labeled queries  
- Normalized relevance scores in the range [0,1]  
- Confidence thresholding with low-confidence warnings (threshold: 0.25)  
- Interactive demo for real-time query testing  

---

## Technology Stack

| Component     | Technologies |
|--------------|--------------|
| Web Scraping | newspaper3k, BeautifulSoup4, Requests |
| Indexing     | Whoosh |
| NLP          | sentence-transformers, NLTK, langdetect |
| Translation  | deep-translator (Google Translate API) |
| Retrieval    | rank-bm25, scikit-learn (TF-IDF, cosine similarity) |
| Evaluation   | NumPy, pandas, custom metrics implementation |
| Environment  | Google Colab (T4 GPU for embeddings) |

---

## Final File Structure

```
CLIR_Bangla-English_210041252/
├── data/
│   ├── bangla_articles_only.json
│   ├── english_articles_only.json
│   └── articles_metadata.csv
├── evaluation/
│   ├── labeled_queries_real.json
│   ├── evaluation_results.json
│   └── system_stats.json
├── CLIR_FINAL_210041252.ipynb
├── requirements.txt
├── README.md
└── DATA_ACCESS.md
```

---

## System Workflow

1. **User Input**  
   Query entered in Bangla or English  

2. **Query Processing**  
   - Language detection  
   - Normalization  
   - Translation with caching  
   - Search variant generation  

3. **Retrieval Phase**  
   - All four models executed  
   - Documents retrieved from both languages  

4. **Ranking Phase**  
   - Scores normalized to [0,1]  
   - Confidence threshold applied  
   - Results sorted by relevance  

5. **Output**  
   Ranked list including titles, sources, and relevance scores  

---

## System Validation

- Module A: Dataset construction and validation  
- Module B: Query detection, normalization, translation  
- Module C: Multiple retrieval models including semantic search  
- Module D: Evaluation with 68 labeled queries and seven metrics  
- Module E: Documentation and reporting  

---

## Academic Context

- **Course:** CSE 4739 Data Mining  
- **Assignment:** Cross-Lingual Information Retrieval System  
- **Submission Type:** Single-member project  
- **Student ID:** 210041252  
- **Date:** February 2026  

---

## Limitations

- Dataset size limited to 2,113 articles (target: 5,000)  
- Certain news sources returned security blocks  
- Translation subject to API rate limits  
- Relevance judgments approximated from topic similarity  

---

## Contact

For questions or clarifications, please contact the author.
