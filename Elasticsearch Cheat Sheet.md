# 🔎 Elasticsearch Cheat Sheet

A production-focused quick reference for **developers, backend engineers, data engineers, and DevOps** working with Elasticsearch day-to-day.

---

## 🧠 Core Concepts at a Glance (1–3 lines each)

```text
Client -> Coordinating Node -> Shards (Primary/Replica) across Nodes
                 |                     |
              Mapping+Analysis      Inverted Index
```

- **Cluster**: A collection of nodes working together, sharing data and load. Identified by `cluster.name`.
- **Node**: A single Elasticsearch instance (JVM process) participating in a cluster; can hold data and/or coordinate requests.
- **Index**: A logical namespace for documents (similar to a table). Backed by one or more shards.
- **Document**: A JSON record stored in an index (similar to a row). Identified by `_id`.
- **Field**: A key in a document (e.g., `title`, `price`). Each field has a mapping (type + options).
- **Shard**: A Lucene index; the unit of horizontal scaling. Each index has N primary shards.
- **Replica**: A copy of a primary shard for HA + read scaling. Replicas can serve searches.
- **Mapping**: Schema definition for fields (types, analyzers, sub-fields, `nested`, etc.). Critical for correct queries/aggregations.
- **Analyzer**: Defines how `text` is tokenized/normalized at index/search time (tokenizer + filters).
- **Inverted Index**: Data structure mapping terms -> document postings, enabling fast full-text search.

---

## 🌐 Common REST APIs (Most Used)

### 🧩 Conventions

- **Base URL**: `http://localhost:9200`
- **Pretty output**: add `?pretty` (dev only)
- **Human**: add `?human=true` (dev)
- **CAT APIs** are human-readable; use JSON APIs for automation.

### 🏗️ Cluster APIs

```bash
GET _cluster/health
GET _cluster/health?level=shards

GET _cat/nodes?v
GET _cat/nodes?v&h=ip,name,roles,heap.percent,ram.percent,cpu,load_1m,master

GET _cat/indices?v
GET _cat/indices?v&h=health,status,index,pri,rep,docs.count,store.size
```

Also useful:

```bash
GET _cluster/settings?include_defaults=true
GET _nodes/stats
GET _nodes/_all/jvm
```

### 🗂️ Index APIs

#### ✅ Create index

```bash
PUT my_index
{
  "settings": {
    "number_of_shards": 3,
    "number_of_replicas": 1,
    "refresh_interval": "1s"
  },
  "mappings": {
    "properties": {
      "title": { "type": "text" },
      "status": { "type": "keyword" },
      "created_at": { "type": "date" }
    }
  }
}
```

#### 🗑️ Delete index

```bash
DELETE my_index
```

#### 📋 List indices (CAT)

```bash
GET _cat/indices?v
GET _cat/indices/my_index?v
```

#### 🧾 Get mapping / settings

```bash
GET my_index/_mapping
GET my_index/_settings
```

#### 🧬 Update mapping (add new fields only)

```bash
PUT my_index/_mapping
{
  "properties": {
    "author": { "type": "keyword" }
  }
}
```

> [!IMPORTANT]
> You generally **cannot change** an existing field’s type/analyzer in-place. The standard production approach is **reindex** into a new index, then switch using an **alias**.

#### ♻️ Refresh / force merge (use carefully)

```bash
POST my_index/_refresh
POST my_index/_forcemerge?max_num_segments=1
```

### 📄 Document APIs

#### Index document (auto ID)

```bash
POST my_index/_doc
{
  "name": "Alice",
  "age": 25,
  "status": "active",
  "created_at": "2026-03-10T10:00:00Z"
}
```

#### Index document (explicit ID)

```bash
PUT my_index/_doc/1
{
  "name": "John",
  "age": 30
}
```

#### Get document

```bash
GET my_index/_doc/1
```

#### Update document (partial)

```bash
POST my_index/_update/1
{
  "doc": {
    "age": 31
  }
}
```

#### Delete document

```bash
DELETE my_index/_doc/1
```

#### 📦 Bulk insert/update (NDJSON)

```bash
POST _bulk
{ "index":  { "_index": "test", "_id": "1" } }
{ "name": "John", "status": "active" }
{ "index":  { "_index": "test", "_id": "2" } }
{ "name": "Jane", "status": "inactive" }
```

