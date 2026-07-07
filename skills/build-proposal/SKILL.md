---
description: Build a client-facing proposal document for an opportunity — Executive Summary, Experience & Qualifications, and Team & Timeline.
argument-hint: opportunity ID (e.g. OPP-2025-042)
---

# Build Client-Facing Proposal

Build a polished, client-facing proposal document for opportunity **$ARGUMENTS**.

## Data gathering — run these MCP calls in parallel

1. **Get Opportunity Details** — fetch the full opportunity: client, description, engagement type, roles needed, required skills, proposed start/end dates.
2. **Get Client Details** — fetch the client's industry, past engagements with the firm.
3. **List Engagements By Status** (completed + active) — you'll filter for relevant ones in the next step.
4. **List Employees With Matching Technical Skills** — using the technical skills from the opportunity roles, find all firm employees with those skills.

Then, for each proposed role assignment on the opportunity:
- **Get Employee Information** for each assigned employee
- **Get Employee Technical Skills** for each assigned employee
- **Get Employee Engagement Role Assignments** for each assigned employee (to surface relevant prior experience)

## Analysis before writing

Before drafting, reason through:

**Related engagements**: From the full engagement list, identify engagements that are relevant by:
- Similar engagement type (e.g. ERP implementation, data platform, staff augmentation)
- Overlapping required technical skills
- Same client or same industry vertical as this opportunity's client

Note the Project Manager and key roles for each related engagement.

**Firm-wide depth**: From the skills search results, count how many firm employees hold each required skill/cert. This is the "bench depth" narrative.

**Team assessment**: For each proposed team member, identify:
- Relevant certifications and technical skills
- Prior engagements that are similar to this opportunity (type, skills, industry)
- Whether they have worked directly with this client before

**DISC / soft skills**: Note each team member's DISC profile (if available in their profile data) and identify how the profiles are complementary.

> **Omission rule — apply throughout the entire document:**
> This is a client-facing document. If any area of analysis comes back weak or empty, **silently omit that section or bullet** rather than flagging the gap. Specifically:
> - If there are no related client engagements → omit the "Industry Experience" subsection entirely
> - If bench depth for a skill is thin (fewer than 3 employees) → omit that skill from the "Technical Depth" narrative
> - If a team member has no relevant prior engagements → omit their experience bullet, do not write "no relevant experience found"
> - If DISC profiles are unavailable or the mix does not tell a compelling story → omit the "Team Dynamics" paragraph
> - Never write phrases like "we have limited experience in…", "no prior engagements were found…", or any language that signals a gap to the client
> The goal is a document that leads with genuine strengths. Gaps belong in the internal game plan, not here.

## Document structure

Write the proposal with professional, client-facing tone. Do not include internal metrics (billing rates, DIR scores, financials).

---

### Executive Summary

2–3 paragraphs covering:
- What the client needs (restate the opportunity in client-friendly language)
- Why ProServe Metrics is uniquely positioned to deliver it (firm experience + team depth)
- The proposed team and timeline at a glance

---

### Experience & Qualifications

#### Firm Track Record
Highlight 3–5 of the most relevant completed or active engagements. For each:
- Client name and industry (use industry only if client confidentiality is preferred)
- Brief description of the work and outcomes
- Why it's relevant to this opportunity

#### Industry Experience
Note any engagements the firm has delivered for clients in the **same industry** as this client. If none, highlight the most adjacent industries.

#### Technical Depth Across the Firm
For each required skill/certification:
- Number of firm employees who hold it
- Any notable concentrations (e.g. "8 of our consultants hold AWS Solutions Architect certification")

Frame this as the support network behind the proposed team — our consultants are not working alone.

---

### Team & Timeline

#### Proposed Team

For each role:

**[Role Title] — [Employee Name]**
- *Certifications & Technical Skills*: list the most relevant to this engagement
- *Related Engagement Experience*: 2–3 bullet points on prior engagements most similar to this work
- *Client/Industry Experience*: note any direct experience with this client, or experience in the client's industry
- ⭐ *Has delivered for this client previously* — flag clearly if applicable

#### Team Strengths
Write 2–3 sentences on how the team's combined experience and skills are specifically suited for this engagement. Reference complementary skills across roles.

#### Team Dynamics
If DISC profiles are available: briefly describe the team's profile mix and what it means for client communication, delivery pace, and problem-solving style.

#### Proposed Timeline
Present a high-level milestone timeline based on the opportunity's proposed dates and engagement type. Use phases (e.g. Discovery, Design, Build, UAT, Go-Live) appropriate to the engagement type. Keep it high-level — this is a proposal, not a project plan.
