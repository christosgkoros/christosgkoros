+++
title = 'Breaking Changes in HTTP APIs'
date = 2026-08-06T08:00:00+03:00
tags = ['apis', 'api-design', 'breaking-changes', 'versioning']
description = "A reference list of the ways an API breaks the clients that already depend on it"
rule_anchors = true
+++

![Photo by Arisa Chattasa on Unsplash](/posts/2026/08/06/breaking-changes-in-http-apis.jpg "Photo by Arisa Chattasa on Unsplash")

A change is breaking if a client that worked yesterday has to change its code, its configuration, or its infrastructure to keep working today. That includes changes that keep every client running while quietly giving it wrong data — those are still breaking, only harder to notice.

Most of the list below follows from one rule. On the request side, anything that *narrows* what the API accepts is breaking, because traffic that was valid yesterday must stay valid. On the response side, anything that *widens* what the API may return, or *weakens* what it guarantees, is breaking, because the client only handles the shapes it was written against. The same edit is breaking in one direction and safe in the other.

Everything said here about a property applies equally to parameters and to headers.

## Endpoints and operations

### Removing a path

Clients calling it get an error instead of data.

### Removing a method

Clients that relied on that specific operation can no longer invoke it. Most get a 405, which they handle no better than a 404.

### Renaming or restructuring a path

Inserting a segment, reordering path parameters, or changing the case of one breaks every caller. Path matching is exact and case-sensitive.

### Renaming a path parameter

The URL a client builds is unchanged, so hand-written clients survive. Generated clients break, because the parameter name became an argument name.

### Changing the server URL, base path, or version prefix

Every client has the base URL pinned in configuration. Moving from */v1* to */v2* relocates the whole API.

### Renaming an operation identifier

It never appears on the wire, but code generators turn it into a method name. Every generated SDK fails to compile.

### Removing a supported request media type

Dropping form-encoded or XML bodies in favour of JSON only turns previously valid requests into a 415.

### Removing or changing a response media type

Clients negotiate with *Accept* and parse accordingly. Removing a representation gives them a 406; changing one, say from *application/json* to *application/problem+json*, breaks their parser.

### Changing the success status code

Moving from 200 to 204 removes the body clients were parsing. Moving from 201 to 202 changes when the resource actually exists.

### Removing or repurposing a documented status code

Clients branch on status codes. Reusing 409 for something that used to return 422 routes their error handling into the wrong branch, often a retry loop.

### Turning a synchronous operation asynchronous

Replacing a 201 with the created resource by a 202 plus a polling location means the client's next read no longer finds the resource. Sequential workflows break, and the failure is timing-dependent.

## Requests

Anything that shrinks the set of accepted requests.

### Removing a property

The API either rejects requests that still send it, or ignores it and quietly drops data.

### Renaming a property or parameter

A removal and an addition with no overlap. Every client fails at once, either rejected as missing a required field or accepted with the value discarded.

### Making an optional property required

Every client that was not already sending it starts getting rejected.

### Adding a required parameter or header

The same break one level out. A new mandatory query parameter, or a required *Idempotency-Key* or version header, invalidates every request already in flight.

### Removing a query parameter

Filters, sorts, *expand*, *fields*. If the server rejects unknown parameters the client fails immediately; if it ignores them, the client gets unfiltered data back, which is worse.

### Changing a property's type

String to integer, scalar to array, array to object, flattening or nesting a structure. Loosely typed clients may appear to survive and then corrupt data on the way in.

### Tightening a property's format

Shortening a maximum string length, narrowing a number range, lowering an array item limit, or removing an enum value. Values that used to be accepted now are not.

### Adding or tightening a pattern

Introducing a regular expression, or making an existing one stricter, rejects values that were previously fine. A common accident, because patterns are often added to *document* what a field already looks like, and real traffic rarely matches the idealised form.

### Tightening numeric constraints

Adding *multipleOf*, or turning a minimum into an exclusive minimum, removes values from the accepted set — including, in the exclusive case, the exact boundary value the documentation encouraged people to use.

### Requiring unique array items

