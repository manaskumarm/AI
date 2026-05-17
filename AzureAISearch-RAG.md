# Azure AI Search & RAG Complete Guide (.NET + Azure)

# Table of Contents

- Introduction
- What is RAG?
- What is Azure AI Search?
- Why Azure AI Search?
- Core Azure AI Search Concepts
- Understanding Embeddings & Vector Search
- Azure AI Search Components
- Pricing Concepts
- Azure Portal Setup (Step-by-Step)
- Creating Index for Restaurant Menus
- .NET Integration
- Dynamic Filtering in .NET
- Prompt to Filter Conversion
- Hybrid Search
- Production Architecture
- Best Practices
- Common Mistakes
- Conclusion

---

# Introduction

Modern AI applications require more than SQL search.

Examples:
- AI Chatbots
- Restaurant Search
- Product Search
- FAQ Bots
- Enterprise Knowledge Systems
- Recommendation Systems
- RAG Systems

Traditional databases are not optimized for:
- semantic similarity
- typo tolerance
- ranking
- vector retrieval
- hybrid search

This is where Azure AI Search becomes powerful.

Azure AI Search supports:
- Full-text search
- Vector search
- Semantic ranking
- Hybrid search
- Filtering
- Faceting
- AI enrichment
- RAG architectures

---

# What is RAG?

RAG stands for:

```text
Retrieval-Augmented Generation
```

RAG combines:
- Search Engine
- Vector Database
- LLM (GPT/OpenAI)

to generate accurate AI responses.

---

# Problem Without RAG

LLMs:
- may hallucinate
- do not know private business data
- cannot directly access enterprise documents

Example:

```text
"What veg items are available under 100?"
```

GPT alone may guess.

---

# How RAG Works

```text
User Prompt
    ↓
Azure AI Search retrieves relevant data
    ↓
Retrieved documents/chunks sent to LLM
    ↓
LLM generates final response
```

---

# Example

```text
User:
"Show low spicy nonveg items under 150"

↓

Azure AI Search retrieves:
- Chicken Roll
- Chicken Noodles

↓

GPT generates response
```

---

# What is Azure AI Search?

Azure AI Search is Microsoft's enterprise search platform.

It provides:
- Full-text search
- Vector database capability
- Semantic ranking
- Hybrid retrieval
- AI enrichment
- Enterprise-grade indexing

---

# Why Use Azure AI Search?

## SQL Limitations

SQL is not optimized for:
- typo tolerance
- ranking
- semantic matching
- AI retrieval
- vector similarity

---

# Azure AI Search Advantages

| Feature | Benefit |
|---|---|
| Full-text search | Keyword search |
| Vector search | Semantic similarity |
| Hybrid search | Keyword + semantic |
| Filters | Exact match conditions |
| Facets | Aggregations |
| Semantic ranking | Better relevance |
| OCR | Search scanned docs |
| AI enrichment | Extract entities/phrases |

---

# Core Azure AI Search Concepts

---

# Index

An Index is the searchable structure.

Think:

```text
Search optimized database
```

Example:

```json
{
  "id": "1001",
  "name": "Chicken Biryani",
  "price": 250,
  "foodType": "NonVeg"
}
```

---

# Searchable Field

Used for:
- keyword search
- full-text search

Example:

```text
biryani
burger
paneer
```

---

# Filterable Field

Used for exact filtering.

Example:

```text
price lt 100
foodType eq 'Veg'
```

---

# Sortable Field

Used for:

```text
Sort by price
Sort by rating
```

---

# Facetable Field

Used for:
- category counts
- aggregations

Example:

```text
Veg: 50 items
NonVeg: 120 items
```

---

# Understanding Embeddings

Embeddings convert text into vectors.

Example:

```text
"Chicken Biryani"
↓
[0.234, 0.883, 0.112 ...]
```

Embeddings help AI understand meaning.

---

# Why Embeddings Matter

Embeddings enable semantic search.

Example:

```text
"Spicy rice item"
```

can match:
- Chicken Biryani
- Schezwan Rice

even without exact keywords.

---

# Vector Search

Search based on meaning rather than exact keywords.

---

# Keyword Search vs Vector Search

| Type | Example |
|---|---|
| Keyword Search | burger |
| Vector Search | crispy chicken sandwich |

---

# Hybrid Search

Hybrid Search combines:
- keyword search
- vector search
- semantic ranking

Best for production AI systems.

---

# Azure AI Search Components

---

# Data Sources

Defines where data comes from.

Examples:
- Azure SQL
- Cosmos DB
- Blob Storage
- Data Lake

---

# Indexers

Automated ingestion pipelines.

Flow:

```text
Data Source
    ↓
Indexer
    ↓
Index
```

---

# Skillsets

AI enrichment pipeline.

Examples:
- OCR
- Chunking
- Entity extraction
- Embedding generation
- Language detection

---

# Aliases

Virtual names for indexes.

Useful for:
- zero downtime deployment
- index versioning
- blue/green deployment

---

# Full Pipeline

```text
Data Source
    ↓
Indexer
    ↓
Skillset
    ↓
Index
    ↓
Alias
```