> [!NOTE]
> **Bulk best practices**:
>
> - Send **NDJSON** lines and end with a newline.
> - Use batches (e.g., **5–15 MB** or a few thousand docs per request).
> - During backfills, consider `refresh_interval=-1`, then restore and refresh (operationally controlled).

---

## 🔎 Query DSL Cheat Sheet (Most Important)

### Search endpoint skeleton

```bash
GET my_index/_search
{
  "track_total_hits": true,
  "query": { "match_all": {} }
}
```

### 📝 Full-text queries (`text` fields)

#### `match`

```bash
GET my_index/_search
{
  "query": {
    "match": {
      "title": "elasticsearch guide"
    }
  }
}
```

#### `match_phrase`

```bash
GET my_index/_search
{
  "query": {
    "match_phrase": {
      "title": "distributed search"
    }
  }
}
```

#### `multi_match` (search multiple fields)

```bash
GET my_index/_search
{
  "query": {
    "multi_match": {
      "query": "kafka connector",
      "fields": ["title^2", "description", "tags"]
    }
  }
}
```

> [!NOTE]
> Tips:
>
> - Prefer `match` / `multi_match` for analyzed text.
> - Use `operator: "and"` for stricter matching (at the cost of recall).

### 🎯 Term-level queries (`keyword`, numeric, date)

#### `term` / `terms` (exact match)

```bash
GET my_index/_search
{
  "query": {
    "term": { "status": "published" }
  }
}
```

```bash
GET my_index/_search
{
  "query": {
    "terms": { "status": ["published", "draft"] }
  }
}
```

#### `range`

```bash
GET my_index/_search
{
  "query": {
    "range": {
      "price": { "gte": 10, "lte": 100 }
    }
  }
}
```

#### `exists`

```bash
GET my_index/_search
{
  "query": {
    "exists": { "field": "created_at" }
  }
}
```

#### `prefix`

```bash
GET my_index/_search
{
  "query": {
    "prefix": { "sku": "ABC-" }
  }
}
```

#### `wildcard` (use carefully)

```bash
GET my_index/_search
{
  "query": {
    "wildcard": {
      "email.keyword": {
        "value": "*@example.com"
      }
    }
  }
}
```

> [!WARNING]
> Tips:
>
> - Prefer `keyword` fields for term queries/aggregations (`status.keyword` or a dedicated `keyword` field).
> - Avoid leading wildcards like `"*foo"`; they can be expensive.

### 🧱 Boolean query (compose everything)

```bash
GET my_index/_search
{
  "query": {
    "bool": {
      "must": [
        { "match": { "title": "kafka" } }
      ],
      "must_not": [
        { "term": { "status": "deleted" } }
      ],
      "should": [
        { "match_phrase": { "title": "kafka connect" } }
      ],
      "filter": [
        { "term": { "status": "published" } },
        { "range": { "created_at": { "gte": "now-30d/d" } } }
      ],
      "minimum_should_match": 0
    }
  }
}
```

> [!IMPORTANT]
> Put non-scoring constraints in **`filter`** (cache-friendly and faster) instead of `must`.

### 🧰 Common extras you’ll use daily

#### Source filtering (reduce payload)

```bash
GET my_index/_search
{
  "_source": ["id", "title", "status"],
  "query": { "match_all": {} }
}
```

#### Highlight

```bash
GET my_index/_search
{
  "query": { "match": { "title": "elasticsearch" } },
  "highlight": {
    "fields": { "title": {} }
  }
}
```

#### Multi-index search

```bash
GET index_a,index_b/_search
{
  "query": { "match_all": {} }
}
```

---

## 📊 Aggregations Cheat Sheet

### Metric aggregations

#### `avg`, `sum`, `min`, `max`, `value_count`

```bash
GET my_index/_search
{
  "size": 0,
  "aggs": {
    "avg_price": { "avg": { "field": "price" } },
    "sum_price": { "sum": { "field": "price" } },
    "min_price": { "min": { "field": "price" } },
    "max_price": { "max": { "field": "price" } },
    "count_docs": { "value_count": { "field": "status" } }
  }
}
```

### Bucket aggregations

#### `terms` (group-by)

```bash
GET my_index/_search
{
  "size": 0,
  "aggs": {
    "group_by_status": {
      "terms": { "field": "status.keyword", "size": 20 }
    }
  }
}
```

#### `range`

