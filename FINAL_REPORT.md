
# CROSS-LINGUAL INFORMATION RETRIEVAL SYSTEM
## CSE 4739 Data Mining - Assignment Report
**Submission Date**: January 25, 2026
**Student**: Single-Member Team

---

## 1. EXECUTIVE SUMMARY

This project implements a functional Cross-Lingual Information Retrieval (CLIR) system for Bangla and English languages. The system enables users to search in one language and retrieve relevant news articles from both languages using multiple retrieval techniques.

**Key Achievements**:
- ✅ Crawled 1,091 news articles (500 Bangla + 591 English)
- ✅ Implemented 2 retrieval models (BM25 + Semantic Embeddings)
- ✅ Built complete cross-lingual query processing pipeline
- ✅ Achieved functional translation and retrieval across languages

---

## 2. DATASET CONSTRUCTION (Module A)

### 2.1 Data Collection
**Methodology**: Web scraping using Python's `requests` and `BeautifulSoup` libraries

**Bangla Sources** (500 articles):
- Prothom Alo (prothomalo.com)
- BD News 24 (bangla.bdnews24.com)
- Kaler Kantho (kalerkantho.com)
- Bangla Tribune (banglatribune.com)
- Dhaka Post (dhakapost.com)

**English Sources** (591 articles):
- The Daily Star (thedailystar.net)
- New Age (newagebd.net)
- The New Nation (dailynewnation.com)
- Daily Sun (daily-sun.com)
- Dhaka Tribune (dhakatribune.com)

### 2.2 Metadata Stored
For each document:
- `title`: Article headline
- `body`: Full article text (up to 5000 characters)
- `url`: Source URL
- `date`: Publication date
- `language`: 'bn' or 'en'

### 2.3 Indexing
**Approach**: Custom inverted index using Python dictionaries
- **Bangla Index**: 3,767 unique terms → 500 documents
- **English Index**: 18,331 unique terms → 591 documents
- **Storage**: Pickle format for fast loading

**Tokenization Strategy**:
- Lowercase normalization
- Unicode-aware regex tokenization
- Title tokens weighted 2x (appear twice in index)
- Minimum token length: 2 characters

---

## 3. QUERY PROCESSING (Module B)

### 3.1 Language Detection
**Tool**: `langdetect` library with fallback to Unicode range detection
- Detects Bangla (bn) vs English (en)
- Fallback: Checks for Bangla Unicode characters (U+0980 to U+09FF)

### 3.2 Query Normalization
- Lowercase conversion
- Whitespace removal
- Basic text cleanup

### 3.3 Translation Pipeline
**Bangla → English**:
- Model: Helsinki-NLP/opus-mt-bn-en (OPUS-MT)
- Transformer-based neural translation

**English → Bangla**:
- Tool: Google Translate API (free tier)
- Fallback solution due to model availability

### 3.4 Example Translations
| Original | Language | Translation |
|----------|----------|-------------|
| বাংলাদেশ ক্রিকেট | bn | Bangladesh Cricket |
| Dhaka traffic news | en | ঢাকা ট্রাফিক খবর |
| শিক্ষা ব্যবস্থা | bn | Education |

---

## 4. RETRIEVAL MODELS (Module C)

### 4.1 Model 1: BM25 (Lexical Retrieval)

**Implementation**: `rank-bm25` library (BM25Okapi variant)

**How it works**:
1. Tokenize query using same tokenizer as indexing
2. Compute BM25 scores for all documents
3. Rank by relevance score
4. Return top-k results

**Strengths**:
- Fast query execution
- Good for exact keyword matching
- Works well for named entities

**Weaknesses**:
- Fails with synonyms or paraphrases
- No semantic understanding
- Sensitive to translation quality

### 4.2 Model 2: Semantic Retrieval (Embeddings)

**Implementation**: sentence-transformers library

**Model**: `paraphrase-multilingual-MiniLM-L12-v2`
- 118M parameters
- Supports 50+ languages including Bangla
- 384-dimensional embeddings

