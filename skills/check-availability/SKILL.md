---
name: check-availability
description: Check consultant availability for a time period. Shows who is free and when, or checks when a specific person can start. Use when you need to understand capacity before committing someone to a role.
argument-hint: [name or skill] [start date] [weeks]
when_to_use: when is available, who is free, check availability, can someone start, capacity
---

# Check Availability

Check consultant availability for a period.

## Instructions

**Parse `$ARGUMENTS` for:**
- A person's name OR a skill/role description
- Start date (if provided; default to today)
- Duration in weeks (if provided; default to 4 weeks)

---

### If checking a specific person

1. Resolve to employee ID if needed (call `list_employees` or `query_data_model` with their name)
2. Call `get_employee_availability_for_period` with their ID and the target period
3. Show:
   - Current engagement (if any) and end date
   - Hours per week available during the target period
   - Next fully available date

---

### If looking for available people with a skill

1. Call `list_employees_with_technical_skills_by_skill_name` with the skill names
2. Filter results to employees with meaningful availability (> 20 hrs/week) in the target period
3. Show top 5 most available:

| Name | Available Hours/Week | Next Fully Available | Key Skills |
|------|---------------------|---------------------|------------|
| ... | ... | ... | ... |

---

If no one is immediately available for the full role hours, show who is partially available and when they open up.
