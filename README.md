# F1 Driver Mood Analysis with Vision-Language Models

A Databricks notebook demonstrating the use of vision-language models (VLMs) and vector search to analyze and semantically search Formula 1 driver images based on mood and emotional expressions.

## Overview

This project showcases how to:
- Download and process images from external sources
- Use AI models to analyze image content and extract insights
- Build vector search indexes for semantic image retrieval
- Perform natural language queries on image metadata

The notebook uses Claude Sonnet 4 to analyze the mood and expressions of F1 2025 season drivers, then enables semantic search to find drivers based on emotional descriptions like "confident", "celebrating", or "focused".

## Features

- **Automated Image Pipeline**: Downloads 20 F1 driver images from GitHub and optimizes them for storage
- **AI-Powered Analysis**: Uses `databricks-claude-sonnet-4` to generate mood descriptions for each driver
- **Vector Search**: Creates embeddings from AI-generated descriptions for semantic search
- **Semantic Queries**: Search for drivers using natural language (e.g., "confident and determined professional athlete")
- **Delta Lake Integration**: Stores results in Delta tables with Change Data Feed enabled

## Prerequisites

- Databricks workspace with access to:
  - Unity Catalog
  - Vector Search endpoints
  - AI Query functions
- Required model endpoints:
  - `databricks-claude-sonnet-4` (for image analysis)
  - `databricks-gte-large-en` (for embeddings)

## Setup

### 1. Install Dependencies

The notebook automatically installs required packages:

```python
%pip install -U --quiet databricks-sdk==0.49.0 "databricks-langchain>=0.4.0" \
    databricks-agents mlflow[databricks] databricks-vectorsearch==0.55 \
    langchain==0.3.25 langchain_core==0.3.59 bs4==0.0.2 \
    markdownify==0.14.1 pydantic==2.10.1
```

### 2. Create Unity Catalog Objects

The notebook creates the following:
- **Catalog**: `formula1`
- **Schema**: `default`
- **Volume**: `driver_images` (for image storage)
- **Table**: `driver_images_table` (for AI-generated descriptions)

### 3. Configure Vector Search

A vector search endpoint and index are created automatically with timestamped names to avoid conflicts.

## Usage

### Running the Notebook

Execute the cells sequentially:

1. **Cell 1-2**: Set up the volume and clear any existing data
2. **Cell 3**: Download and compress driver images from GitHub
3. **Cell 4**: Analyze images using Claude Sonnet 4 to extract mood descriptions
4. **Cell 5**: View the enriched data with AI-generated captions
5. **Cell 6-7**: Install dependencies and initialize Vector Search client
6. **Cell 8**: Create vector search endpoint and index
7. **Cell 9**: Test semantic search capabilities
8. **Cell 10**: Display sample image

### Example Queries

Once the vector index is built, you can perform semantic searches:

```sql
-- Find confident drivers
SELECT search_score, driver_name, enriched_caption
FROM VECTOR_SEARCH(
    index => 'formula1.default.f1_drivers_index',
    query_text => 'confident and determined professional athlete',
    num_results => 5
)
```

```sql
-- Find celebratory moods
SELECT search_score, driver_name, enriched_caption
FROM VECTOR_SEARCH(
    index => 'formula1.default.f1_drivers_index',
    query_text => 'happy celebrating victory triumph',
    num_results => 3
)
```

```sql
-- Find focused/intense expressions
SELECT search_score, driver_name, enriched_caption
FROM VECTOR_SEARCH(
    index => 'formula1.default.f1_drivers_index',
    query_text => 'serious focused intense concentration',
    num_results => 3
)
```

## Data Source

Driver images are sourced from the [formula1-datasets](https://github.com/toUpperCase78/formula1-datasets) repository, specifically the F1 2025 Season Drivers collection.

### Included Drivers (2025 Season)

- Alexander Albon (#23)
- Carlos Sainz (#55)
- Charles Leclerc (#16)
- Esteban Ocon (#31)
- Fernando Alonso (#14)
- Gabriel Bortoleto (#5)
- George Russell (#63)
- Isack Hadjar (#6)
- Jack Doohan (#7)
- Kimi Antonelli (#12)
- Lance Stroll (#18)
- Lando Norris (#4)
- Lewis Hamilton (#44)
- Liam Lawson (#30)
- Max Verstappen (#1)
- Nico Hulkenberg (#27)
- Oliver Bearman (#87)
- Oscar Piastri (#81)
- Pierre Gasly (#10)
- Yuki Tsunoda (#22)

## Technical Details

### Image Processing

Images are:
- Downloaded as PNG files
- Resized to max 800x800 pixels (maintaining aspect ratio)
- Converted to JPEG format with 85% quality
- Optimized for storage efficiency

### AI Analysis Prompt

The notebook uses the following prompt for image analysis:
```
"Please describe the mood of the person in the image"
```

This generates natural language descriptions that are then embedded for semantic search.

### Vector Search Configuration

- **Pipeline Type**: TRIGGERED (manual refresh)
- **Primary Key**: File path
- **Embedding Source**: AI-generated mood descriptions
- **Embedding Model**: `databricks-gte-large-en`
- **Endpoint Type**: STANDARD

## Troubleshooting

### Vector Search Not Ready

If you see "Vector search not ready yet", the index is still building embeddings. This typically takes 5-10 minutes after creation. The notebook includes a fallback text-based search for immediate results.

### Index Already Exists

The notebook generates unique timestamped names for endpoints and indexes to avoid conflicts. If you need to reuse existing resources, update the `index_name` variable in the relevant cells.

## Architecture

```
GitHub Images → Databricks Volume → AI Analysis → Delta Table → Vector Index → Semantic Search
```

1. Images downloaded from GitHub
2. Stored in Unity Catalog volume
3. Analyzed by Claude Sonnet 4
4. Results saved to Delta table
5. Embeddings created via Vector Search
6. Natural language queries enabled

## Use Cases

This pattern can be extended to:
- Product catalog search based on visual attributes
- Medical image classification and retrieval
- Document analysis with multimodal data
- Content moderation and tagging
- Visual similarity search with natural language

## License

This notebook is provided as an educational example. The F1 driver images are sourced from a public repository; please respect their original licensing terms.

## Contributing

Feel free to extend this notebook with:
- Additional AI analysis prompts
- Different embedding models
- Alternative image sources
- Enhanced search capabilities
- Visualization components

## References

- [Databricks AI Functions](https://docs.databricks.com/en/large-language-models/ai-functions.html)
- [Databricks Vector Search](https://docs.databricks.com/en/generative-ai/vector-search.html)
- [Unity Catalog Volumes](https://docs.databricks.com/en/connect/unity-catalog/volumes.html)
- [F1 Datasets Repository](https://github.com/toUpperCase78/formula1-datasets)
