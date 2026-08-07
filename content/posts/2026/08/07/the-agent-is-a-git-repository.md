+++
title = 'The Agent Is a Git Repository'
date = 2026-08-07T10:00:00+03:00
tags = ['agents', 'ai', 'automation', 'productivity']
description = "How I build the assistants I hand daily work to — a repo for memory, CLIs and MCP servers for hands, and a log you can read as a diff"
+++

![Photo by Praveen Thirumurugan on Unsplash](/posts/2026/08/07/the-agent-is-a-git-repository.jpg "Photo by Praveen Thirumurugan on Unsplash")

I open a screen full of terminals, each in a different repository. Each one is one of my AI assistants, ready to accept tasks. I usually have two or three running at the same time, thinking, planning and acting. It looks like a scene from a hacker film. It is not — the repos are almost entirely Markdown.

## Three layers, and only one is disposable

Every AI assistant I run is set up the same way. A repository is the source of truth: it holds what is true and what is allowed. Command-line tools and MCP servers are the hands: they are what the assistant can actually do. A harness like Claude Code or Codex is the engine: it runs a workflow defined in the repo, then goes away.

Only that third layer is disposable, and keeping it that way is most of the work. AI tools and models change every few months, my instructions change weekly — so neither the knowledge nor the audit trail can live inside the harness.

## The repository is the agent

There is no application code in these repos. An *AGENTS.md* sits at the root, and it does not hold the knowledge — it routes to it. *context/* is what is true here, *guidelines/* is how to judge, *templates/* is what good output looks like, *config/* is the same rules machine-readable.

When the agent learns something new during its loops — a command that behaves differently than documented, a convention nobody wrote down — that becomes a commit. Not a memory store the harness manages for me. I tried that; it did not hold up. A memory you cannot diff is a memory you cannot correct. If something changes in the company, or I find that something is not working, I describe the change and ask the agent to update its own knowledge — which arrives as a commit I can review.

## The hands are CLIs and MCP servers

An agent is only as delegable as its action surface is legible. In practice, depending on the external system, that surface should be either a CLI or an MCP server.

CLI is the preferred path: one interface, same behavior across harnesses, usually no harness-specific configuration. If your team already scripts it, the agent can usually use it as-is.

MCP servers are the second option when there is no suitable CLI. They work well, but you need to configure them in each harness you run. That setup usually does not change often, so it is manageable.

I avoid direct API integrations when possible. CLIs and MCP servers provide clearer, named actions and a more stable operational surface for delegation, permissions, and auditing.

## Named actions make the permission model writable

Because every capability has a name, the rules about it can be a file:

```yaml
autonomous_actions:
  - "update_description"
requires_review:
  - "change_priority"    # priority changes affect sprint planning
  - "close_item"         # cannot close without human sign-off
forbidden_actions:
  - "change_status"      # transitions are owned by the engineer
```

You cannot write that against a browser clicking buttons. You can write it against a subcommand or a tool name — so the tool surface is not only how the agent acts, it is the vocabulary the permission model is written in. Pick the tools and you have picked what is expressible. The comments matter as much as the rules; reasoning that lives in a head leaves with it.

## Every action is logged, and the log is the diff

Every change to the outside world produces a line before the session ends:

```json
{
  "timestamp": "2026-08-04T00:00:00Z",
  "item": "ABC-1455",
  "action": "dedupe_link",
  "reason": "Second independent report of the same validation bug."
}
```

The field carrying the weight is *reason*. Everything else is recoverable from the target system's history; the reason is the only place the agent's judgment survives the run — and judgment is what you are delegating.

Because the action names come from the tool surface, a log line is close to executable — reproduce it by hand and the log becomes a replay, not a report. Most commits here touch one file — that day's log — with the outcome in the subject line. The log file *is* the diff, so *git log* is not metadata about the work. It is the work journal, written in the same motion.

## An example: Open Collections Maintainer

One assistant I run like this maintains the Open Collections project.

The project covers APIs I am studying or working on that do not have a Postman presence, although they usually have reference documentation or even OpenAPI specs. Some notable ones are the Kubernetes API and the Dapr API.

Its job is simple: fetch the documentation or OpenAPI descriptions and synchronize those with collections in Postman workspaces.

The agent repository contains the directory of APIs I am following, the workflow, the rules, and of course the audit trail of changes for each new API version that is synced to Postman:

- https://github.com/christosgkoros/open-collections-maintainer

The public destination and project context are here:

- https://opencollections.tech/

## Autonomy is where this stops being optional

Run an agent attended and you are the safety net: every call surfaces for approval and you catch the bad one. On a scheduled run there is nobody. The only things between the agent and a mess are the tiers you wrote down and the fact that every effect lands as a reviewable commit. Remove either half and it is not a trade I would take.

The pitch for agents is usually the agent. The interesting artifact is the residue — the diff you read on a Monday without opening anything else. I do not trust the agent. I trust the log. That is a lower bar, and the one that scales, because you can hand more work to something whose mistakes you are guaranteed to find.
