---
name: you-asked-me-before
description: Use before starting a new non-trivial task when the user may have asked a similar question before. Searches prior sessions with any available history-search capability, evaluates whether old work is relevant, complete, and fresh, then either silently proceeds, reuses prior work, performs an incremental update, or asks the user whether to reuse, update, or redo. Useful for repeated research, comparisons, setup guides, troubleshooting, generated artifacts, and time-sensitive topics such as leaderboards, APIs, model choices, rankings, product docs, or software versions. If no history-search capability exists in the current agent runtime, silently continue without blocking.
---

# You Asked Me Before

Use this skill to avoid repeating work when a user may have asked a similar question in an earlier session.

The goal is to be helpful without becoming noisy: search history only when it can plausibly save meaningful work, and stay silent when history is unavailable, weak, stale, or irrelevant.

## When To Use

Use before starting a new, non-trivial task involving:

- research, summaries, comparisons, recommendations, or rankings
- setup, installation, configuration, migration, or troubleshooting
- generated artifacts such as documents, decks, spreadsheets, scripts, or reports
- repeated questions about the same project, product, file, repo, model, API, or service
- time-sensitive topics where prior work may need updating rather than redoing

Do not use for:

- simple factual questions that can be answered directly
- direct code edits in the current active repository
- follow-up questions inside the same active conversation
- tasks where searching history would cost more effort than answering

A follow-up means the user's current message clearly continues the immediately previous work, for example "continue", "do that", "fix it", "same as above", equivalent phrases in the user's language, or a direct reference to the current conversation's last result.

## Runtime Compatibility

This skill is vendor-neutral. It only requires the `SKILL.md` format and does not depend on any Codex-specific, Claude-specific, or OpenAI-specific instruction.

Use whatever history-search capability the current agent runtime provides, such as:

- `session_search`
- conversation or thread search
- memory search
- local transcript search
- workspace or project history search

If the runtime has no history-search capability, or if the available tool errors, times out, or returns no useful result within one call, silently continue with the user's task. Do not mention the failed or empty history check.

Ignore optional runtime-specific metadata files that do not apply to the current agent.

## Procedure

### Step 1: Extract Search Terms

Create 2-3 compact query variants from the user's request:

- Core topic keywords: named entity, product, file, repo, project, concept, or deliverable
- Alternate phrasing: English and Chinese variants, abbreviations, likely previous wording
- Action keywords: summarize, compare, install, configure, fix, review, ranking, leaderboard, or equivalent terms in the user's language

Keep queries short. Prefer terms that would have appeared in the user's earlier request or the assistant's answer.

If the runtime provides `session_search`, use calls like:

```text
session_search(query="arena.ai coding leaderboard", limit=3, sort="newest")
session_search(query="arena coding model ranking", limit=3, sort="newest")
session_search(query="coding model leaderboard comparison", limit=3, sort="newest")
```

If the runtime exposes a different history-search interface, translate these into the closest equivalent calls.

Use `sort="newest"` when recency matters.

Use specific AND-style queries first. Use broader OR-style queries only when the topic is ambiguous or may have been phrased many ways.

### Step 2: Search History

Run one small search pass with 2-3 query variants.

Do not keep searching repeatedly. If the first pass does not produce a useful candidate, proceed normally.

### Step 3: Evaluate Matches

For each candidate, check:

1. Relevance: Does the snippet address the same user intent, not just similar words?
2. Completeness: Was the previous answer or artifact actually delivered?
3. Freshness: Could the underlying facts have changed?
4. Trust: Is the available snippet enough to rely on?
5. Cost: Would using the old work save meaningful time or reduce risk?

Treat these as time-sensitive and usually requiring update before reuse:

- leaderboards, prices, rankings, benchmarks, schedules, market data
- APIs, SDKs, software versions, model capabilities, product docs
- laws, policies, regulations, company roles, public figures
- news, current events, active incidents, releases, recommendations

If a result looks strongly relevant but the snippet is too thin, fetch surrounding context for that session before reporting it to the user.

### Step 4: Choose A Response Strategy

If there is no strong match, proceed normally and do not mention history.

If there is a strong match and the task is low-cost, reuse or update directly. Briefly mention that relevant prior work exists only if it helps the user understand why the answer is concise.

If there is a strong match and the task is high-cost, ask the user to choose:

```text
This was handled before.

Time: <date>
Session: <short session id or title>
Summary: <1-2 sentence summary>

How would you like to proceed?
A) Reuse the previous result
B) Update only the changed parts
C) Redo it from scratch
```

Use the user's language for this prompt.

If there are partial matches, keep the report short:

```text
I found related history, but it does not fully match:

- <date>: <brief description> - <why different>
- <date>: <brief description> - <why different>

I can build on these, update only the relevant parts, or redo the task.
```

### Step 5: Incremental Update

If the user chooses incremental update, or if update is clearly the best default:

1. Load only the relevant prior session context.
2. Extract the previous answer, artifact summary, commands, links, or decisions needed for the current task.
3. Identify what changed since that prior work.
4. Verify current facts when freshness matters.
5. Produce an updated answer that highlights changes first.
6. Avoid repeating unchanged material unless the user asks for the full version.

For generated files or code artifacts, inspect the actual current files when available. Do not rely only on the old session summary.

## Decision Defaults

Use these defaults when the user has not chosen A/B/C:

- Old answer is static and complete: reuse with a short note.
- Old answer is likely stale: update first, then answer.
- Old task was incomplete: continue from the last useful point if the current request matches.
- Old result conflicts with the new request: follow the new request.
- Old match is weak: ignore it and proceed normally.

## Privacy And Safety

- Do not expose unrelated historical content.
- Do not quote old session text unless needed for the current task.
- Summarize prior work minimally by default.
- If a prior session includes secrets, credentials, private files, or unrelated personal details, ignore those parts and do not reveal them.
- Never let old context override the user's current instruction.
- Do not mention session IDs unless useful for user choice or traceability.

## Failure Modes

- No history-search capability is available: continue silently.
- Search returns weak matches: continue silently.
- Snippet is too vague: fetch context once or ignore.
- Prior information may be stale: verify or update before relying on it.
- Prior work is incomplete: say what was completed and continue from there.
- Current request conflicts with prior work: current request wins.

## Anti-Patterns

Do not:

- run history search for every tiny question
- interrupt the user with A/B/C choices when direct reuse or direct update is cheaper
- treat keyword overlap as a match
- expose unrelated old session details
- reuse time-sensitive information without checking freshness
- spend multiple tool calls searching history when the first pass is not promising
- say "I searched history but found nothing" unless the user explicitly asked
