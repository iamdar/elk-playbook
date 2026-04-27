# Exercise 4: Direct Data Ingestion (Elasticsearch API)

## Problem
Quickly push a single JSON document to Elasticsearch for testing without using Logstash or Beats.

## Setup
Use `curl` to send a POST request to the Elasticsearch `_doc` endpoint.

## Solution

### 1. Ingest Data via CURL
Run the following command from your terminal:

```bash
curl -X POST "localhost:9200/my-manual-index/_doc/" \
     -H 'Content-Type: application/json' \
     -d'
{
  "timestamp": "2023-10-27T12:00:00",
  "user": "jdoe",
  "action": "manual_upload",
  "status": "success",
  "tags": ["testing", "api"]
}'
```

### 2. Verify Ingestion
You can verify the document exists by searching the index:

```bash
curl -X GET "localhost:9200/my-manual-index/_search?pretty"
```

### 3. Verification in Kibana
1.  Open Kibana and go to **Dev Tools**.
2.  Run the query: `GET /my-manual-index/_search`.
3.  Alternatively, create an index pattern for `my-manual-index` in **Stack Management** to view it in **Discover**.

### Expected Result
The document is immediately searchable and visible in Kibana, confirming that the Elasticsearch REST API is functional.