**How it works**:
1. Pre-compute embeddings for all documents (offline)
2. Encode query into same embedding space
3. Compute cosine similarity with all document embeddings
4. Rank by similarity score
5. Return top-k results

**Strengths**:
- Captures semantic meaning beyond keywords
- Works with synonyms and paraphrases
- More robust to translation errors

**Weaknesses**:
- Slower than BM25 (requires neural network inference)
- Higher computational cost
- Needs GPU for large-scale deployment

---

## 5. SYSTEM ARCHITECTURE

### 5.1 Complete Pipeline
```
User Query (Any Language)
    ↓
[Language Detection] → Identify bn or en
    ↓
[Normalization] → Lowercase, cleanup
    ↓
[Translation] → Translate to opposite language
    ↓
┌─────────────────┬──────────────────┐
│ Same Language   │  Other Language  │
│ Search          │  Search          │
│                 │  (Cross-lingual) │
├─────────────────┼──────────────────┤
│ • BM25          │  • BM25          │
│ • Semantic      │  • Semantic      │
└─────────────────┴──────────────────┘
    ↓
[Combine & Rank Results]
    ↓
Display to User
```

### 5.2 Example Query Flow

**Query**: "cricket match score" (English)

1. **Detected Language**: en
2. **Translated to**: ক্রিকেট ম্যাচের স্কোর (Bangla)
3. **Searches Performed**:
   - English BM25: 3 results found
   - English Semantic: 3 results found
   - Bangla BM25: 0 results (no keyword matches)
   - Bangla Semantic: 3 results found
4. **Total Results**: 9 articles from both languages

---

## 6. RESULTS & OBSERVATIONS

### 6.1 System Performance

**Search Speed** (approximate):
- BM25: < 100ms per query
- Semantic: ~500ms per query (includes embedding computation)

**Retrieval Quality**:
- BM25 excels at exact matches and named entities
- Semantic model better for conceptual queries
- Cross-lingual retrieval works effectively via translation

### 6.2 Sample Results Comparison

**Query**: "শিক্ষা" (Education in Bangla)

**BM25 Results** (English docs after translation):
1. "Education needs youth leadership" (score: 6.674)
2. "Viva for assistant teacher recruitment..." (score: 5.310)

**Semantic Results** (English docs):
1. "Education needs youth leadership" (score: 0.454)
2. "When silence hurts more than the slap" (education context) (score: 0.438)

**Observation**: Both models found the same top article, showing complementary strengths.

---

## 7. CHALLENGES & SOLUTIONS

### 7.1 Crawling Challenges

**Problem**: Some Bangla sites had anti-scraping measures
**Solution**: 
- Implemented RSS feed parsing as fallback
- Added archive page crawling
- Used polite crawling (delays, headers)

### 7.2 Translation Limitations

**Problem**: English→Bangla OPUS-MT model not available on HuggingFace
**Solution**: Used Google Translate API as alternative (free, no authentication required)

### 7.3 Encoding Issues

**Problem**: Some Bangla text had character encoding errors
**Solution**: Used UTF-8 encoding throughout, graceful error handling with BeautifulSoup

### 7.4 Data Quality

**Problem**: Accidentally crawled some 404 error pages
**Impact**: Minor - semantic search still works, BM25 returns no results for these
**Mitigation**: Content validation (minimum length checks)

---

## 8. TECHNOLOGIES USED

### 8.1 Libraries & Frameworks

| Component | Technology |
|-----------|------------|
| Web Scraping | BeautifulSoup, Requests |
| Language Detection | langdetect |
| Translation | Transformers (OPUS-MT), Google Translate |
| Lexical Retrieval | rank-bm25 |
| Semantic Retrieval | sentence-transformers, scikit-learn |
| Storage | pickle, numpy, json |
| Development | Python 3.12, Google Colab |

### 8.2 Models Used

1. **Helsinki-NLP/opus-mt-bn-en**: Bangla→English translation
2. **paraphrase-multilingual-MiniLM-L12-v2**: Multilingual sentence embeddings
3. **Google Translate**: English→Bangla translation

