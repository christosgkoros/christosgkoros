+++
title = 'Agents Are the New Microservices'
date = 2026-05-15T08:00:00+03:00
tags = ['agents', 'ai', 'architecture', 'microservices']
description = "... and not because of the hype"
+++

## 1. Single responsibility works best

People tend to model agents on people: a `product owner` agent, an `engineer` agent, a `QA` agent. That fails for the same reason `UserService` failed. A role is not a scope. It is a bundle of unrelated jobs that share a title.

Single-task agents hold up better. A `backlog grooming` agent. A `release notes` agent. A `migration risk review` agent. One job you can describe in a sentence, with a small tool surface and a clear output.

The microservices parallel is not the noun-shaped service. It is the behavior-shaped one: `place-order`, `calculate-tax`, `issue-refund`. All the logic for one operation behind one boundary, small enough to fit in one person's head.

## 2. But it is not free

Every split multiplies what you have to manage. Two agents means two prompts to version, two eval suites, two sets of tools, a handoff contract, and traces that span both. Observability stops being "did the agent get it right" and becomes "where in the chain did this go wrong" — a distributed-systems problem with fuzzier signals.

Microservices taught the same lesson. Each new service was another pipeline, dashboard, alert, runbook, on-call surface — the marginal cost almost always underestimated.

The strongest argument for splitting is evaluation. A broad `engineer` agent is hard to judge because almost any output can be defended. A `migration risk review` agent can be tested against known diffs, risks, and misses.

Small agents are easier to evaluate. That does not make them free. It makes the trade-off clearer.

## 3. Duplication happens

DRY is a strong instinct, and it is wrong as often as it is right here. Two agents both running a small validation, both touching the same resources — that is not always a smell. It is often the cheapest way to keep them independent.

The microservices version was shared libraries. They reduced duplication but created coupling that outlived the teams that wrote them. Drift was often cheaper than coordinating six teams around one package.

Agents have the same trade-off, sharper. If agent A depends on agent B, A inherits B's latency, failure modes, token cost, and prompt churn. Sometimes worth it. Often not.

The rule: duplicate by default, share only when the cost of drift exceeds the cost of coupling.

## 4. The contract is the product

API contracts are strict: schema, types, status codes, versions, tests. Agent contracts sit on natural language input and probabilistic output. The substrate is weaker, so the scaffolding has to be stronger, not looser.

Tool definitions versioned like API specs. Output formats tested. Failure modes named. A handoff between two agents is not just a prompt — it is a contract.

What is the upstream agent allowed to omit? What format must it produce? What should the downstream agent reject? When should it stop?

Microservices spent years learning this through schema discipline and contract testing. Agent systems are repeating the curve with worse tooling and fuzzier inputs — which makes the contract more important, not less.

## 5. The bill arrives later

Microservices had a cloud bill — lumpy, monthly, visible. Agents have a token bill: per call, growing with verbosity, context, retries, and depth of delegation.

That last one is the trap. Every orchestrator that calls a sub-agent stacks tokens. Every retry, every reflection, every "let me think step by step" stacks tokens. A three-layer system can cost ten times a one-layer system and feel only marginally smarter.

The cost is not only money. It is latency. Delegation depth turns one request into a chain of model calls, tool calls, summaries, and retries. The user feels that as slowness long before finance sees it as spend.

The architecture decides the bill. Once the call graph is set, the bill is set with it. Watch depth of delegation more than count of agents.

## 6. The analogy breaks

Microservices let hundreds of developers work on one product without tripping over each other. The architecture absorbed organizational scale.

Agents are the opposite shape. They exist so fewer developers can produce more. The architecture absorbs labor, not headcount.

That inverts the incentive. More services meant more team autonomy, good even when expensive. More agents means more tokens, more latency, more contracts — paid by the same small team trying to do more with less.

The structural lessons transfer. The optimization target does not. Microservices answered "how do we coordinate many people?" Agents answer "how does one person leverage many runs?" Same discipline, opposite goals.