---

# Pricing Concepts

When creating Azure AI Search service, you see:

---

# Indexes

Number of searchable collections allowed.

---

# Vector Quota

Amount of vector storage available.

Important for:
- RAG
- embeddings
- semantic retrieval

---

# Total Storage

Storage available for:
- documents
- vectors
- metadata
- indexes

---

# Partitions

Controls:
- storage scaling
- indexing throughput

More partitions:
- more storage
- better indexing performance

---

# Replicas

Controls:
- query throughput
- high availability

---

# Partition vs Replica

| Component | Purpose |
|---|---|
| Partition | Scale storage/data |
| Replica | Scale queries/HA |

---

# Azure Portal Setup

---

# Step 1 — Create Azure AI Search Service

Go to:

```text
Azure Portal
→ Create Resource
→ Azure AI Search
```

Choose:
- Resource Group
- Service Name
- Region
- Pricing Tier

Recommended:
- Basic tier for development
- S1 for production

---

# Step 2 — Open Search Service

```text
Search Management
→ Indexes
```

---

# Step 3 — Create Index

Click:

```text
+ Create Index
```

---

# Restaurant Menu Index Design

---

# Sample Menu Document

```json
{
  "itemId": "MNU-001",
  "name": "Paneer Tikka Masala",
  "category": "Main Course",
  "subCategory": "North Indian Curry",
  "description": "Creamy tomato gravy",
  "price": 320,
  "foodType": "Vegetarian",
  "spiceLevel": "Medium",
  "rating": 4.5
}
```

---

# Recommended Schema

| Field | Type | Searchable | Filterable | Sortable |
|---|---|---|---|---|
| itemId | Edm.String | No | Yes | Yes |
| name | Edm.String | Yes | Yes | No |
| category | Edm.String | Yes | Yes | Yes |
| subCategory | Edm.String | Yes | Yes | Yes |
| description | Edm.String | Yes | No | No |
| price | Edm.Double | No | Yes | Yes |
| foodType | Edm.String | Yes | Yes | Yes |
| spiceLevel | Edm.String | Yes | Yes | Yes |
| rating | Edm.Double | No | Yes | Yes |

---

# IMPORTANT

Enable:
- Searchable
- Filterable
- Sortable

correctly while creating fields.

Otherwise filters/search will fail.

---

# .NET Integration

---

# Install NuGet Package

```bash
dotnet add package Azure.Search.Documents
```

---

# Menu Model

```csharp
using Azure.Search.Documents.Indexes;
using Azure.Search.Documents.Indexes.Models;

public class MenuItem
{
    [SimpleField(IsKey = true)]
    public string ItemId { get; set; }

    [SearchableField(IsFilterable = true)]
    public string Name { get; set; }

    [SearchableField(IsFilterable = true)]
    public string Category { get; set; }

    [SearchableField(IsFilterable = true)]
    public string SubCategory { get; set; }

    [SearchableField]
    public string Description { get; set; }

    [SimpleField(IsFilterable = true, IsSortable = true)]
    public double Price { get; set; }

    [SearchableField(IsFilterable = true)]
    public string FoodType { get; set; }

    [SearchableField(IsFilterable = true)]
    public string SpiceLevel { get; set; }

    [SimpleField(IsFilterable = true, IsSortable = true)]
    public double Rating { get; set; }
}
```

---

# Create Search Client

```csharp
using Azure;
using Azure.Search.Documents;

var client = new SearchClient(
    new Uri("https://YOUR_SEARCH_SERVICE.search.windows.net"),
    "restaurant-menu-index",
    new AzureKeyCredential("YOUR_API_KEY"));
```

---

# Upload Documents

```csharp
await client.UploadDocumentsAsync(menuItems);
```

---

# Exact Filtering Examples

---

# Get Products Less Than 100

```csharp
var options = new SearchOptions
{
    Filter = "price lt 100"
};

var result = await client.SearchAsync<MenuItem>("*", options);
```

---

# Veg Products Under 100

```csharp
var options = new SearchOptions
{
    Filter = "price lt 100 and foodType eq 'Vegetarian'"
};

var result = await client.SearchAsync<MenuItem>("*", options);
```

---

# NonVeg Less Spicy Under 50

```csharp
var options = new SearchOptions
{
    Filter = "price lt 50 and foodType eq 'NonVegetarian' and spiceLevel eq 'Low'"
};

var result = await client.SearchAsync<MenuItem>("*", options);
```

---

# Burger OR Biriyani

```csharp
var result = await client.SearchAsync<MenuItem>(
    "burger OR biriyani");
```

---

# Search + Filter Together

```csharp
var options = new SearchOptions
{
    Filter = "foodType eq 'Vegetarian' and price lt 200"
};

var result = await client.SearchAsync<MenuItem>(
    "paneer",
    options);
```

This means:
- Search paneer
- Only within veg items under 200

---

# Reading Results

```csharp
await foreach (var item in result.Value.GetResultsAsync())
{
    Console.WriteLine(item.Document.Name);
}
```

