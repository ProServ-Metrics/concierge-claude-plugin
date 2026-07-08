---
name: evaluate-lineup
description: Run a comprehensive 7-dimension evaluation of a proposed engagement team. Spawns 6 specialist evaluators in parallel, then synthesizes their structured outputs into an Executive Summary + full dimension report.
argument-hint: [opportunity-id] or describe the lineup
when_to_use: evaluate this team, how strong is this lineup, assess my team, rate these candidates
---

# Evaluate Lineup

Orchestrate a full parallel evaluation of a proposed engagement team.

## Step 1 — Gather the lineup

Parse `$ARGUMENTS`. You need:
- Opportunity name/ID and client name
- Role assignments: each role → employee name or ID

If you only have names, call **Search Employees by Name** to resolve IDs first.

## Step 2 — Fetch all employee data in one batch

Call **Get Employees** with all employee IDs and fields `Basic,Organization,TechnicalSkills,Strengths,DISC,ActiveEngagementRoleAssignments`.

Also call **Get Opportunity Details** if you have an opportunity ID (for required skills and start/end dates).

## Step 3 — Spawn all 6 dimension evaluators in parallel (ALL IN ONE RESPONSE)

Call the Task tool **6 times in the same response**. Each agent receives the full team data from Step 2. Each agent returns **structured JSON** (not markdown).

| # | Agent | Returns |
|---|-------|---------|
| 1 | `concierge:evaluator-technical-synergy` | `{teamStatus, capabilitiesCovered, criticalGaps, singlePointsOfFailure, ...}` |
| 2 | `concierge:evaluator-team-chemistry` | `{teamChemistryScore, collaborationNetworkScore, pairDetails, discTeamDynamics, ...}` |
| 3 | `concierge:evaluator-financial-impact` | `{totalRevenue, totalCost, overallMargin, individualMargins, ...}` |
| 4 | `concierge:evaluator-skills-coverage` | `{teamTechnicalCoverageScore, teamSynergyScore, backstopDetails, mentoringOpportunities, ...}` |
| 5 | `concierge:evaluator-team-experience` | `{teamExperienceScore, teamMembers[{roleExperience, tenure, versatility, industry, client}], ...}` |
| 6 | `concierge:evaluator-risk` | `{overallRiskLevel, risksByCategory, topRisks}` |

**Prompt for each agent:**
```
EVALUATION BRIEF
Opportunity: [name] (ID: [id])
Client: [clientName]
Start date: [startDate]

TEAM LINEUP
[For each role:]
- [Role]: [Employee Name] (ID: [employeeId])
  Skills: [top 5 skills with scores and technicalSkillKey]
  DISC: D:[d] I:[i] S:[s] C:[c]
  Strengths: [top 5 in rank order]
  Active engagements: [list or "None"]
  Engagement history: [engagement names, clients, dates]

Required skills for opportunity: [list from opportunity roles, include technicalSkillKey]

Return your analysis as structured JSON per your output schema.
```

Tell the user: > "Running 6-dimension evaluation in parallel. Synthesizing results shortly."

## Step 4 — Spawn the Executive Summary evaluator

Once all 6 dimension agents return their JSON, pass ALL of their outputs to `concierge:evaluator-executive-summary` in a single Task call:

```
EXECUTIVE SUMMARY BRIEF
Opportunity: [name] · Client: [clientName]
Team: [list roles + names]

DIMENSION OUTPUTS:
[technical-synergy JSON]
[team-chemistry JSON]
[financial-impact JSON]
[skills-coverage JSON]
[team-experience JSON]
[risk JSON]

Synthesize using the 8-step methodology. Return structured Markdown.
```

## Step 5 — Assemble and present the complete evaluation record

Combine the Executive Summary markdown with 6 fully rendered dimension sections into one document. **This is a complete evaluation record, not an executive digest — render every field from every evaluator's JSON output. Do not summarize, collapse, or drop items.**

Specifically required:

- **Executive Summary**: include the full Mitigation Action Plan table (all rows, all columns: risk / action / owner / timing / success indicator), the full Alternative Considerations section, and the red/yellow/green flag breakdown as separate lists — not collapsed into bullets.
- **Risk dimension**: render ALL `risksByCategory` entries (not just `topRisks`), each with description, likelihood, impact, and mitigation.
- **Team Chemistry dimension**: include the full `productiveTensions[]` and `riskDynamics[]` bullet lists, the complete `strengthsFinderDomainCoverage[]` table (all four domains with coverage %, isPrimary, isSecondary), and all `strengthsSynergies[]` entries.
- **Skills Coverage dimension**: include the full `mentoringOpportunities[]` list (all 3, with skill, proficiency scores, impactRank, and impact description) and all named `backstopDetails[]` pairs.
- **Team Experience dimension**: include all 5 dimension scores per member in the table (role, tenure, versatility, industry, client).
- **Financial dimension**: include the `isMockData` flag prominently if true (⚠ banner).

````markdown
# Lineup Evaluation: [Opportunity Name]
**Client**: [Client] · **Start**: [Date] · **Evaluated**: [today]

---

[EXECUTIVE SUMMARY section — including full Mitigation Action Plan table, Alternative Considerations, and flagged risk signals]

---

## Dimension Details

### 1. Technical Synergy
[capabilitiesCovered table · criticalGaps table · singlePointsOfFailure table · team strengths]

### 2. Team Chemistry
[Collaboration pair detail table · DISC dynamics with productiveTensions + riskDynamics bullets · StrengthsFinder domain coverage table · strengths synergies]

### 3. Financial Impact
[Per-member margin table · ⚠ mock data flag if isMockData=true]

### 4. Skills Coverage
[Team synergy % · missing project skills · backstop pairs table · full mentoring opportunities table with impactRank]

### 5. Team Experience
[5-dimension table per member: role / tenure / versatility / industry / client · standouts · gaps]

### 6. Risk Assessment
[Full risksByCategory grouped tables (all 7 categories) · top risks summary]

---
*Prepared by Concierge Staffing · ProServe Metrics*
````

After presenting the complete document, ask:
> "Would you like this in a different format — Word-style document, HTML rendered card, or PDF-ready layout?"

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

## Step 4 — Aggregate and present

Once all 6 evaluator agents have returned their Markdown sections, assemble them into a single well-formatted Markdown report:

```markdown
# Lineup Evaluation: [Opportunity Name]
**Client**: [Client] · **Start**: [Date] · **Evaluated**: [today's date]

---

## Scorecard
| Dimension | Score |
|-----------|-------|
| Technical Synergy | [X]/10 |
| Team Chemistry | [X]/10 |
| Financial Impact | [margin]% margin |
| Skills Coverage | [X]/10 |
| Team Experience | [X]/10 |
| Risk | [Low / Medium / High / Critical] |

**Overall Recommendation**: [1–2 sentences: go / go-with-conditions / do-not-go and why]

---

[Paste each agent's full Markdown output here, in the order above, separated by `---`]
```

Present the complete assembled Markdown to the user.

Then ask:
> "Would you like this report in a different format? I can produce it as a **Word-style document**, **HTML** (viewable as a rendered card), **PDF-ready layout**, or keep it as Markdown. Just let me know."