---

## 9. AI TOOL USAGE LOG

### 9.1 Code Generation

**Tool Used**: Claude (Anthropic)

**Components Generated with AI Assistance**:
1. Web scraper with retry logic and RSS fallback
2. BM25 integration code
3. Sentence transformer embedding pipeline
4. Query processing functions
5. Result display formatting

**Verification Process**:
- All generated code was tested on live data
- Functions validated with multiple test queries
- Error handling tested with edge cases
- Performance measured and optimized

**Example Prompt**:
> "Create a Python function to scrape news articles from a given URL using BeautifulSoup. Extract title, body text, date, and URL. Handle encoding errors gracefully."

**Verification**: Code tested on all 10 news sites, successfully extracted metadata from valid articles.

### 9.2 Report Writing

**Tool Used**: Claude (Anthropic)

**Sections with AI Assistance**:
- System architecture descriptions
- Technical explanations of BM25 and embeddings
- README documentation structure

**Verification**: All technical details cross-referenced with library documentation.

---

## 10. DELIVERABLES

### 10.1 Code Repository
- **Location**: Google Colab notebook + Google Drive
- **Files**:
  - `CLIR_System_Complete.ipynb`: Complete implementation
  - README.md: Documentation
  - system_config.json: Configuration details

### 10.2 Dataset
- **Location**: `/content/drive/MyDrive/CLIR_Project/data/`
- **Files**:
  - `bangla_articles.json`: 500 Bangla articles
  - `english_articles.json`: 591 English articles
  - Individual site JSONs in `bangla_raw/` and `english_raw/`

### 10.3 Indexes & Models
- **Location**: `/content/drive/MyDrive/CLIR_Project/indexes/`
- **Files**:
  - `bangla_index.pkl`, `english_index.pkl`: Inverted indexes
  - `bangla_embeddings.npy`, `english_embeddings.npy`: Pre-computed embeddings
  - Translation models in `models/` directory

---

## 11. LIMITATIONS & FUTURE WORK

### 11.1 Current Limitations
1. **Dataset Size**: 1,091 articles (target was 5,000 for full team)
   - Acceptable for single-member reduced scope
2. **Translation Quality**: Google Translate for en→bn may have errors
3. **No Query Expansion**: Synonym handling not implemented
4. **No Named Entity Mapping**: Cross-lingual NE mapping not included

### 11.2 Potential Improvements
1. **Larger Dataset**: Crawl more articles or use existing datasets
2. **Better Translation**: Fine-tune bilingual models
3. **Hybrid Ranking**: Combine BM25 + Semantic scores
4. **Query Expansion**: Add synonym dictionary and morphological variants
5. **Evaluation Framework**: Implement Precision@k, Recall@k, nDCG metrics

---

## 12. CONCLUSION

This project successfully demonstrates a functional Cross-Lingual Information Retrieval system for Bangla and English. The system:

✅ Crawls and indexes real-world news data
✅ Processes queries in both languages
✅ Implements two complementary retrieval methods
✅ Achieves cross-lingual search via translation
✅ Returns relevant results from both language corpora

The system serves as a foundation for understanding CLIR challenges and can be extended with additional features for production use.

---

## REFERENCES

1. Ballesteros, L., & Croft, W. B. (1997). Phrasal translation and query expansion techniques for cross-language information retrieval. *ACM SIGIR Forum*.

2. Conneau, A., et al. (2019). Unsupervised Cross-lingual Representation Learning at Scale. *arXiv preprint arXiv:1911.02116*.

3. Feng, F., et al. (2022). Language-agnostic BERT Sentence Embedding. *arXiv preprint arXiv:2007.01852*.

4. Robertson, S., & Zaragoza, H. (2009). The Probabilistic Relevance Framework: BM25 and Beyond. *Foundations and Trends in Information Retrieval*.

5. Tiedemann, J., & Thottingal, S. (2020). OPUS-MT – Building open translation services for the World. *Proceedings of EAMT*.

---

**End of Report**