Payloads with repeated entries that the server used to deduplicate are now rejected.

### Turning a free-form string into an enum

The strongest possible narrowing: from unbounded to a fixed list. Clients sending anything outside the list start failing, including values the API itself previously stored and returned.

### Making a nullable input non-nullable

Clients that send null to mean "clear this field" are rejected. Common in *PATCH* payloads, where null and absent mean different things.

### Closing the schema

Setting *additionalProperties: false* rejects every payload carrying a field the server does not know, including a newer client's fields hitting an older deployment mid-rollout.

### Removing a variant from a polymorphic request

Dropping a *oneOf* member, renaming a discriminator value, or changing the discriminator property makes a whole class of valid payloads unrepresentable.

### Marking a property read-only

It can no longer be set on write. Servers that reject it break clients immediately; servers that ignore it let clients believe they wrote a value that was thrown away.

### Changing a default value

Nothing fails. Results simply change for every client that omitted the property and relied on what used to happen.

### Changing a parameter's location

Moving a parameter between query, header, path, and cookie means the client sends it where the server no longer reads. If unknown query parameters are ignored, the value vanishes without a word.

### Changing parameter serialization

Switching an array between comma-separated and repeated-key form, or adopting *deepObject* for a nested filter, changes the bytes on the wire while the parameter name stays the same. It looks present and parses to the wrong value.

### Reducing the maximum request body size

Usually a gateway setting rather than an application change. Previously accepted uploads become a 413, and it hits the largest and most important clients first.

### Enforcing validation that was never enforced

The documented contract does not change at all; the server just starts holding people to it. Upgrading a validation library, adding schema middleware, or fixing a bug that let bad input through will reject real traffic that has been non-conforming for years. One of the most common breaks in practice, and no comparison of two API documents can see it.

## Responses

Anything that adds to what the API may return, or subtracts from what it guarantees.

### Removing a required property

Clients that expect it and rely on it break.

### Renaming a response property

Every reader looking up the old key finds nothing. Depending on the client, that is a crash, a null, or a default quietly substituted for real data.

### Making a required property optional

The break is intermittent, showing up only on the responses that omit it, which makes it hard to trace back.

### Making a non-nullable property nullable

The same failure in a different shape. Clients dereference the value directly, and generated clients often type it as non-optional, so a null fails deserialization before any application code runs.

### Changing a response property's type

Strictly typed clients fail to deserialize. Loosely typed ones coerce and carry on with corrupted values.

### Widening a property's format

Longer strings, bigger numbers, more array items, new enum values. Clients that sized a column, a buffer, or a switch statement to the old bounds cannot cope with the new ones.

### Adding a variant to a polymorphic response

A new *oneOf* member, or a new discriminator value, is something the client has never seen. Generated deserializers commonly throw; hand-written switch statements fall through to a branch that was never meant to run.

### Changing the collection envelope

Moving between a bare array and a wrapped object, or renaming the wrapper, changes the root shape. Every path expression, every deserialization target, and every pagination assumption breaks at once.

### Changing identifier format

Integers to UUIDs, adding a type prefix, lengthening an opaque token. Breaks clients that stored the value in a narrower column, validated its shape, sorted on it, or inferred meaning from it — and it breaks both directions, because clients send back what they were given.

### Changing date, time, or timezone representation

Moving from a local timestamp to UTC, changing precision, or switching between epoch seconds and milliseconds usually still parses somewhere. The result is data that is wrong by a fixed offset or a factor of a thousand, with no error anywhere.

### Changing numeric representation or precision

A 64-bit integer sent as a JSON number overflows in clients whose numbers are doubles; sent as a string it breaks their arithmetic. Changing monetary rounding or decimal places quietly changes totals.

### Removing a response header

Headers are contract. Dropping *Location* breaks the follow-up read after a create, dropping *ETag* disables conditional requests and optimistic concurrency, dropping a total-count or rate-limit header breaks pagination and backoff.

### Changing the error response shape

The part of the contract most often left undocumented and most relied upon. Renaming an error code, restructuring the validation-error array, or migrating to a problem-details format breaks the error handling of every client that branches on it.

