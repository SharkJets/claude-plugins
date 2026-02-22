# gamedev-mentor

Complete game design skill suite for Claude Code. One install gives you a senior game design mentor plus six specialist skills covering every major domain of game design.

[![What If GMTK Could Review YOUR Game? (Claude Code Plugin)](https://img.youtube.com/vi/3K6Kg4P5PqA/0.jpg)](https://www.youtube.com/watch?v=3K6Kg4P5PqA)

## Install

```
/plugin marketplace add sharkjets/claude-plugins
/plugin install gamedev-mentor@sharkjets
```

## Updating

```bash
/plugin marketplace update .
```

---

## Skills

### Entry Points

Start here if you're unsure what you need.

**`/mentor-review [path/to/project]`**

Reads your actual game code and audits it against design principles across all domains. Produces severity-ranked findings (CRITICAL / NOTABLE / MINOR) with file:line references and prioritized next steps.

```
/mentor-review src/
```

| Domain | What it looks for |
|---|---|
| AI Behavior | Telegraph gaps, player-favoring biases, consistency, world integration |
| Combat Feel | Hit stop, animation phases, defensive options, enemy readability, stunlock |
| Player Incentives | Optimal vs. intended behavior, push-forward design, reward timing |
| Engagement & Pacing | Novelty curve, session loop structure, return motivation, dark patterns |
| Puzzle Design | Catch-first structure, hint availability, misdirection, failure feedback |

---

**`/mentor-consult`**

Structured interview — no code required. Diagnoses design problems through conversation. Identifies root causes, maps them to principles, gives concrete recommendations with real-game examples.

```
/mentor-consult
```

How it works:
1. **Intake** — Four questions: game type, core loop, biggest problem, what success feels like
2. **Domain probes** — Targeted follow-ups for relevant domains only
3. **Diagnosis** — Top 3–5 problems mapped to violated principles and player-facing impact
4. **Recommendations** — Specific, actionable changes with real-game examples

---

### Specialist Skills

Use these for deep implementation guidance after the mentor identifies problem areas.

**`/ai-design`**
Enemy behaviors, NPC logic, behavior trees, dynamic difficulty, telegraphing, attacker limits, player-favoring biases.

**`/combat-design`**
Melee combat feel, attack animation phases (startup / active / recovery), hit stop, screen shake, defensive mechanics, combo structure.

**`/player-protection-design`**
Player incentives, reward timing, push-forward mechanics, optimal vs. intended strategy alignment, soft discouragement.

**`/ethical-engagement-design`**
Pacing, novelty curves, session loop structure (30s / 5min / 30min), flow state, onboarding, dark pattern avoidance.

**`/game-design-problem-solving`**
Root cause analysis, lever management, creative reframing, second-order effects, playtesting methodology.

**`/puzzle-design`**
Catch and revelation structure, hint systems, misdirection, minimalism, failure feedback, difficulty curves.

---

## Workflow

1. Start with `/mentor-review` (if you have code) or `/mentor-consult` (if you want a conversation).
2. The mentor identifies which domains have problems.
3. Follow up with the relevant specialist skill for deep implementation guidance.

## Sources

Skills in this plugin are informed by [Game Maker's Toolkit](https://www.youtube.com/@GMTK) (Mark Brown):

| Skill | Video |
|---|---|
| `/player-protection-design` | [How Game Designers Protect Players From Themselves](https://www.youtube.com/watch?v=7L8vAGGitr8) |
| `/ai-design` | [What Makes Good AI?](https://www.youtube.com/watch?v=9bbhJi0NBkk) |
| `/combat-design` | [What Makes a Good Combat System?](https://www.youtube.com/watch?v=8X4fx-YncqA&t=3s) |
| `/ethical-engagement-design` | [How to Keep Players Engaged (Without Being Evil)](https://www.youtube.com/watch?v=hbzGO_Qonu0) |
| `/game-design-problem-solving` | [How Game Designers Solved These 11 Problems](https://www.youtube.com/watch?v=rJZyPdYIbZI) |
| `/puzzle-design` | [What Makes a Good Puzzle?](https://www.youtube.com/watch?v=zsjC6fa_YBg) |

## Version

1.0.0
