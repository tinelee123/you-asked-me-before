# You Asked Me Before

An agent skill for checking whether a user has asked a similar question before, then reusing, updating, or redoing prior work without interrupting the user unnecessarily.

This repository uses the portable `SKILL.md` format. It is designed for any agent runtime that can read skills or load Markdown instructions, including Codex, Claude Code, and other SKILL.md-compatible agents.

If the current agent has a history-search capability, the skill uses it. If history search is unavailable, it silently falls back to normal task execution.

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

`SKILL.md` contains the portable skill instructions. `agents/openai.yaml` contains optional UI metadata for OpenAI/Codex runtimes; other agents can ignore it safely.

## Compatibility

The core skill is runtime-neutral:

- Codex/OpenAI skill runtimes can use `SKILL.md` and the optional `agents/openai.yaml` metadata.
- Claude Code and Claude Skills can use the top-level `SKILL.md`.
- Other SKILL.md-compatible agents can use the same instructions directly.
- Agents without native skill support can still use the contents of `SKILL.md` as custom instructions.

The only functional dependency is access to prior-session or conversation history. If an agent does not expose a history-search tool, this skill degrades gracefully and continues without blocking.

## Installation

Install or import this repository as a SKILL.md-compatible agent skill:

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

The skill has been validated with the skill creator validator:

```text
Skill is valid!
```

It also includes dry-run coverage for the main behavior paths: unavailable history search, weak matches, strong matches, stale prior answers, high-cost tasks, partial matches, current-conversation follow-ups, and conflicts between old context and the current request.