### Removing a link relation

For clients that navigate by links rather than building URLs, a removed or renamed relation is a removed capability. The hypermedia equivalent of deleting a path.

## Authentication and access

These deserve their own group because fixing them usually needs a human on the client side, not just a code change.

### Making a public operation authenticated

Every anonymous caller starts getting a 401, with no workaround short of obtaining credentials.

### Removing or replacing a security scheme

Retiring Basic auth for OAuth, or an API key for signed requests, forces every client to be rewritten and re-provisioned.

### Adding a required scope, or splitting an existing one

Existing tokens do not carry the new scope, so authorization fails even though authentication succeeded. Often produces a 403 where clients only handle 401.

### Changing where credentials travel

Moving an API key from a query parameter to a header, or from a custom header to *Authorization*. Worse than a lost parameter, because the request may not be rejected at all — just processed as anonymous.

### Shortening credential lifetime, or rotating without overlap

Clients that cache a token for the old duration and have no refresh path break. Rotating a signing key or shared secret with no window where both are valid breaks everyone at the instant of the switch.

### Introducing per-user authorization filtering

Correctly scoping a collection that used to return more than the caller should have seen is the right fix and still a breaking change. Responses shrink, pages shift, downstream reports change totals, and no status code changes, so nothing alarms.

## Behaviour and meaning

Same request, same response shape, different meaning. Almost none of this is visible in an API description, and much of it raises no error at all.

### Changing an endpoint's business logic

Clients that depend on the existing behaviour, or on the results it produced, stop working as intended.

### Changing what a property means

Applications keep using it according to the old definition. The shape is unchanged, so no validation, diff, or type check can catch it.

### Changing default sort order, or dropping an ordering guarantee

Clients depend on observed order whether or not you promised it. A changed default, or a non-deterministic order from a parallelised query, breaks pagination correctness — items skipped or repeated across pages — with no error.

### Changing pagination

Reducing default or maximum page size, switching from offset to cursor, changing cursor encoding, or altering what *total* and *has_more* mean. Cursor changes are the worst: clients that persisted a cursor cannot resume.

### Changing idempotency or retry semantics

If a retried request used to be deduplicated and now creates a second resource, every client with a retry policy starts producing duplicates. Starting to deduplicate makes intentional repeat submissions disappear.

### Changing the consistency model

Adding a cache or a read replica moves you from read-after-write to eventual consistency, and breaks the write-then-read sequence most clients are built on. Failures are intermittent and load-dependent.

### Adding or removing a side effect

An endpoint that starts sending a notification, writing an audit record, or triggering a downstream workflow does something the client did not ask for. One that stops means an integration silently no longer happens.

### Changing cascade or referential behaviour

Making a delete cascade to children, or ceasing to, changes what exists after the call. Newly enforcing a foreign-key or uniqueness constraint rejects writes that used to succeed.

### Requiring optimistic concurrency control

Starting to require *If-Match* on updates returns a 428 or 412 to every client that does not send it. Clients that do send it start seeing conflicts they have no handling for.

### Changing partial-failure behaviour on batch operations

Moving a bulk endpoint from best-effort with per-item results to all-or-nothing, or the reverse, inverts the client's error handling. It either stops retrying items that did fail, or retries a whole batch that partly succeeded.

### Changing search or filter semantics

Making a filter case-sensitive, switching exact matching to fuzzy, changing tokenization, or changing whether multiple filter values combine with AND or OR. Result sets change while every response stays perfectly valid.

### Changing time granularity, rounding, or retention

Aggregating by day instead of by hour, changing a rounding rule, or shortening how far back history goes changes numbers and truncates series. Reports built on the API stop reconciling.

### Removing a deprecated feature, or bringing a sunset date forward

Deprecation is not removal. The break happens at removal, and it lands on exactly the clients that never read the announcement.

## Infrastructure and transport

None of this appears in an API description, and no contract tool catches it. It breaks clients just as hard.

### Reducing a rate limit or introducing a quota

