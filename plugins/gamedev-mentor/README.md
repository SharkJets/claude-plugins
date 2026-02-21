# gamedev-mentor Plugin

A higher-level entry point into game design mentorship. Rather than activating when you're already working in a specific domain, this plugin surfaces design problems you may not know you have — through codebase review or structured consultation.

## Skills

### mentor-review

Reads your game codebase and produces a structured design audit. Identifies which design domains are relevant to your game type, reads key files, flags violations against domain principles, and delivers a report with file:line references and prioritized recommendations.

**Use when:** You want an objective audit of your existing project — "what design problems does my code reveal?"

**How to invoke:**

```
/mentor-review path/to/your/project
```

Or describe the task naturally:

```
Review my game project at src/ for design problems
```

**What it audits:**

| Domain | What it looks for |
|---|---|
| AI Behavior | Telegraph gaps, player-favoring biases, consistency, world integration |
| Combat Feel | Hit stop, animation phases, defensive options, enemy readability, stunlock |
| Player Incentives | Optimal vs. intended behavior, push-forward design, reward timing |
| Engagement & Pacing | Novelty curve, session loop structure, return motivation, dark patterns |
| Puzzle Design | Catch-first structure, hint availability, misdirection, failure feedback |

**Report format:**

- Executive summary with top 3 findings
- Domain-by-domain findings with severity (CRITICAL / NOTABLE / MINOR) and file:line references
- Recommended next steps
- Specialist plugin recommendations for deep implementation help

---

### mentor-consult

Runs a structured intake interview, then diagnoses your top design problems with expert analysis. No code required — works from your description of the game.

**Use when:** You want guided feedback on your design without sharing code, or you're not sure what's wrong and need help figuring it out.

**How to invoke:**

```
/mentor-consult
```

Or describe the need naturally:

```
Help me figure out what's wrong with my game's design
```

**How it works:**

1. **Intake** — Four questions asked together: game type, core loop, biggest problem, what success feels like
2. **Domain probes** — Targeted follow-up questions for domains relevant to your game type (skips irrelevant domains)
3. **Diagnosis** — Top 3–5 design problems, each mapped to the principle it violates and explained in terms of player-facing impact
4. **Recommendations** — Concrete, specific actions with real-game examples; closes with specialist plugin suggestions

---

## Installation

```bash
/plugin marketplace add sharkjets/claude-plugins
/plugin install gamedev-mentor@sharkjets
```

## Updating

```bash
/plugin marketplace update .
```



## Specialist Plugins

This plugin surfaces problems and points you toward solutions. For deep implementation help, install the relevant specialist:

| Plugin | Domain |
|---|---|
| `ai-rubric@sharkjets` | Enemy AI, NPC logic, behavior trees, dynamic difficulty |
| `combat-design@sharkjets` | Melee combat, hit stop, animation phases, game feel |
| `player-protection@sharkjets` | Player incentives, reward systems, push-forward design |
| `ethical-engagement-design@sharkjets` | Pacing, novelty, flow state, dark pattern avoidance |
| `puzzle-design@sharkjets` | Puzzle structure, hint systems, difficulty curves |
| `game-design-problem-solving@sharkjets` | Root cause analysis, playtesting methodology |

## Version

1.0.0
