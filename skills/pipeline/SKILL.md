---
name: pipeline
description: Review the opportunity pipeline and analyze resource allocation. Shows open opportunities, their staffing status, and how proposed team assignments affect the broader pipeline. Use when you want an overview of what's open or need to check for resource conflicts.
argument-hint: [opportunity-id or 'overview']
when_to_use: pipeline, open opportunities, what's available, resource conflicts, who else needs staffing
---

# Pipeline Review

Review the opportunity pipeline and analyze resource allocation.

## Instructions

**If `$ARGUMENTS` is empty or 'overview':**
1. Call `list_opportunities_by_status` (status: "open")
2. Present a summary table:

| Opportunity | Client | Stage | Start | Open Roles |
|-------------|--------|-------|-------|------------|
| ... | ... | ... | ... | X of Y |

Highlight any opportunities that are overdue for staffing or have urgent start dates within 4 weeks.

---

**If `$ARGUMENTS` contains an opportunity ID:**

1. Call `get_opportunity_by_id` to get full details including the current role assignments
2. Spawn a pipeline analyst subagent:
   - `description`: `"Pipeline resource analysis for [opportunityName]"`
   - `agent`: `"concierge:pipeline-analyst"`
   - `prompt`:
   ```
   OPPORTUNITY: [opportunityName] (ID: [opportunityId])
   CLIENT: [clientName]
   START: [startDate]
   
   CURRENT TEAM ASSIGNMENTS:
   [list each role and assigned employee if any]
   
   Analyze resource allocation: identify pipeline conflicts, capacity concerns, 
   and alternative staffing options.
   ```

3. Present the resource allocation analysis

---

Offer to drill into any opportunity or run a staffing workflow for it.