---

# Dynamic Filtering in .NET

In real systems:
- filters come dynamically
- user prompt changes
- API builds filters dynamically

---

# Example Request DTO

```csharp
public class MenuSearchRequest
{
    public string Query { get; set; }

    public double? MaxPrice { get; set; }

    public string FoodType { get; set; }

    public string SpiceLevel { get; set; }
}
```

---

# Dynamic Filter Builder

```csharp
public string BuildFilter(MenuSearchRequest request)
{
    var filters = new List<string>();

    if (request.MaxPrice.HasValue)
    {
        filters.Add($"price lt {request.MaxPrice.Value}");
    }

    if (!string.IsNullOrWhiteSpace(request.FoodType))
    {
        filters.Add($"foodType eq '{request.FoodType}'");
    }

    if (!string.IsNullOrWhiteSpace(request.SpiceLevel))
    {
        filters.Add($"spiceLevel eq '{request.SpiceLevel}'");
    }

    return string.Join(" and ", filters);
}
```

---

# Generic Search Service

```csharp
public async Task<List<MenuItem>> SearchMenus(MenuSearchRequest request)
{
    var filter = BuildFilter(request);

    var options = new SearchOptions
    {
        Filter = filter,
        Size = 20
    };

    var searchText = string.IsNullOrWhiteSpace(request.Query)
        ? "*"
        : request.Query;

    var response = await _client.SearchAsync<MenuItem>(
        searchText,
        options);

    var results = new List<MenuItem>();

    await foreach (var item in response.Value.GetResultsAsync())
    {
        results.Add(item.Document);
    }

    return results;
}
```

---

# Example Usage

---

# Example 1

```csharp
var request = new MenuSearchRequest
{
    Query = "burger",
    MaxPrice = 150
};
```

Equivalent:

```text
Search burger under 150
```

---

# Example 2

```csharp
var request = new MenuSearchRequest
{
    FoodType = "Vegetarian",
    MaxPrice = 100
};
```

Equivalent:

```text
Veg items under 100
```

---

# Prompt to Filter Conversion

For AI Chatbots:
- user types natural language
- LLM converts prompt to filters
- Azure AI Search executes filters

---

# Example

User prompt:

```text
Show me low spicy nonveg items under 200
```

Converted filter:

```text
foodType eq 'NonVegetarian'
and spiceLevel eq 'Low'
and price lt 200
```

---

# Recommended Architecture for AI Chatbot

```text
React Frontend
       ↓
.NET API
       ↓
Azure OpenAI
       ↓
Generate Filter
       ↓
Azure AI Search
       ↓
Return Results
```

---

# Recommended Production Architecture

```text
Frontend
    ↓
API Gateway
    ↓
Search Service (.NET)
    ↓
Azure AI Search
    ↓
Azure OpenAI
```

---

# Best Practices

---

# Use Proper Data Types

Correct:

```text
Price → Edm.Double
Rating → Edm.Double
```

Wrong:

```text
Price → String
```

---

# Normalize Values

Prefer:

```text
Veg
NonVeg
Low
Medium
High
```

Avoid inconsistent values.

---

# Keep Searchable Fields Limited

Do not make every field searchable.

This increases:
- storage
- indexing time
- costs

---

# Use Aliases in Production

Instead of:

```text
products-v1
```

Use:

```text
products-live
```

---

# Start Simple

Initially use:
- keyword search
- filters

Later add:
- vector search
- semantic ranking
- hybrid retrieval

---

# Common Mistakes

---

# Making Every Field Searchable

Bad for:
- performance
- cost
- indexing

---

# Wrong Data Types

Price should never be string.

---

# Overusing Skillsets

Skillsets are powerful but expensive at scale.

---

# Over-Chunking in RAG

Too many chunks:
- increase vector quota usage
- increase costs
- slow retrieval

---

# When to Use Skillsets

---

# Use Skillsets For

| Scenario | Use |
|---|---|
| OCR | Yes |
| PDF extraction | Yes |
| Knowledge mining | Yes |
| Rapid POC | Yes |

---

# Avoid Skillsets For

| Scenario | Better Option |
|---|---|
| Real-time systems | Custom pipeline |
| Complex workflows | Application logic |
| Large scale ingestion | Custom services |

---

# Conclusion

Azure AI Search is one of the best enterprise search platforms for:
- AI Chatbots
- RAG Systems
- Product Search
- Restaurant Search
- Knowledge Retrieval
- Semantic Search

For beginner systems:
- start with filters + keyword search

For advanced AI systems:
- move toward:
  - vector search
  - semantic ranking
  - hybrid retrieval
  - RAG architecture

For production systems:
- use proper indexing strategy
- optimize searchable fields
- manage vector quota carefully
- prefer custom ingestion pipelines for scalability

---

# Recommended Learning Path

1. Full-text Search
2. Filters
3. Sorting & Facets
4. Hybrid Search
5. Vector Search
6. Semantic Ranking
7. RAG Architecture
8. Azure OpenAI Integration
9. Production Scaling
10. Multi-tenant Search Systems