```bash
GET my_index/_search
{
  "size": 0,
  "aggs": {
    "price_ranges": {
      "range": {
        "field": "price",
        "ranges": [
          { "to": 10 },
          { "from": 10, "to": 100 },
          { "from": 100 }
        ]
      }
    }
  }
}
```

#### `date_histogram` (time series)

```bash
GET my_index/_search
{
  "size": 0,
  "aggs": {
    "by_day": {
      "date_histogram": {
        "field": "created_at",
        "calendar_interval": "day"
      }
    }
  }
}
```

#### `histogram`

```bash
GET my_index/_search
{
  "size": 0,
  "aggs": {
    "price_hist": {
      "histogram": {
        "field": "price",
        "interval": 50
      }
    }
  }
}
```

> [!NOTE]
> Aggregation tips:
>
> - Aggregations typically require **`keyword`** (or numeric/date) fields.
> - For high-cardinality `terms`, tuning `shard_size` (and sometimes `execution_hint`) can help.

---

## 🧪 Text Analysis (Analyzer / Tokenizer / Token Filters)

### Key ideas (short)

- **Analyzer**: A named pipeline to convert text into tokens.
- **Tokenizer**: Splits text into tokens (e.g., `standard`, `whitespace`, `ngram`).
- **Token filter**: Normalizes/changes tokens (e.g., `lowercase`, `stop`, `asciifolding`).

### Define a custom analyzer

```bash
PUT my_index
{
  "settings": {
    "analysis": {
      "analyzer": {
        "my_analyzer": {
          "type": "custom",
          "tokenizer": "standard",
          "filter": ["lowercase", "asciifolding"]
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "title": { "type": "text", "analyzer": "my_analyzer" }
    }
  }
}
```

### Inspect analysis output

```bash
GET _analyze
{
  "analyzer": "standard",
  "text": "Elasticsearch in Production!"
}
```

Best practices:

- Decide analyzers early; changing analyzers later usually means **reindex**.
- Use multi-fields (`text` + `keyword`) for “search + exact/agg” needs.

---

## 🧾 Mapping Cheat Sheet (Types You Use Daily)

### Common field types

| Type               | Use for                    | Notes                                           |
| ------------------ | -------------------------- | ----------------------------------------------- |
| `text`             | full-text search           | analyzed, not ideal for aggs/sorting            |
| `keyword`          | exact match, aggs, sorting | not analyzed                                    |
| `date`             | timestamps                 | ensure consistent formats                       |
| `long` / `integer` | numbers                    | choose correct range                            |
| `boolean`          | true/false                 |                                                 |
| `object`           | JSON object                | flattens fields; cross-object matching pitfalls |
| `nested`           | arrays of objects          | preserves per-object matching semantics         |

### Mapping example (practical defaults)

```bash
PUT my_index
{
  "mappings": {
    "properties": {
      "title": {
        "type": "text",
        "fields": {
          "keyword": { "type": "keyword", "ignore_above": 256 }
        }
      },
      "status": { "type": "keyword" },
      "price": { "type": "long" },
      "created_at": { "type": "date" },
      "is_active": { "type": "boolean" },
      "attrs": { "type": "object" }
    }
  }
}
```

> [!WARNING]
> Pitfalls:
>
> - `object` arrays can cause unexpected matches; use **`nested`** when you need per-item matching.
> - Dynamic mapping is convenient but risky in production; consider templates to control field growth.

---

## 📄 Pagination (from/size, search_after, scroll)

### `from` / `size` (simple, but deep pagination is expensive)

```bash
GET my_index/_search
{
  "from": 0,
  "size": 10,
  "query": { "match_all": {} }
}
```

### `search_after` (recommended for deep pagination)

Requirements:

- Use a deterministic `sort`
- Use the last hit’s `sort` array as `search_after`

```bash
GET my_index/_search
{
  "size": 10,
  "sort": [
    { "created_at": "desc" },
    { "_id": "asc" }
  ],
  "search_after": ["2026-03-10T10:00:00Z", "1"],
  "query": { "match_all": {} }
}
```

> [!IMPORTANT]
> **Scroll is for reindex/export**. Avoid it for user-facing pagination; prefer `search_after`.

### Scroll (for reindex/export)

```bash
POST my_index/_search?scroll=1m
{
  "size": 500,
  "sort": ["_doc"],
  "query": { "match_all": {} }
}
```

Then:

