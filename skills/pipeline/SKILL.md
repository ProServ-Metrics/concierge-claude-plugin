---
name: pipeline
description: Review the opportunity pipeline and analyze resource allocation. Shows open opportunities, their staffing status, and how proposed team assignments affect the broader pipeline. Use when you want an overview of what's open or need to check for resource conflicts.
when_to_use: pipeline, open opportunities, what's available, resource conflicts, what needs staffing, what deals are in flight, what's coming up, show me the pipeline, staffing gaps, upcoming engagements---

# Pipeline Review

Review the current opportunity pipeline and analyze resource allocation.

## Instructions

1. Call `list_opportunities` with:
   - `opportunityStages: ["NEGOTIATION", "PROPOSAL"]`
   - `fields: "Basic,Roles,RoleRequiredTechnicalSkills"`

2. Sort results by start date ascending (soonest first).

3. Present a summary table, **prefaced with a brief note** that the view is filtered to NEGOTIATION and PROPOSAL stages and offer to show all stages:

| Opportunity | Client | Stage | Start | Open Roles |
|-------------|--------|-------|-------|------------|
| ... | ... | ... | ... | X of Y |

Count "open roles" as roles with no assigned employee (or unfilled role assignments).

Highlight any opportunities that are overdue for staffing or have urgent start dates within 4 weeks of today.

---

Offer to drill into any specific opportunity or run a staffing workflow for it.
