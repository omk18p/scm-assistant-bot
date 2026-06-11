# SCM Assistant – Supply Chain RAG Chatbot

## Github Repository

[https://github.com/omk18p/scm-assistant-bot](https://github.com/omk18p/scm-assistant-bot)

## Public Chatbot URL

[https://cloud.flowiseai.com/chatbot/14fbc6d3-ca56-4c23-8ef1-5555e503b982](https://cloud.flowiseai.com/chatbot/14fbc6d3-ca56-4c23-8ef1-5555e503b982)

---

# Project Overview

SCM Assistant is a Retrieval-Augmented Generation (RAG) chatbot built using Flowise Cloud. The chatbot is designed to answer supply chain governance and supplier performance questions by leveraging information from a supplier dataset and a governance policy document.

The objective of the project was to create a conversational assistant capable of retrieving relevant information from both structured and unstructured data sources and providing business-focused responses.

---

# Data Sources Used

## 1. SupplyChain_Governance_Policy_v3.2.pdf

This document contains supplier governance policies including:

* Supplier tier definitions
* Service Level Agreements (SLAs)
* Supplier Watch List (SWL) rules
* Audit requirements
* Compliance thresholds
* Escalation procedures
* Disruption response procedures
* Regional concentration limits
* Volume Rebate Program criteria

---

## 2. supplier_performance_data.csv

This dataset contains:

* Approximately 2,000 Purchase Orders
* More than 100 suppliers
* 27 supplier-performance attributes

Key fields include:

* Supplier ID
* Supplier Name
* Compliance Score
* OTD Rate
* Defect Rate
* Risk Level
* Sustainability Score
* Region
* Product Category
* PO Value
* Disruption Indicators

---

# Architecture

```text
CSV Dataset
        +
PDF Policy
        │
        ▼
Document Store
        │
        ▼
Recursive Character Text Splitter
        │
        ▼
OpenAI Embeddings
(text-embedding-3-small)
        │
        ▼
Vector Retrieval
        │
        ▼
GPT-4o-mini
        │
        ▼
Final Response
```

---

# Technologies Used

## Platform

* Flowise Cloud

## LLM

* GPT-4o-mini

## Embedding Model

* text-embedding-3-small

## Retrieval Method

* Retrieval-Augmented Generation (RAG)

---

# Chunking Experiments

To evaluate retrieval quality and indexing performance, two chunking configurations were tested.

| Configuration   | Chunk Size | Chunk Overlap | CSV Chunks Generated | PDF Chunks Generated |
| --------------- | ---------- | ------------- | -------------------- | -------------------- |
| Configuration A | 1000       | 200           | 2000                 | 19                   |
| Configuration B | 500        | 100           | 4000                 | 35                   |

---

## Configuration A

### Settings

```text
Chunk Size: 1000
Chunk Overlap: 200
```

### Results

```text
CSV Chunks Generated : 2000
PDF Chunks Generated : 19
```

### Observations

* Larger chunks preserved more context.
* Better performance for policy-based retrieval.
* Lower embedding count.
* Faster indexing and retrieval.

### Advantages

* Better context preservation.
* Lower embedding cost.
* Smaller vector index.
* More stable responses.

### Limitations

* Individual supplier records were sometimes grouped together, reducing precision for supplier-specific retrieval.

---

## Configuration B

### Settings

```text
Chunk Size: 500
Chunk Overlap: 100
```

### Results

```text
CSV Chunks Generated : 4000
PDF Chunks Generated : 35
```

### Observations

* More granular chunking.
* Increased retrieval precision.
* Higher embedding count.
* Larger vector index.

### Advantages

* Better retrieval of small information segments.
* More detailed supplier-record retrieval.

### Limitations

* Higher embedding and storage requirements.
* Increased retrieval complexity.
* Context occasionally fragmented across multiple chunks.

---

# Final Configuration Selected

Configuration A was selected for the final implementation.

Reason:

* Better balance between context preservation and retrieval quality.
* Lower embedding cost.
* Faster indexing.
* More reliable policy-document retrieval.

---

# Chatflow Implementation

The chatbot was implemented using:

```text
OpenAI Chat Model
        │
        ▼
Conversational Retrieval QA Chain
        │
        ▼
Retriever
        │
        ▼
Document Store
```

The retrieval chain uses document chunks retrieved from the indexed knowledge base and supplies them to GPT-4o-mini for response generation.

---

# Features Implemented

✅ Flowise Cloud deployment

✅ PDF ingestion and processing

✅ CSV ingestion and processing

✅ Recursive text splitting

✅ OpenAI embeddings integration

✅ Conversational Retrieval QA workflow

✅ Public chatbot deployment

✅ Governance policy retrieval

✅ Supply-chain knowledge retrieval

✅ Multiple chunking experiments

---

# Sample Questions Tested

Examples of questions used during testing:

* What is Supplier Watch List (SWL)?
* What causes a supplier to be placed on SWL?
* What restrictions apply to suppliers on SWL?
* What is the regional concentration limit?
* What is a Level 3 disruption response?
* What are the Volume Rebate Program eligibility criteria?
* What defines a Tier-1 supplier?
* What defines a Tier-2 supplier?

---

# Challenges Faced

## 1. Structured CSV Retrieval

The biggest challenge encountered during development was retrieving and reasoning over large structured CSV datasets.

While policy-based retrieval worked reliably, analytical questions requiring:

* Aggregation
* Filtering
* Cross-record comparison
* Supplier ranking

were more difficult to answer consistently using a pure RAG pipeline.

---

## 2. Vector Store Configuration

Several iterations were required to configure:

* Embeddings
* Vector indexing
* Retrieval pipelines
* Document-store integration

to achieve reliable retrieval performance.

---

## 3. Retrieval Accuracy

Policy-document retrieval produced strong results, while supplier-level retrieval occasionally required additional tuning due to the size and structure of the CSV dataset.

---

## Problems Faced During Development

### 1. Document Store Indexing Challenges

A major challenge encountered during development was configuring the Document Store and ensuring that both the PDF and CSV data were properly indexed for retrieval.

Several iterations of:

* Document Store creation
* Embedding configuration
* Vector indexing
* Retrieval pipeline setup

were required before obtaining usable results.

---

### 2. Retrieval of Structured CSV Data

The governance policy PDF produced reliable retrieval results because it contains unstructured textual information.

However, the supplier dataset contains structured tabular information with:

* 2,000 purchase orders
* 116 suppliers
* 27 columns

Questions requiring:

* Aggregations
* Filtering
* Supplier ranking
* Cross-record calculations

proved more difficult for a standard RAG workflow.

Examples include:

```text id="w7x7jb"
Which suppliers qualify for the annual Volume Rebate Program?

Which region has the highest total PO value?

Which suppliers are on Supplier Watch List (SWL)?

Which product category has the highest average defect rate?
```

These questions require analysis across many rows rather than retrieval of a single document chunk.

---

### 3. Retrieval Accuracy

The chatbot successfully retrieved policy-related information but supplier-specific retrieval was less reliable.

This resulted in situations where:

* Policy definitions were retrieved correctly.
* Exact supplier lists were not consistently reproduced.
* Aggregate calculations were not always generated correctly.

---

## Why The Provided Validation Questions Were Not Fully Reproduced

The provided validation questions require both:

```text id="l1o7mg"
Policy Retrieval
+
Structured Dataset Analysis
```

Examples:

### Question 1

```text id="t33u9v"
Which Tier-3 suppliers have an active disruption flag?
```

Requires:

* Finding suppliers from CSV
* Checking disruption status
* Determining tier
* Applying policy response level

---

### Question 2

```text id="8w4m0v"
Which suppliers qualify for the Volume Rebate Program?
```

Requires:

* Tier filtering
* OTD filtering
* Defect-rate filtering
* Sustainability filtering
* Policy validation

---

### Question 3

```text id="spiv8v"
Which region has the highest total PO value?
```

Requires:

* Summing PO values
* Grouping by region
* Comparing totals
* Checking policy limits

---

### Question 4

```text id="kj7jyb"
Which suppliers are on SWL status?
```

Requires:

* Filtering suppliers with Compliance Score below threshold
* Retrieving supplier names
* Applying policy restrictions

---

### Question 5

```text id="f6q5uk"
Which product category has the highest average defect rate?
```

Requires:

* Grouping records by category
* Computing averages
* Comparing categories
* Applying policy thresholds

---

Because these questions depend heavily on structured dataset analysis, the chatbot was able to retrieve relevant policy information but was not always able to reproduce the exact expected supplier lists and aggregate calculations.

---

## Questions Successfully Answered

The chatbot successfully answered governance-policy and compliance-related questions including:

### Supplier Watch List (SWL)

```text id="u0ol0g"
What is Supplier Watch List (SWL)?

What causes a supplier to be placed on SWL?

What restrictions apply to suppliers on SWL?

What compliance score threshold triggers SWL status?
```

---

### Governance Policy Questions

```text id="4r6dvl"
What is the Volume Rebate Program?

What are the eligibility criteria for the Volume Rebate Program?

What is the regional concentration limit?

What happens when the concentration limit is exceeded?
```

---

### Supplier Tier Questions

```text id="kpxqj8"
What defines a Tier-1 supplier?

What defines a Tier-2 supplier?

What defines a Tier-3 supplier?

How are suppliers classified into tiers?
```

---

### Disruption Response Questions

```text id="b3ek6h"
What is a Level 3 disruption response?

What actions are required during a Level 3 disruption?

Who should be notified during a disruption event?
```

---

### Compliance & Audit Questions

```text id="tzl59q"
What happens when a supplier fails an audit?

What compliance score is considered acceptable?

What corrective actions are required after audit findings?
```

---

## Key Learning

This project highlighted the difference between:

```text id="d8m28h"
Unstructured Document Retrieval (PDF)
```

and

```text id="vg7yaz"
Structured Dataset Analytics (CSV)
```

The RAG architecture performed strongly for governance-policy retrieval, while supplier-level analytics would benefit from a hybrid architecture combining RAG with structured data querying and aggregation capabilities.

---

# What Was Successfully Completed

✓ Flowise Cloud account setup

✓ Document Store creation

✓ CSV loading

✓ PDF loading

✓ Chunking experiments

✓ OpenAI API integration

✓ GPT-4o-mini integration

✓ Embedding generation

✓ Conversational retrieval workflow

✓ Public chatbot deployment

✓ Knowledge-base creation

✓ Governance policy retrieval

---

# Current Limitations

* Retrieval accuracy for large tabular datasets can be improved.
* Some analytical supplier queries require stronger structured-data processing.
* Additional retrieval optimization could improve supplier-specific lookups.
* Hybrid RAG + structured analytics approaches could further enhance performance.

---

# Future Improvements

If additional development time were available, the following enhancements would be implemented:

1. Hybrid RAG + Structured Analytics architecture.
2. Dedicated supplier analytics pipeline.
3. Improved vector-store indexing strategy.
4. Enhanced retrieval ranking.
5. Better handling of aggregation-based business queries.
6. Advanced supplier-performance dashboards.

---

# Conclusion

SCM Assistant successfully demonstrates the implementation of a Retrieval-Augmented Generation chatbot using Flowise Cloud, OpenAI embeddings, and GPT-4o-mini. The system integrates both governance-policy knowledge and supplier-performance data into a unified conversational interface and provides a strong foundation for future enhancements involving advanced supplier analytics and structured-data reasoning.
