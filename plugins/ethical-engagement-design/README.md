# Ethical Engagement Design Plugin

Design player engagement through quality craft — pacing, novelty, goal structure, flow state, and dark pattern avoidance. Covers the session-level layer of the game design stack: keeping players coming back because the game is genuinely good, not because it manipulates them.

Based on proven design principles from Half-Life, Stardew Valley, Factorio, Celeste, Hades, and more.

## Skills

### ethical-engagement-design

Activates a game design advisor specializing in player engagement, session design, and ethical retention. Evaluates your systems against the core principle: **players should return because your game is genuinely good, not because it's engineered to make leaving feel bad.**

Use this when you're designing progression systems, debugging why players drop off, building pacing curves, tuning difficulty, or reviewing systems for dark patterns.

**How to invoke:**

```
Use the ethical-engagement-design skill

[describe your game, the system you're designing, or the retention problem you're seeing]
```

Or just describe the problem naturally — the skill triggers on any engagement or session design topic:

```
My game feels boring after the first hour. Players have seen everything and
there's nothing new to discover. How do I keep the experience feeling fresh?
```

**When it activates:**

The skill triggers on any of the following — including tangential phrasing:

- Pacing problems: "game drags," "act 2 is boring," "players quit in the middle," "too much combat in a row"
- Progression design: progression curves, reward loops, leveling systems, unlock pacing
- Retention concerns: "players aren't coming back," "session length," "daily active users"
- Difficulty design: dynamic difficulty, flow state, frustration, "game is too easy/hard"
- Dark pattern review: FOMO, loot boxes, login streaks, time gates, sunk cost traps
- Goal structure: "players feel directionless," "too many quests," "nothing to work toward"
- Novelty distribution: front-loading, mid-game content drought, "game runs out of ideas"

Applies to Unity, Unreal, Godot, and custom engines.

**The advisor evaluates your work against 4 pillars + an ethics check:**

1. **Master your pacing** — alternate tension and release across gameplay pillars; prevent pillar fatigue; build, peak, breathe
2. **Leverage novelty and mystery** — distribute new elements throughout the game, not just the tutorial; use foreshadowing to create forward pull
3. **Structure goals and progress** — always maintain short-term and long-term goals; use exponential growth curves so players look back and feel powerful
4. **Refine the challenge** — tune dynamic difficulty to keep players in flow state; make death productive, not punishing
+ **Ethics check** — flag FOMO, loss aversion, sunk cost exploitation, and variable-ratio reinforcement; always ask if the player would thank you for this system

**Bundled references:**

- `references/patterns.md` — Implementation patterns for 8 common engagement systems (pseudocode, engine-agnostic)

## Implementation Patterns

The skill references `references/patterns.md` when you need actual code or architecture guidance. It contains engine-agnostic pseudocode patterns for:

| Pattern | Problem It Solves |
| --- | --- |
| Pacing Manager | Game stays at one intensity level too long — exhaustion or boredom |
| Novelty Scheduler | Novelty front-loaded in tutorial; mid-game content drought |
| Foreshadowing System | Players don't feel pulled forward; nothing to wonder about |
| Goal Layer Manager | Players feel directionless (no goals) or overwhelmed (too many) |
| Exponential Growth Curve | Progression feels flat; player doesn't feel more powerful over time |
| Flow State Monitor | Can't tell if players are bored or frustrated; no DDA signal |
| Productive Failure System | Death feels like wasted time instead of a learning experience |
| Dark Pattern Detector | Engagement systems drift into manipulation during development |

## Design Principle Quick Reference

**The core rule:** Players should return to your game because it's genuinely good — not because leaving feels bad. Design engagement through craft, not compulsion.

**When reviewing engagement systems, check for:**

- Novelty front-loading — does the game introduce everything in hour one and coast?
- No forward pull — does the player have something to wonder about or look forward to?
- Sustained intensity — has there been a tension release in the last 5 minutes?
- Goal gaps — can the player always answer "what am I doing right now" and "what am I working toward"?
- Dark pattern drift — does any system create anxiety about *not* playing?

**When designing retention systems:**

Always ask: "Would the player thank me for this system if they understood exactly how it works?" If the answer is no — if it works by creating anxiety, exploiting loss aversion, or manufacturing obligation — redesign it. The goal is players who *want* to return, not players who feel they *have* to.

**When a playtest reveals drop-off:**

Don't patch the symptom — diagnose the layer:
1. Is the pacing wrong? (Pillar fatigue, sustained intensity, no recovery beats)
2. Is novelty exhausted? (No new enemies, mechanics, environments, or story beats)
3. Are goals unclear? (Player can't answer what to do next or what they're building toward)
4. Is challenge mistuned? (Player in frustrated or bored state, not flow)

## Pairs With

These plugins form a complete game design advisor stack:

| Plugin | Layer | Focus |
|---|---|---|
| **gamedev-mentor** | Entry Point | How to *surface* design problems across all domains |
| **ai-design** (ai-rubric) | Moment-to-moment | How enemies *behave* |
| **combat-design** | Moment-to-moment | How combat *feels* and plays |
| **player-protection-design** (player-protection) | Per-encounter | How mechanics *incentivize* the right playstyle |
| **ethical-engagement-design** | Session-level | How the experience *keeps players coming back* |
| **game-design-problem-solving** | Meta / Process | How to *diagnose and fix* when something goes wrong |
| **puzzle-design** | Content / Level | How to *craft puzzles* that produce eureka moments |

## Installation

```bash
/plugin marketplace add sharkjets/claude-plugins
/plugin install ethical-engagement-design@sharkjets
```

## Updating

```bash
/plugin update ethical-engagement-design@sharkjets
```

## Version

1.0.0
