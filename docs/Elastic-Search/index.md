---
title: Elastic Search (part 1 of many)
date: "2022-03-24T12:12:03.284Z"
description: "Match bool prefix might be the solution you're looking for."
---

## Background

One of the best things about my work place ([Octopus Energy](https://octopusenergy.com/)) is that I get to learn new tools and have the luxury to try and fail.

Earlier this year, I was given a task to implement elastic search for a model that is heavily used by our customers. The real challenge was to improve lookups, but for me, it was learning elastic search and doing it right the first time. I strongly believe learning becomes easier when I can understand what needs to be learned. The diffcult part is finding out what to learn and how to apply. Fortunately, I found some previous PRs by my colleagues and the best part was concise commits and PR structure they maintained. I just had to figure out how to put in the exact same structure and make it work in my case.

Side note - One of the best arts I've picked at Octopus Energy is that I absolutely love chronlogical commits, PR descriptions that try to solve one problem and do it thoroughly. My PR descriptions will also contain enough notes to help the next person get started on a similar work. I also do it out of selfishness because I don't usually get it right the first time, so it is easier for me to just look back again and have all the commands and resources available.

## What was my problem?

I already got over the big part which was understanding and implementing elastic search. However, it wasn't being used in the way I thought it would be used. I mean, I thought users would always search using the perfect search terms, they would always have the right spellings or they won't try to search using a keyword in the middle of a sentence. Turns out I was all wrong. It wasn't the user's fault, it was my thinking of how this service would be used.

## Answer

Get feedback from your users and try to use the service as they would use. One of the pain points that was immediately obvious was that I totally forgot the importance of combining search results to narrow down a search. In other words, I simply forgot how to do an "AND" search. This reminds me of my ECE 251 class at [NJIT](https://www.njit.edu/) and my professor's famous quote: _Don't lose common sense_.

## Solution

Perform an "AND" search and combine it's result with a `match_bool_prefix` search. For example:

```python
import elasticsearch_dsl as es

def get_concise_results():
    parent = "Some input that should be used for an exact match"
    child = "Some input that can be fuzzy"

    parent_query = es.Q("match", parent=parent)

    child_query = es.Q("match_bool_prefix", child=child)

    s = Search(index="some_index").query(parent_query).query(child_query)

    return s.exectue()
```

By doing an "AND" search, which is simply calling the query function twice, I already improved my results by a very far margin. Then comes `match_bool_prefix`, which allows fuzzy match but also supports prefix query. Which means users could search by something in the middle of a sentence and still get results, or if they tried to lookup using the keywords at the start of the sentence, they would still get much better results. If you use Kibana (highly recommended) to perform elastic search queries here's the query:

```
{
  "query": {
    "bool" : {
      "match": {
          parent_field:"parent_query"
        },
      "match_bool_prefix":{
          child_field: "child_query"
        }
      ]
    }
  }
}
```

</br>

## References

> If you've made this far, I would highly recommend to read through our [coding conventions](https://github.com/octoenergy/conventions/blob/master/git.md#pull-requests)

> Match bool prefix ([link to Elastic Search docs](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-match-bool-prefix-query.html))