```bash
POST _search/scroll
{
  "scroll": "1m",
  "scroll_id": "DXF1ZXJ5QW5kRmV0Y2gBAAAAAAA..."
}
```

Clear scroll:

```bash
DELETE _search/scroll
{
  "scroll_id": "DXF1ZXJ5QW5kRmV0Y2gBAAAAAAA..."
}
```

---

## 🔃 Sorting

### Basic sorting

```bash
GET my_index/_search
{
  "query": { "match_all": {} },
  "sort": [
    { "price": "asc" }
  ]
}
```

> [!NOTE]
> Sort on `keyword` (or numeric/date). Sorting on `text` is not supported unless you enable fielddata (generally avoid).

---

## ⚡ Performance Tips (Production)

- **Use `filter`** for non-scoring conditions (faster + cacheable) instead of `must`.
- **Avoid leading wildcards** (`*foo`) and overly broad regex; prefer `prefix`, `term`, or dedicated fields.
- **Use `keyword` fields** for aggregations and sorting.
- **Avoid deep pagination** with `from/size`; use `search_after`.
- **Use Bulk API** for ingestion; tune batch size, parallelism, and refresh strategy.
- **Control `_source`**: fetch only needed fields to reduce network and heap pressure.
- **Keep mappings intentional**: prevent mapping explosion (dynamic fields) and high-cardinality surprises.
- **Shard sizing**: prefer fewer, larger shards over many tiny shards; monitor heap + query latency.
- **Refresh interval**: for heavy ingestion, consider increasing or temporarily `-1` (with operational care).

---

## 🛠️ Useful Dev Tools

- **Kibana Dev Tools Console**: fastest way to prototype REST calls.
- **curl**: automation + quick checks (CI/CD, runbooks).
- **Postman/Insomnia**: collections + environments for repeatable calls.
- **elasticsearch-head** (or similar UIs): quick visibility (use cautiously in production environments).

Example `curl`:

```bash
curl -sS "http://localhost:9200/_cluster/health?pretty"
```

---

## 🩺 Troubleshooting Commands (Runbook Style)

### Cluster health and allocation

```bash
GET _cluster/health
GET _cluster/health?level=shards
GET _cat/shards?v
GET _cat/allocation?v
```

### Explain shard allocation (why unassigned)

```bash
POST _cluster/allocation/explain
{
  "index": "my_index",
  "shard": 0,
  "primary": true
}
```

### Hot threads (CPU contention)

```bash
GET _nodes/hot_threads
```

### Pending tasks (cluster coordination pressure)

```bash
GET _cluster/pending_tasks
```

### Thread pool rejections / node stats

```bash
GET _nodes/stats/thread_pool
GET _nodes/stats/jvm,fs,indices,transport,http
```

---

## 🧠 Bonus (Advanced)

### Nested query (correct matching inside arrays of objects)

Mapping example:

```bash
PUT my_nested_index
{
  "mappings": {
    "properties": {
      "items": {
        "type": "nested",
        "properties": {
          "sku": { "type": "keyword" },
          "qty": { "type": "integer" }
        }
      }
    }
  }
}
```

Query:

```bash
GET my_nested_index/_search
{
  "query": {
    "nested": {
      "path": "items",
      "query": {
        "bool": {
          "must": [
            { "term": { "items.sku": "ABC-1" } },
            { "range": { "items.qty": { "gte": 2 } } }
          ]
        }
      }
    }
  }
}
```

> [!WARNING]
> Script queries are powerful but can be slow and harder to cache. Prefer indexed/precomputed fields when possible.

### Script query (use sparingly)

```bash
GET my_index/_search
{
  "query": {
    "script": {
      "script": {
        "lang": "painless",
        "source": "doc['price'].size()!=0 && doc['price'].value > params.min",
        "params": { "min": 100 }
      }
    }
  }
}
```

Guidance:

- Scripts can be powerful but can be slow; prefer indexed fields / precomputed values where possible.

### Painless scripting basics

- Access doc values via `doc['field'].value` (when field has doc values).
- Use `params` for inputs.
- Handle missing fields (`doc['f'].size()==0`).

### Index templates (standardize mappings/settings)

```bash
PUT _index_template/logs_template
{
  "index_patterns": ["logs-*"],
  "template": {
    "settings": {
      "number_of_shards": 3,
      "number_of_replicas": 1
    },
    "mappings": {
      "properties": {
        "@timestamp": { "type": "date" },
        "service": { "type": "keyword" },
        "message": { "type": "text" }
      }
    }
  },
  "priority": 100
}
```