Lowering a limit, adding a new quota dimension, or narrowing a burst allowance turns working traffic into a 429. Clients without backoff turn that into an outage of their own making.

### Reducing a timeout or a response size cap

Fails exactly the largest, slowest, most valuable requests, often only in production and only for the biggest clients.

### Raising the minimum TLS version or changing cipher suites

Clients on older runtimes fail at the handshake, before any HTTP exists. They see an unspecific network error, which makes it slow to diagnose.

### Changing certificates, certificate authorities, or breaking pinning

A different CA breaks clients with a restricted trust store; any certificate change breaks clients that pinned a leaf. Both fail at the handshake.

### Changing hostnames or egress IP ranges

Many clients sit behind a firewall allowlist. Changing the hostname, or the addresses it resolves to, blocks them at the network layer while the API is perfectly healthy. Adding addresses is as breaking as removing them.

### Changing HTTP protocol requirements

Requiring HTTP/2, dropping HTTP/1.1, requiring SNI or ALPN, or disabling keep-alive breaks clients whose stack cannot comply — again below the HTTP layer, so the error messages are useless.

### Changing compression or transfer-encoding

Returning gzip regardless of what the client asked for, or refusing compressed request bodies, breaks clients that do not negotiate properly. Switching to chunked responses breaks anything that relied on *Content-Length*.

### Tightening CORS policy

Removing an allowed origin, method, or request header breaks browser clients at the preflight. Nothing changes for non-browser clients, so the break is invisible to most testing.

### Changing caching semantics

Making a response cacheable that was not, lengthening *max-age*, or changing *Vary* causes clients and intermediaries to serve stale or cross-contaminated data. Making a response uncacheable instead multiplies latency and load.

### Introducing a redirect

Forcing HTTP to HTTPS, or relocating a path, breaks clients that do not follow redirects — and, with 301 and 302, clients whose HTTP library drops the method, the body, or the *Authorization* header along the way.

### Changing regional endpoints or data residency

Consolidating or relocating regional endpoints changes the URL and can change which jurisdiction the data sits in. The second part may put a client in breach of its own compliance commitments, which no amount of client code can fix.

### Removing a sandbox or test environment

Retiring a sandbox, changing its reset policy, or letting it drift from production breaks clients' ability to develop and test, and quietly invalidates their test suites.

### Changing the token or discovery endpoint

Relocating an OAuth token endpoint, an OIDC discovery document, or a JWKS URL breaks authentication for every client that pinned it, which is most of them — many libraries cache discovery results indefinitely.

## Changes that are usually safe

Not all change is breaking. These are additive and normally fine — each with the client population it still breaks.

### Adding a new path or operation

Unless it shadows an existing path in a router that matches greedily, like */orders/summary* against */orders/{id}*.

### Adding an optional request property

Unless it has no server-side default, so omitting it produces undefined behaviour.

### Adding a response property

Unless clients were generated with a closed schema, or they hash, sign, or echo back the whole payload.

### Relaxing a request constraint

A wider range, a longer string, a new enum value. Unless the value gets echoed back in a response, which makes it a response widening in the other direction.

### Narrowing a response constraint

Unless clients validate responses against the published schema, or have already stored wider values.

### Adding a response header

Unless an intermediary has a total header size limit, or the client treats unknown headers as an error.

### Adding an optional query parameter

Unless it changes the default behaviour of requests that omit it.

### Adding a new enum value to a request field

Unless the field is shared between request and response.

### Adding a new error status code

Unless clients only handle the codes previously documented and treat everything else as fatal.

### Marking something deprecated

Unless the deprecation is enforced rather than announced, which is just removal wearing a label.

### Documentation-only edits

Unless the documentation *was* the contract, and the edit changes what is guaranteed rather than how it is described.

The pattern repeats: a change is safe only relative to how strictly clients are written. Which is why an API needs a stated tolerance policy — what clients must agree to ignore in order to keep the additive changes above additive.

The longer version of this list, with stable identifiers for each category, how each one can be detected, and what to do instead, lives in [breaking-changes-categories](https://github.com/christosgkoros/breaking-changes-categories).
