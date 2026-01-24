# Cross-Lingual Information Retrieval System

## Project Overview
A functional CLIR system that retrieves relevant news articles in Bangla and English from cross-lingual queries.

## Components Implemented

### 1. Dataset (Module A)
- **Bangla Articles**: 500 documents from 5 news sites
- **English Articles**: 591 documents from 5 news sites
- **Total**: 1,091 news articles
- **Metadata**: title, body, url, date, language

### 2. Query Processing (Module B)
- Language detection (Bangla/English)
- Query normalization
- Machine translation:
  - Bangla → English: OPUS-MT model
  - English → Bangla: Google Translate

### 3. Retrieval Models (Module C)
- **BM25 (Lexical)**: Keyword-based retrieval
- **Semantic (Embeddings)**: Multilingual sentence transformers
  - Model: paraphrase-multilingual-MiniLM-L12-v2
  - Similarity: Cosine similarity

## How It Works
1. User enters query in ANY language (Bangla or English)
2. System detects language and normalizes query
3. Query is translated to opposite language
4. Both BM25 and Semantic models search BOTH language corpora
5. Results ranked by relevance score

## Files Structure
```
CLIR_Project/
├── data/
│   ├── bangla_articles.json
│   ├── english_articles.json
│   ├── bangla_raw/ (individual site data)
│   └── english_raw/ (individual site data)
├── indexes/
│   ├── bangla_index.pkl
│   ├── english_index.pkl
│   ├── bangla_embeddings.npy
│   └── english_embeddings.npy
├── models/
│   ├── bn_en_tokenizer/
│   └── bn_en_model/
└── system_config.json
```

## Key Features
✅ Cross-lingual search (search in one language, get results from both)
✅ Two complementary retrieval methods (lexical + semantic)
✅ Real-world news dataset (1000+ articles)
✅ Handles both Bangla and English scripts

## Technologies Used
- **Web Scraping**: BeautifulSoup, Requests
- **Indexing**: Custom inverted index with pickle
- **NLP**: Transformers (OPUS-MT), sentence-transformers
- **Retrieval**: rank-bm25, scikit-learn
- **Translation**: HuggingFace Transformers, Google Translate

## Author
Single-member team submission for CSE 4739 Data Mining Course

## Date
January 2026