### ILM (Index Lifecycle Management) (hot/warm/delete)

Create a policy:

```bash
PUT _ilm/policy/logs_ilm
{
  "policy": {
    "phases": {
      "hot": {
        "actions": {
          "rollover": {
            "max_size": "50gb",
            "max_age": "7d"
          }
        }
      },
      "delete": {
        "min_age": "30d",
        "actions": { "delete": {} }
      }
    }
  }
}
```

Attach via template:

```bash
PUT _index_template/logs_with_ilm
{
  "index_patterns": ["logs-*"],
  "template": {
    "settings": {
      "index.lifecycle.name": "logs_ilm",
      "index.lifecycle.rollover_alias": "logs"
    }
  }
}
```

Bootstrap alias:

```bash
PUT logs-000001
{
  "aliases": {
    "logs": { "is_write_index": true }
  }
}
```

---

## 📌 Quick Reference Table (1 page)

### Everyday APIs

| Task                        | API                                | Example                                                  |
| --------------------------- | ---------------------------------- | -------------------------------------------------------- |
| Check cluster health        | `GET _cluster/health`              | `GET _cluster/health?level=shards`                       |
| List nodes                  | `GET _cat/nodes?v`                 | `GET _cat/nodes?v&h=ip,name,roles,heap.percent`          |
| List indices                | `GET _cat/indices?v`               | `GET _cat/indices?v&h=index,docs.count,store.size`       |
| Create index                | `PUT <index>`                      | `PUT my_index { ... }`                                   |
| Delete index                | `DELETE <index>`                   | `DELETE my_index`                                        |
| Get mapping                 | `GET <index>/_mapping`             | `GET my_index/_mapping`                                  |
| Update mapping (add fields) | `PUT <index>/_mapping`             | `PUT my_index/_mapping { "properties": ... }`            |
| Index doc (auto ID)         | `POST <index>/_doc`                | `POST my_index/_doc { ... }`                             |
| Index doc (with ID)         | `PUT <index>/_doc/<id>`            | `PUT my_index/_doc/1 { ... }`                            |
| Get doc                     | `GET <index>/_doc/<id>`            | `GET my_index/_doc/1`                                    |
| Update doc                  | `POST <index>/_update/<id>`        | `POST my_index/_update/1 { "doc": ... }`                 |
| Delete doc                  | `DELETE <index>/_doc/<id>`         | `DELETE my_index/_doc/1`                                 |
| Bulk ingest                 | `POST _bulk`                       | NDJSON actions + sources                                 |
| Search                      | `GET <index>/_search`              | `GET my_index/_search { "query": ... }`                  |
| Analyze text                | `GET _analyze`                     | `GET _analyze { "analyzer": "standard", "text": "..." }` |
| Shards view                 | `GET _cat/shards?v`                | `GET _cat/shards?v`                                      |
| Allocation explain          | `POST _cluster/allocation/explain` | `POST _cluster/allocation/explain { ... }`               |
| Hot threads                 | `GET _nodes/hot_threads`           | `GET _nodes/hot_threads`                                 |

### Most-used Query DSL patterns

| Task               | Query            | Notes                                     |
| ------------------ | ---------------- | ----------------------------------------- |
| Full-text search   | `match`          | use on `text` fields                      |
| Exact match        | `term` / `terms` | use on `keyword` fields                   |
| Range filter       | `range`          | numeric/date                              |
| Compose logic      | `bool`           | put non-scoring in `filter`               |
| Exact phrase       | `match_phrase`   | stricter, can reduce recall               |
| Multi-field search | `multi_match`    | boost with `^`                            |
| Field existence    | `exists`         | missing vs null differs by mapping/source |
| Prefix search      | `prefix`         | prefer over wildcard                      |
| Deep pagination    | `search_after`   | stable sort required                      |
| Export scan        | `scroll`         | not for user pagination                   |

### Aggregations quick list

| Need                | Aggregation                | Field type      |
| ------------------- | -------------------------- | --------------- |
| Average/Sum/Min/Max | `avg`, `sum`, `min`, `max` | numeric         |
| Count               | `value_count`              | keyword/numeric |
| Group-by            | `terms`                    | `keyword`       |
| Buckets by range    | `range`                    | numeric/date    |
| Time series         | `date_histogram`           | `date`          |
