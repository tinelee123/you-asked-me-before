# You Asked Me Before

A Codex skill for checking whether a user has asked a similar question before, then reusing, updating, or redoing prior work without interrupting the user unnecessarily.

This skill is designed for agents that have access to `session_search` or an equivalent history-search tool. If history search is unavailable, it silently falls back to normal task execution.

## What It Does

- Extracts compact search terms from the user's request.
- Searches prior sessions when the task is likely worth deduplicating.
- Evaluates matches for relevance, completeness, freshness, trust, and cost.
- Reuses complete static answers when safe.
- Performs incremental updates when prior work may be stale.
- Asks the user whether to reuse, update, or redo only for high-cost tasks.
- Avoids exposing unrelated or sensitive historical session content.

## When To Use

Use this skill before starting a new, non-trivial task involving:

- research, summaries, comparisons, recommendations, or rankings
- setup, installation, configuration, migration, or troubleshooting
- repeated questions about a project, product, file, repo, API, model, or service
- generated artifacts such as documents, decks, spreadsheets, scripts, or reports
- time-sensitive topics where previous work may need updating instead of redoing

Do not use it for simple factual questions, direct code edits in the current active repo, or follow-up questions within the same conversation.

## Repository Structure

```text
.
|-- SKILL.md
`-- agents/
    `-- openai.yaml
```

`SKILL.md` contains the actual skill instructions. `agents/openai.yaml` contains UI metadata for OpenAI/Codex-compatible skill runtimes.

## Installation

Install or import this repository as a Codex-compatible skill:

```text
https://github.com/tinelee123/you-asked-me-before
```

The skill name is:

```text
you-asked-me-before
```

Example invocation:

```text
Use $you-asked-me-before to check whether I asked this before, then reuse, update, or redo as appropriate.
```

## Validation

The skill has been validated with the Codex skill creator validator:

```text
Skill is valid!
```

It also includes dry-run coverage for the main behavior paths: unavailable history search, weak matches, strong matches, stale prior answers, high-cost tasks, partial matches, current-conversation follow-ups, and conflicts between old context and the current request.
