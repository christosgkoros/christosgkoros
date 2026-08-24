+++
title = 'Every API Invents a Query Language'
date = 2026-08-06T09:00:00+03:00
tags = ['apis', 'api-design', 'openapi', 'json-schema', 'query-language']
description = "Filter syntax gets reinvented on every search endpoint. The gap is not that no standard exists — it is that none of them can be described."
+++

![Photo by Vadim Bogulov on Unsplash](/posts/2026/08/06/every-api-invents-a-query-language.jpg "Photo by Vadim Bogulov on Unsplash")

## The moment it happens

You ship *GET /pets*. Someone asks to filter by status, so you add *?status=available*. Then by species. Then someone wants cats **or** dogs, and a query string has no way to say "or", so you invent something — *?species=cat,dog* — and quietly decide that commas mean OR today. Then someone wants pets born after 2020, excluding the adopted ones, and you find yourself designing a grammar inside a query parameter.

Every REST API that lives long enough grows a query language. It usually arrives around the third filtering request, it is never designed on purpose, and it ends up as a string that only your server understands.

## Everyone's is different, and that is not the problem

Look at the products that took this seriously and you find genuinely good languages. Elastic has its Query DSL, plus KQL and Lucene syntax on top. New Relic has NRQL. Dynatrace has DQL. Splunk has SPL. Azure has Kusto. These are not hacks — people build careers on them, and each fits its product's data model closely.

The problem is not that they differ. It is what they *are*. Each one was born inside a product, and its specification is a documentation site. Beautifully written, often, with a grammar section and a full operator table — but a web page. You cannot import a web page. You cannot *$ref* it, diff it, or hand it to a code generator. If you are building an ordinary REST API and you want a filter language, none of that work is available to you. You get to read it, admire it, and then write your own parser.

Elastic gets closest, because the Query DSL is JSON rather than a string. But it is Elasticsearch's query language — bound to its mappings, analyzers and scoring. It is not a component you can drop into your own API and expect to mean anything.

## The generic ones exist. They still do not help.

This is the part I expected to end differently when I started looking.

There *are* vendor-neutral filter languages. OData has *$filter*, an OASIS standard. SCIM's filter grammar is in RFC 7644, an actual RFC with actual ABNF. FIQL was an IETF draft, and RSQL grew out of it. Google's AIP-160 specifies filtering for their APIs and is public. So the situation is not the usual "nobody standardised this".

They all share one property: the filter is a **string**.

```text
?$filter=Price gt 20 and Category/Name eq 'Food'
```

Which means that when you describe that endpoint in OpenAPI, the most honest thing you can write is:

```yaml
parameters:
  - name: $filter
    in: query
    schema:
      type: string
```

Everything that matters — which operators exist, which fields are filterable, what nesting is allowed, what a valid operand looks like — lives in prose somewhere else. Your API description says "a string goes here". A generated SDK gives your users a *String* parameter and a link to the docs. Validation happens on your server, at runtime, after the request has already been built and sent.

That is the actual gap. Not the absence of a standard. The absence of a *describable* one.

## What "described" buys you

Structure the filter as JSON instead of a string and something changes: JSON Schema can describe it. And once JSON Schema can describe it, OpenAPI can reference it, which means it validates in CI, shows up in generated documentation, and produces real types in generated clients.

Concretely, this gets rejected before it leaves the client:

```json
{ "status": { "$eqq": "open" } }
```

A typo in a string-based language is a 400 at best, and at worst a filter that matches everything. In a described one it is a schema violation your editor underlines.

## So I tried building one

[json-query-language](https://github.com/christosgkoros/json-query-language) is a JSON-encoded, SQL-flavoured predicate language described by a single JSON Schema file. Here is one:

```json
{
  "$and": [
    { "status": "available" },
    { "$or": [
        { "species": { "$in": ["cat", "dog"] } },
        { "tags":    { "$hasAny": ["rescue", "senior"] } }
    ]},
    { "born": { "$gte": "2020-01-01" } }
  ]
}
```

Two rules cover most of it. Sibling members AND together at every level, so the object above reads top to bottom. And a bare scalar means equality — *{"status": "available"}* is shorthand for *$eq*. The shorthand deliberately stops at scalars: arrays and objects need an explicit operator, so *{"tags": ["a"]}* can never be silently read as either "equals" or "is one of".

## The scoping decision that makes it reusable

The one thing I would defend hardest is what the language leaves out. It describes the **predicate** only. No projection, no sort, no pagination.

That felt wrong at first, because a search endpoint clearly needs all three. But projection and ordering and paging are where APIs legitimately differ — cursor versus offset, sparse fieldsets, per-resource sort keys. Try to standardise those and you have written a framework nobody's API quite fits. The filter is the part that genuinely does not differ. "This field is greater than that value" means the same thing in every domain there is.

Keep the scope to the part that is universal, and the schema stays something you can actually drop into an existing API next to whatever pagination you already have.

## The decisions that were harder than expected

**Nulls.** Evaluation is three-valued, like SQL, which means *$not* does not do what people assume. Negating "status equals archived" excludes records where status is null, because NOT UNKNOWN is UNKNOWN and only TRUE matches. That surprises everyone, including me, repeatedly. Missing and null are also different things — *$exists* tests the key, *$isNull* tests the value.


**Partial implementations.** No backend implements every operator. The dangerous move is silently ignoring a clause you cannot execute, because dropping a filter clause *widens* the result set — you return more than the caller asked for, which for a filter is the worst available failure mode. So operators are grouped into profiles you take whole or not at all, an endpoint publishes which ones it accepts, and anything outside them is rejected rather than skipped.

## Where OpenAPI comes in

This was the point of the exercise, so it should be the boring part:

```yaml
components:
  schemas:
    Filter:
      $ref: 'https://raw.githubusercontent.com/christosgkoros/json-query-language/refs/heads/main/query-language-schema.json'
    PetSearchRequest:
      type: object
      required: [filter]
      properties:
        filter: { $ref: '#/components/schemas/Filter' }
```

On OpenAPI 3.1 the search itself is a *POST /pets/search*, because 3.1's Path Item has a fixed set of methods. On 3.2 you can describe the HTTP *QUERY* method through *additionalOperations* — which is the honest verb here, since QUERY is safe and idempotent and still carries a body. It says "this is a read" in a way POST cannot, so intermediaries may cache it and clients may retry it. QUERY is still an IETF draft, so the sensible thing is to ship both and let clients choose.

Either way, the filter grammar is written once and referenced from every search endpoint you have. Clients learn one language instead of one per endpoint.

## Where it met a real API

The research behind this fed into Postman's own search API, and what shipped there is a better argument for the previous section than anything I could write. [POST /search](https://learning.postman.com/api-docs/api-reference/search/postman-resources) takes a filter that will look familiar:

```json
{
  "elementType": "requests",
  "q": "testing",
  "filters": {
    "$and": [
      { "visibility": { "$eq": "internal" } },
      { "publisherIsVerified": { "$eq": true } }
    ]
  }
}
```

Same shape, much smaller language.

## It is early

It is pre-1.0 and the grammar may still move. Mostly I want to know whether the framing holds up. If you have built a search endpoint and invented a filter syntax for it, I would like to hear which part of this would not have worked for you.

[github.com/christosgkoros/json-query-language](https://github.com/christosgkoros/json-query-language)
