# Tiki Product Analytics & Recommendation System

Graduation project for analyzing product data, ratings, and building a recommendation system from Tiki.vn e-commerce platform.

## Overview

This project implements a complete data pipeline for:
- **Web Crawling**: Scraping product and rating data from Tiki.vn
- **Data Processing**: Processing and storing data using distributed systems
- **Sentiment Analysis**: Classifying review comments as positive/neutral/negative
- **Product Recommendation**: Content-based recommendation using Word2Vec and FAISS

## Architecture

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   Crawler   │────▶│    Kafka    │────▶│   HBase     │
└─────────────┘     └─────────────┘     └─────────────┘
                                              │
                                              ▼
                    ┌─────────────┐     ┌─────────────┐
                    │   Kibana    │◀────│Elasticsearch│
                    └─────────────┘     └─────────────┘
                                              │
                                              ▼
                    ┌─────────────┐     ┌─────────────┐
                    │    FAISS    │◀────│   PySpark   │
                    └─────────────┘     └─────────────┘
```

## Tech Stack

| Component | Technology |
|-----------|------------|
| Message Queue | Apache Kafka (3 brokers) + Zookeeper (3 nodes) |
| Data Storage | Apache HBase |
| Search & Analytics | Elasticsearch 8.8.0 + Kibana 7.17.10 |
| Stream Processing | Apache Spark (PySpark 3.2.4) |
| ML/Recommendation | Word2Vec, FAISS, Random Forest |
| NLP | PyVi (Vietnamese Tokenizer) |
| Monitoring | Prometheus, Grafana, KMinion |

## Project Structure

```
graduation_project/
├── crawler/                    # Data crawling modules
│   ├── product/               # Product data crawler
│   ├── rating/                # Rating/review crawler
│   ├── category/              # Category crawler
│   ├── api_manager/           # API request management
│   ├── hbase/                 # HBase connection manager
│   └── data/                  # Kafka producer for data
├── elk_consumer/              # Elasticsearch data consumers
│   ├── create_product.py      # Product index creation
│   ├── create_rating.py       # Rating index creation
│   ├── save_product.py        # Product data indexing
│   ├── save_rating.py         # Rating data indexing
│   └── save_overview_rating.py # Aggregated rating stats
├── rcms/                      # Recommendation system
│   ├── faiss_rcms.py          # FAISS-based recommendation
│   ├── faiss_train.py         # Model training
│   ├── faiss_save.py          # Index persistence
│   ├── faiss_get.py           # Similarity search
│   ├── tf-idf.py              # TF-IDF vectorization
│   └── utils.py               # Utility functions
├── comment_predict/           # Sentiment analysis
│   ├── predict.py             # Comment classification model
│   ├── predict_colab.py       # Google Colab version
│   └── translate.py           # Translation utilities
├── data_hbase/                # HBase data operations
│   ├── read_hbase.py          # Read data from HBase
│   ├── get_review.py          # Fetch reviews
│   └── delete_table.py        # Table management
├── docker-compose.yml         # Infrastructure setup
└── requirement.txt            # Python dependencies
```

## Prerequisites

- Python 3.8+
- Docker & Docker Compose
- Java 8+ (for Spark)

## Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/kaiz-dev/graduation_project.git
   cd graduation_project
   ```

2. **Install Python dependencies**
   ```bash
   pip install -r requirement.txt
   ```

3. **Start infrastructure services**
   ```bash
   docker-compose up -d
   ```

## Dependencies

```
# Core Data Processing
elasticsearch==8.8.0
happybase==1.2.0
pyspark==3.2.4

# NLP - Vietnamese
pyvi==0.1.1

# Message Queue
kafka-python>=2.0.2

# ML & Vector Search
faiss-cpu>=1.7.0
scikit-learn>=1.0.0

# Data Manipulation
numpy>=1.21.0
pandas>=1.3.0

# Web Crawling
requests>=2.28.0
beautifulsoup4>=4.11.0
lxml>=4.9.0

# HBase Thrift Protocol
thrift>=0.16.0

# Visualization
matplotlib>=3.5.0
seaborn>=0.12.0

# Translation (optional)
googletrans==4.0.0-rc1
```

## Usage

### 1. Start the Crawler

```bash
# Start category crawler
python crawler/category/category_consumer.py

# Start product crawler
python crawler/crawl_consumer.py
```

### 2. Index Data to Elasticsearch

```bash
# Create indices
python elk_consumer/create_product.py
python elk_consumer/create_rating.py

# Save data
python elk_consumer/save_product.py
python elk_consumer/save_rating.py
```

### 3. Train Recommendation Model

```bash
# Train Word2Vec and FAISS index
python rcms/faiss_train.py

# Save the trained model
python rcms/faiss_save.py
```

### 4. Train Sentiment Analysis Model

```bash
python comment_predict/predict.py
```

### 5. Get Recommendations

```python
from rcms.faiss_rcms import recommend_similar_products

products = recommend_similar_products("tai nghe bluetooth")
print(products)
```

## Docker Services

| Service | Port | Description |
|---------|------|-------------|
| Zookeeper (x3) | 12181, 22181, 32181 | Kafka coordination |
| Kafka (x3) | 19092, 29092, 39092 | Message brokers |
| Kibana | 5602 | Data visualization |
| Kafka Exporter | 9304 | Kafka metrics |

## API Endpoints (Elasticsearch)

- Products Index: `product_idx`
- Ratings Index: `rating_index`
- Overview Ratings: `overview_rating_idx`

## Features

### Sentiment Classification
- **Positive**: Ratings 4-5 stars
- **Neutral**: Rating 3 stars  
- **Negative**: Ratings 1-2 stars

### Recommendation Algorithm
1. Text preprocessing with Vietnamese tokenization (PyVi)
2. Word2Vec embeddings for product names
3. FAISS indexing for fast similarity search
4. K-nearest neighbors for recommendations

## License

This project is developed as a graduation thesis.
