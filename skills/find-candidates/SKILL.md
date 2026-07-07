---
name: find-candidates
description: Find top candidates for a specific role by skills, availability, or other criteria. Use when searching for people for a role without running the full staffing workflow.
argument-hint: [skill keywords or role description]
when_to_use: user asks who is available, who has a skill, find me someone for, who can fill this role
---

# Find Candidates

Search for top candidates for a specific role.

## Instructions

**Parse `$ARGUMENTS` for:**
- Role name or description
- Required technical skills (comma-separated)
- Any constraints: availability date, seniority level, specific team

If arguments are missing or ambiguous, ask one concise clarifying question.

**Search in at most 3 MCP calls:**

1. **Find by skills** — call `list_employees_with_technical_skills_by_skill_name` once with ALL required skill names as an array (lowercase):
   ```
   {skillNames: ["python", "machine learning", "aws"]}
   ```

2. **Get full details** — from the results, take the top 8 by skill match score and call `list_employees` with their IDs in one batch call.

3. **Filter and rank:**
   - Exclude any employee with status = TERMINATED
   - Score by: technical match (skills hit / skills required) + availability
   - Pick top 5

**Never call individual employee lookups in a loop.** Batch always.

**Output:**

| Rank | Name | Skills Match | Available | Notes |
|------|------|-------------|-----------|-------|
| 1 | ... | 5/5 required | Immediate | ... |
| 2 | ... | 4/5 required | Aug 1 | ... |

Include a 1-2 sentence justification for the top pick. If fewer than 3 candidates meet the criteria, say so and offer alternatives (relax constraints or suggest related skills).
