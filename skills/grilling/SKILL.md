---
name: grilling
description: Stress-tests an already-scoped plan, decision, or idea by mapping every open decision into a design tree and resolving it in batched rounds, each question carrying a recommended answer. Use when intent is already confirmed and a plan has several interdependent open decisions, or when the user says "grill me", "grill this", "stress-test this plan". Not for discovering what the user wants in the first place — that's `interview-me`.
---

# Grilling

## Overview

A plan with several open, interdependent decisions gets worse the longer those decisions stay silently assumed. Grilling surfaces every one of them as a design tree and resolves them with the user in batched rounds — fast, because each question already carries the agent's recommended answer.

## When to Use

- Intent is already confirmed (see `interview-me`) but the plan branches into several decisions that depend on each other
- The user explicitly says "grill me", "grill this", "stress-test this plan"
- A spec or design doc is about to be written and the open decisions need settling first

**When NOT to use:**

- You don't yet know what the user actually wants — that's `interview-me`, not this skill
- The decision was already made and implemented, and you're now auditing it — that's `doubt-driven-development`

## The Process

### 1. Map the design tree

Break the plan into every decision still open. Each decision branches into the decisions that hang off it — the ones whose answer depends on this one being settled first.

### 2. Work it in rounds

The **frontier** is every decision whose prerequisites are already settled: the questions answerable *now* without guessing at an answer you haven't heard yet. Ask the whole frontier in one round, each question numbered with a recommended answer attached, worded so "yes" accepts the recommendation:

```
❓ **Q1** - **<question title>**: <question body, may include multiple choices>

➡️ <your recommended answer>

---

❓ **Q2** - **<question title>**: <question body>

➡️ <your recommended answer>
```

A question whose answer depends on another question still open in this round belongs to a *later* round, not this one.

### 3. Offload facts, never decisions

When a frontier question needs a fact from the environment (filesystem, config, an API), look it up yourself or dispatch a sub-agent — don't ask the user for something you could find. Don't block the round on it: only the questions downstream of that fact wait; ask the rest of the frontier now. The *decisions* always go to the user.

### 4. Recompute and repeat

Each answered round reshapes the tree: settled decisions push the frontier outward and unblock what depended on them. Recompute the frontier and ask the next round.

### 5. Stop when the frontier is empty

Done when every branch of the design tree has been visited and nothing is left silently assumed. Do not act on the plan until the user confirms the shared understanding reached across all rounds.

## Common Rationalizations

| Rationalization | Reality |
|---|---|
| "I'll batch all the questions regardless of dependency" | A question whose answer depends on one still open invites a guess at the dependency. Defer it to a later round. |
| "I'll just assume the obvious default" | Every silent assumption is a place the plan can diverge from what the user wants. Ask it, with your recommendation attached — "yes" is cheap. |
| "I'll ask the user to look this up" | Facts are your job, never the user's. Look it up or dispatch a sub-agent; only decisions go to the user. |

## Red Flags

- A round mixing a question with one that depends on another still-open answer
- Asking the user to look up something a tool or sub-agent could find
- Proceeding to the plan before the frontier is empty
- A question with no recommended answer attached

## Verification

- [ ] Every open decision in the plan appears somewhere in the design tree
- [ ] Each round asked only the current frontier, never a question still blocked on an unanswered one
- [ ] Every question carried a recommended answer worded so "yes" accepts it
- [ ] Facts came from tools or sub-agents, never from asking the user to look something up
- [ ] The frontier emptied and the user confirmed shared understanding before any plan or code work began

## Interaction with Other Skills

- **`interview-me`**: upstream. Grilling assumes intent is already confirmed; if it isn't, run `interview-me` first — "grill me" from a user with no stated plan yet means `interview-me`, not this skill.
- **`spec-driven-development`**: downstream. The resolved design tree is the raw material for the spec.
- **`doubt-driven-development`**: different timing. Doubt-driven stress-tests a decision already made and implemented; grilling stress-tests decisions not yet made.
