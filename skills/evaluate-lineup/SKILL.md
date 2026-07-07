---
name: evaluate-lineup
description: Run a comprehensive evaluation of a proposed engagement team lineup. Assesses technical fit, team synergy, chemistry, financial impact, and risks. Use when you have specific people assigned to roles and want a structured assessment.
argument-hint: [opportunity-id] or describe the lineup
when_to_use: evaluate this team, how strong is this lineup, assess my team, rate these candidates
---

# Evaluate Lineup

Run a comprehensive evaluation of a proposed team lineup.

## Instructions

**Parse `$ARGUMENTS` or ask the user for:**
- Opportunity name or ID (used for context)
- Role assignments: each role → person's name or employee ID

If you only have names, call `list_employees` or `query_data_model` to resolve them to employee IDs.

**Fetch employee data:**
- Call `list_employees` with all employee IDs in one batch call
- Retrieve: technical skills, availability, engagement history, DISC profile (if available)

**Spawn the evaluator:**
Call the Task tool with:
- `description`: `"Lineup evaluation"`
- `agent`: `"concierge:team-evaluator"`
- `prompt`: Include the opportunity context and the full role→employee mapping with the fetched employee data

**Present results** in a clean format covering:
- Overall efficacy score (1-10)
- Team synergy score (1-10)
- Top strengths (3-5)
- Top risks with mitigations (up to 3)
- Recommended mentoring pairs

Offer to drill into any dimension if the user wants more detail.
