---
name: evaluate-lineup
description: Run a comprehensive 6-dimension evaluation of a proposed engagement team — technical synergy, team chemistry, financial impact, skills coverage, team experience, and risk. Spawns all six evaluator agents in parallel.
argument-hint: [opportunity-id] or describe the lineup
when_to_use: evaluate this team, how strong is this lineup, assess my team, rate these candidates
---

# Evaluate Lineup

Run a full parallel evaluation of a proposed engagement team.

## Step 1 — Gather the lineup

Parse `$ARGUMENTS`. You need:
- Opportunity name/ID and client name
- Role assignments: each role → employee name or ID

If you only have names, call **Search Employees by Name** to resolve IDs first.

## Step 2 — Fetch all employee data in one batch

Call **Get Employees** with all employee IDs and fields `Basic,Organization,TechnicalSkills,Strengths,DISC,ActiveEngagementRoleAssignments`.

Also call **Get Opportunity Details** if you have an opportunity ID (for required skills and start/end dates).

## Step 3 — Spawn all 6 evaluators in parallel (ALL IN ONE RESPONSE)

Call the Task tool **6 times in the same response** before waiting for any results. Pass each agent the full team data you fetched in Step 2.

| # | Agent | Description |
|---|-------|-------------|
| 1 | `concierge:evaluator-technical-synergy` | Skill gaps, critical gaps, single points of failure |
| 2 | `concierge:evaluator-team-chemistry` | DISC dynamics, collaboration history, StrengthsFinder coverage |
| 3 | `concierge:evaluator-financial-impact` | Margins, billing rates, profit per role |
| 4 | `concierge:evaluator-skills-coverage` | Per-member coverage scores, backstop pairs, mentoring opportunities |
| 5 | `concierge:evaluator-team-experience` | Role, industry, and client experience scores |
| 6 | `concierge:evaluator-risk` | Risk categories, severity, mitigations |

**Prompt for each agent** — fill in the bracketed fields:
```
EVALUATION BRIEF
Opportunity: [name] (ID: [id])
Client: [clientName]
Start date: [startDate]

TEAM LINEUP
[For each role:]
- [Role]: [Employee Name] (ID: [employeeId])
  Skills: [top 5 skills with scores]
  DISC: D:[d] I:[i] S:[s] C:[c]
  Strengths: [top 5 in rank order]
  Active engagements: [list or "None"]
  Engagement history: [total count, key clients/industries]

Required skills for opportunity: [list from opportunity roles]

Run your specialized evaluation and return your structured output.
```

Tell the user:
> "Running 6-dimension evaluation. This will take a moment."

## Step 4 — Aggregate and present results

Once all 6 return, synthesize into a final report:

```
## LINEUP EVALUATION: [Opportunity Name]
Client: [Client] · Start: [Date]

─────────────────────────────────────────
### Scorecard
| Dimension | Score |
|-----------|-------|
| Technical Synergy | [X]/10 |
| Team Chemistry | [X]/10 |
| Financial Impact | [margin]% |
| Skills Coverage | [X]/10 |
| Team Experience | [X]/10 |
| Risk | [Low/Medium/High/Critical] |

**Overall: [X]/10**

─────────────────────────────────────────
[Paste each evaluator's full output, separated by headers]
─────────────────────────────────────────
```

Offer to drill into any dimension or swap a team member.

