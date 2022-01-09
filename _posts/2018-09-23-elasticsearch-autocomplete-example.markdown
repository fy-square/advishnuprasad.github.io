---
layout: post
title: "Elasticsearch Autocomplete Example"
date: 2018-09-23 20:57:23 +0530
comments: true
categories: ["Elasticsearch", "Autocomplete"]
---

This post will help you to create autocomplete feature on Elasticsearch.

For Example, if you have a select box and when you search for `data` you would want to get all results that starts with `data`.

![ES1]({{ site.url }}/images/blog_1.png)
<!-- ![ES1](http://localhost:4000/images/blog_1.png) -->


This can be done easily in Elasticsearch. The important thing here is the type of analyzer and tokenizer than you use for autocomplete.

#### Step 1: Create Mapping with the following tokenizer and analyzer

```
PUT auto-test
{
  "settings": {
    "analysis": {
      "analyzer": {
        "autocomplete": {
          "tokenizer": "autocomplete",
          "filter": [
            "lowercase"
          ]
        },
        "autocomplete_search": {
          "tokenizer": "lowercase"
        }
      },
      "tokenizer": {
        "autocomplete": {
          "type": "edge_ngram",
          "min_gram": 2,
          "max_gram": 10,
          "token_chars": [
            "letter"
          ]
        }
      }
    }
  },
  "mappings": {
    "test": {
      "properties": {
        "technology": {
          "type": "text",
          "analyzer": "autocomplete",
          "search_analyzer": "autocomplete_search"
        }
      }
    }
  }
}
```

`autocomplete_search` analyzer is used for searching case insensitive words.

Step 2: Test Analyzers

```
POST auto-test/_analyze
{
  "field": "technology",
  "text": "database"
}
```

If you see the results, ES is creating the following tokens. You can change the min_gram based on your needs.

```
{
  "tokens": [
    {
      "token": "da",
      "start_offset": 0,
      "end_offset": 2,
      "type": "word",
      "position": 0
    },
    {
      "token": "dat",
      "start_offset": 0,
      "end_offset": 3,
      "type": "word",
      "position": 1
    },
    {
      "token": "data",
      "start_offset": 0,
      "end_offset": 4,
      "type": "word",
      "position": 2
    },
    {
      "token": "datab",
      "start_offset": 0,
      "end_offset": 5,
      "type": "word",
      "position": 3
    },
    {
      "token": "databa",
      "start_offset": 0,
      "end_offset": 6,
      "type": "word",
      "position": 4
    },
    {
      "token": "databas",
      "start_offset": 0,
      "end_offset": 7,
      "type": "word",
      "position": 5
    },
    {
      "token": "database",
      "start_offset": 0,
      "end_offset": 8,
      "type": "word",
      "position": 6
    }
  ]
}
```

Step 3: Create data and search

```
POST auto-test/test
{
  "technology": "data"
}

POST auto-test/test
{
  "technology": "database"
}

```
```
GET auto-test/_search
{
  "query": {
    "match": {
      "technology": "dat"
    }
  }
}
```

The results should be

```
{
  "took": 2,
  "timed_out": false,
  "_shards": {
    "total": 5,
    "successful": 5,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": 2,
    "max_score": 0.2876821,
    "hits": [
      {
        "_index": "auto-test",
        "_type": "test",
        "_id": "3woWB2YBsF1yfhimkgKz",
        "_score": 0.2876821,
        "_source": {
          "technology": "data"
        }
      },
      {
        "_index": "auto-test",
        "_type": "test",
        "_id": "xGsWB2YBVxVaheWYfgCc",
        "_score": 0.2876821,
        "_source": {
          "technology": "database"
        }
      }
    ]
  }
}
```
