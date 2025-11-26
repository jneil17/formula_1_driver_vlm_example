# F1 Driver Mood Analysis with Vision-Language Models

Analyze Formula 1 driver images using AI and search them semantically with natural language queries.

## What It Does

This Databricks notebook uses Claude Sonnet 4 to analyze the mood and expressions of F1 2025 season drivers, then builds a vector search index so you can find drivers using natural language descriptions like "confident and determined" or "celebrating victory".

## Key Features

- **AI-Powered Analysis**: Automatically generates mood descriptions for 20 F1 drivers
- **Semantic Search**: Find drivers by emotional expressions using natural language
- **End-to-End Pipeline**: Downloads images, analyzes them, and builds searchable indexes
- **Delta Lake Storage**: All data stored in Unity Catalog with Change Data Feed enabled

## Quick Start

1. Run the notebook in Databricks with access to:
   - `databricks-claude-sonnet-4` (image analysis)
   - `databricks-gte-large-en` (embeddings)
   - Vector Search endpoints

2. The notebook will:
   - Download 20 driver images from this repository
   - Analyze each image with AI
   - Build a vector search index
   - Enable semantic queries

## Example Queries

```sql
-- Find confident drivers
SELECT driver_name, enriched_caption
FROM VECTOR_SEARCH(
    index => 'formula1.default.f1_drivers_index',
    query_text => 'confident and determined professional athlete',
    num_results => 5
)
```

## Data Source

Driver images are sourced from the [formula1-datasets](https://github.com/toUpperCase78/formula1-datasets) repository, specifically the F1 2025 Season Drivers collection.

## Use Cases

This pattern works for any image collection where you want semantic search:
- Product catalogs
- Medical imaging
- Document analysis
- Content moderation
- Visual asset management

## References

- [Databricks AI Functions](https://docs.databricks.com/en/large-language-models/ai-functions.html)
- [Databricks Vector Search](https://docs.databricks.com/en/generative-ai/vector-search.html)
- [Unity Catalog Volumes](https://docs.databricks.com/en/connect/unity-catalog/volumes.html)
- [F1 Datasets Repository](https://github.com/toUpperCase78/formula1-datasets)

## License

Educational example. Please respect original image licensing terms.
