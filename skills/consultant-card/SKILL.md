---
description: Generate a rich HTML consultant card artifact for a given employee — matching the visual design of the Concierge web app card. Renders inline in the Claude artifacts panel.
argument-hint: employee name or ID
---

# Consultant Card

Generate a rich HTML consultant card for **$ARGUMENTS** and render it as an artifact.

## Step 1 — Resolve the employee

If `$ARGUMENTS` looks like an ID (starts with a digit or is a UUID), use it directly.
Otherwise use **Search Employees by Name** with `$ARGUMENTS` to find the employee.
If multiple results are returned, ask the user to clarify before proceeding.

## Step 2 — Fetch all card data in parallel

Call ALL of the following simultaneously:

1. **Get Employee Information** — full profile: name, title, manager, department, email, phone, discProfile (D/I/S/C scores), strengths (ranked), currentRoleAssignments
2. **Get Employee Technical Skills** — technicalSkillName + totalSkillScore, sorted desc; take top 4
3. **Get Employee DIR Score** — OperationalScore, CompleteScore, OperationalConfidence

## Step 3 — Render the card as an HTML artifact

Using the data collected, produce a **single self-contained HTML document** (no external deps) that precisely mirrors the visual design below, then output it wrapped in a fenced `html` code block so Claude Desktop renders it as an artifact.

---

### Visual design spec

**Overall card**: dark background `#1a1a2e`, rounded corners 8px, box-shadow, width 340px, font-family Inter/system-ui, color `#e0e0e0`.

---

#### Tile 1 — Profile header

```
┌──────────────────────────────────┐
│  [Avatar]  Full Name             │  ← name in white, 16px bold
│  [50×50]   Title                 │  ← subtitle, #a0a0b0, 12px
│  orange    Manager: [name]       │  ← 11px
│  initials  Department            │  ← italic, 11px
│                     [✉][📞][💬]  │  ← 24px icon buttons, right-aligned
└──────────────────────────────────┘
```

- Avatar: 50×50 circle, background `#f57c00` (orange), white initials (first + last initial), 16px bold
- Contact icons: email → `mailto:`, phone → `tel:`, Teams → `https://teams.microsoft.com/l/chat/0/0?users={email}`
- Divider line below this section

---

#### Tile 2 — Performance & Capacity

Section label `PERFORMANCE & CAPACITY` in `#7a7aaa`, 10px, uppercase, letter-spacing 0.5px.

**DIR Score row** (left: label, right: score badge):
- Label: "DIR Score" 12px
- Badge: rounded pill, score as integer. Color by value:
  - ≥ 80 → green `#2e7d32` bg, `#a5d6a7` text
  - ≥ 60 → orange `#e65100` bg, `#ffcc80` text
  - < 60 → red `#b71c1c` bg, `#ef9a9a` text

**CSOT utilization** (if capacity data available, otherwise omit):
Show three rows for 4-week / 8-week / 12-week utilization as labeled progress bars.
Bar fill color: green if ≤ 80%, orange if ≤ 100%, red if > 100%.

---

#### Tile 3 — Active Engagements

Section label `ACTIVE ENGAGEMENTS` styled same as above.

If no active engagements: show "No active engagements" in muted text.

Otherwise show a compact table:
| Client | Project | Role | End Date |
Each row 11px, alternating row bg `#16213e` / transparent.

---

#### Tile 4 — Skills (two columns)

Left column — `TECH SKILLS`:
Each skill on one row: skill name (11px uppercase, truncate) on left, score badge on right.
Score badge: 24×24 rounded square. Color:
- ≥ 8 → green
- ≥ 6 → blue `#1565c0` bg, `#90caf9` text
- ≥ 4 → orange
- < 4 → red
Show top 4 skills by score.

Right column — `SOFT SKILLS` (use these fixed values):
- Team Work: 9
- Communication: 5
- Flexibility: 6
- Presentation: 8
Each row: skill name + thin horizontal progress bar (0–10 scale), bar color matching score thresholds above.

Vertical divider between columns.

---

#### Tile 5 — Strengths & DISC (two columns, footer)

Left column — `TOP STRENGTHS`:
List top 4 strengths by rank (person.strengths sorted by strengthRank ascending).
Each strength: small colored dot + name, 11px. Cycle dot colors: `#1976d2`, `#388e3c`, `#f57c00`, `#7b1fa2`.

Right column — `DISC PROFILE`:
Four vertical bars (D / I / S / C) displayed as a mini bar chart.
Bar heights proportional to scores (max bar height 80px).
Colors: D=`#1976d2`, I=`#0288d1`, S=`#ed6c02`, C=`#d32f2f`.
Score label below each bar initial.

---

## Step 4 — Output

Output the HTML in a fenced `html` code block. The document must be fully self-contained (no CDN, no external fonts — use system-ui). Include a small watermark at the bottom: "Concierge · ProServe Metrics" in 9px muted text.

After the artifact, write one sentence confirming who the card is for and offering to generate cards for additional team members.
