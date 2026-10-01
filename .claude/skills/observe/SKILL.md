---
name: observe
description: Record the decisions made in the current conversation into the soul's persistent memory via soul_observe. Use when the user types /observe, or asks to "record/log/save what we decided".
argument-hint: "[optional focus, e.g. 'auth refactor' or a specific decision]"
allowed-tools: mcp__soul__soul_observe, mcp__soul__soul_remember, mcp__soul__soul_recall
---

# /observe — record what we've decided

Capture the decisions from this session and feed them through the soul's
psychology pipeline with `soul_observe`, so they persist across sessions.

Focus (optional): $ARGUMENTS

## Steps

1. **Collect decisions.** Scan the conversation (since the last `/observe`, if
   one ran) for things that were actually *decided*, not just discussed:
   - choices between approaches, and the reason for the choice
   - conventions or constraints the user set
   - scope calls (what we're deliberately *not* doing)
   - completed work that settles an open question

   If `$ARGUMENTS` is given, restrict to decisions related to that focus.
   If nothing was decided, say so and stop — don't call the tool.

2. **Check for duplicates.** Call `mcp__soul__soul_recall` with a short query
   summarizing the decisions. Drop any decision already stored verbatim;
   if a decision *changes* a stored one, note it as a revision ("Changed X → Y because Z").

3. **Record.** Call `mcp__soul__soul_observe` once with:
   - `user_input`: a concise statement of what the user asked for / the
     context that led to the decisions (1–3 sentences).
   - `agent_output`: the decision list, one per line, each formatted
     `Decision: <what> — Why: <reason>`.
   - `channel`: `"claude-code"`

4. **Pin critical ones (optional).** For decisions that are long-lived
   project rules (conventions, architectural constraints), also call
   `mcp__soul__soul_remember` with `memory_type="semantic"` and
   `importance` 7–9, so they rank highly on recall.

5. **Report back** briefly: the decisions recorded (bullet list), and the
   soul name / mood returned by the tool. If the `soul` MCP server is not
   connected, tell the user and print the decision list so nothing is lost.

## Guidelines

- Record decisions, not transcripts. Each line should stand alone months later.
- Use absolute dates (today's date) instead of "today" / "yesterday".
- Never record secrets, keys, or decrypted soul contents.
