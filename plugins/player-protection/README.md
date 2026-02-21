# Player Protection Plugin

Design game mechanics that align player incentives with intended gameplay. Covers reward systems, push-forward mechanics, grading/scoring, soft discouragement, and progression design — for any engine and any genre.

## Skills

### player-protection-design

Activates a game design advisor specializing in player psychology and incentive design. Evaluates your systems against the core principle: **players will optimize the fun out of your game if you let them.**

Use this when you're designing reward systems, debugging why players are turtling or cheesing, building scoring/grading systems, or aligning "effective" with "fun."

**How to invoke:**

```
Use the player-protection-design skill

[describe your game system, share code, or explain the problem]
```

Or just describe the problem naturally — the skill triggers on any player incentive topic:

```
Players just crouch behind cover and headshot everything. Combat feels boring.
How do I make aggressive play feel better without punishing cautious players?
```

**When it activates:**

The skill triggers on any of the following — including tangential phrasing:

- Designing or reviewing: reward systems, difficulty, progression, scoring, resource loops
- Player behavior problems: "players cheese," "players turtle," "players exploit," "players grind"
- Incentive questions: "how to encourage aggression," "reward vs punish," "push-forward combat"
- Grading systems: style meters, silent assassin rankings, par times, post-level breakdowns
- Pacing problems, session length limits, anti-farming mechanics

Applies to Unity, Unreal, Godot, and custom engines.

**The advisor evaluates your work against 7 principles:**

1. **Identify unfun optimal strategies** — diagnose the gap between "what wins" and "what's fun"
2. **Choose positive reinforcement over punishment** — flip every penalty into a reward for the opposite behavior
3. **Implement push-forward mechanics** — tie survival resources to the behavior you want to encourage
4. **Use abstract grading and scoring systems** — reward the intended playstyle through prestige, not force
5. **Signal intent through system depth** — complexity allocation tells players what matters
6. **Soft discouragement via environmental response** — dynamic world reactions, never explicit penalties
7. **Avoid the hard fail trap** — make the intended playstyle feel like the player's idea, not yours

**Bundled references:**

- `references/patterns.md` — Implementation patterns for 8 common incentive systems (pseudocode, engine-agnostic)

## Implementation Patterns

The skill references `references/patterns.md` when you need actual code or architecture guidance. It contains engine-agnostic pseudocode patterns for:

| Pattern | Problem It Solves |
| --- | --- |
| Push-Forward Health Loop | Players hide behind cover instead of engaging (Doom/Bloodborne model) |
| Push-Forward Resource Loop | Players hoard resources and never use them (Hyper Light Drifter model) |
| Rest Bonus / Positive Reframe | You need to limit a behavior but penalties feel punitive (WoW model) |
| Style Meter / Combo Grading | Players spam one effective move; combat becomes monotonous (DMC model) |
| Post-Level Grading Breakdown | Players don't know what they missed or how to improve (Hitman model) |
| Soft Discouragement Manager | Players camp or turtle in static positions |
| Dominant Strategy Detector | Dev-time tool to detect when one strategy crowds out all others |
| Fragility-Based Stealth Incentive | Players go loud in a stealth game because combat is available (Arkham model) |

## Design Principle Quick Reference

**The core rule:** Players will optimize the fun out of your game if you let them. Your job is to make the most effective strategy and the most fun strategy the same thing.

**When reviewing reward/penalty code, check for:**

- Penalties that could be reframed as rewards for the opposite behavior
- Resources that incentivize the wrong playstyle (health from cover → encourages camping)
- Hard fail states that could be replaced with soft discouragement
- Grading that rewards the wrong behaviors (or doesn't reward the right ones)
- System depth mismatches — deep combat in a stealth game signals "go loud"

**When writing reward systems:**

Always ask: "Can I flip this penalty into a reward for the opposite behavior?" Grade on the behaviors you want to see, make the grade visible during play, and never make it a gate — it's a carrot, not a stick.

**When a playtest reveals degenerate strategies:**

Don't patch the symptom — diagnose the incentive. Ask:
1. What is the player actually optimizing for?
2. Why is that optimization more attractive than the intended path?
3. Which principle can realign the incentives?

## Installation

```bash
/plugin marketplace add sharkjets/claude-plugins
/plugin install player-protection@sharkjets
```

## Updating

```bash
/plugin update player-protection@sharkjets
```

## Version

1.0.0
