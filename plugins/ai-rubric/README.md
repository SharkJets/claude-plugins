# AI Rubric Plugin

Design, implement, and review game AI systems against a principled rubric. Covers enemy behaviors, NPC logic, companion AI, stealth systems, behavior trees, dynamic difficulty, and more — for any engine and any genre.

## Skills

### ai-design

Activates a game AI design advisor that evaluates your work against 10 gameplay-first principles. Covers the full range of game AI problems: from high-level architecture to concrete implementation patterns.

Use this when you're designing a new AI system, reviewing existing behavior, debugging why enemies feel cheap or dumb, or brainstorming how to make encounters more satisfying.

**How to invoke:**

```
Use the ai-design skill

[describe your AI system, share code, or just explain the problem]
```

Or just describe the problem naturally — the skill triggers on any game AI topic:

```
My enemies feel too easy to cheese. Players just crouch in a corner and
headshot everyone. How do I fix this without making the game unfair?
```

**When it activates:**

The skill triggers on any of the following — including tangential phrasing:

- Designing or implementing: enemy AI, NPC logic, companion AI, stealth systems, combat AI
- Architecture: behavior trees, state machines, GOAP planners, utility AI
- Systems: dynamic difficulty, AI directors, faction systems, creature ecosystems
- Player feedback: "enemies feel dumb," "players are cheesing my AI," "guards don't react to noise"
- Friendlies: companion behavior, ally pathfinding, squad coordination

Applies to Unity, Unreal, Godot, and custom engines.

**The advisor evaluates your work against 10 principles:**

1. **Align aggressiveness with the core experience** — AI intensity should reinforce the game's fantasy, not fight it
2. **Let the player "cheat" subtly** — grace periods, attacker limits, near-miss mechanics
3. **Telegraph AI intentions clearly** — barks, animations, UI indicators before every significant action
4. **Prioritize consistency over unpredictability** — deterministic triggers, varied execution
5. **Integrate AI with broader game systems** — enemies pick up weapons, use hazards, react to the world
6. **React and adapt to player habits** — counter dominant strategies visibly, not punitively
7. **Manage pacing and tension dynamically** — waves of intensity and release, not constant pressure
8. **Give AI independent goals** — schedules, factions, survival needs; not just "find and kill player"
9. **Design better friendlies** — narrative enhancement over combat effectiveness; never babysitting
10. **Remember: AI is a design tool** — always ask if the change makes encounters more fun

**Bundled references:**

- `references/patterns.md` — Implementation patterns for 8 common AI systems (pseudocode, engine-agnostic)

## Implementation Patterns

The skill references `references/patterns.md` when you need actual code or architecture guidance. It contains engine-agnostic pseudocode patterns for:

| Pattern | Problem It Solves |
| --- | --- |
| Simultaneous Attacker Limiter | Players feel overwhelmed unfairly when too many enemies engage at once |
| Detection Grace Period | Instant detection feels cheap; player has no time to react |
| AI Bark / Telegraph System | AI makes smart decisions but the player never knows it |
| Player Habit Tracker | One dominant strategy trivializes the game |
| Dynamic Tension Director | Constant intensity leads to fatigue, not excitement |
| Consistent-but-Varied Response | Predictable AI is exploitable; random AI is unfair |
| Independent AI Schedule System | Enemies feel like they exist only to fight the player |
| Behavior Tree Template | Minimal, well-structured BT skeleton incorporating all principles |

## Design Principle Quick Reference

**The core rule:** A game enemy's real job is not to kill the player. It is to present interesting gameplay.

**When reviewing AI code, check for:**

- Missing telegraphs — does the player see the AI's decision before it lands?
- Missing player-favoring biases — grace periods, attacker limits, near-miss shots?
- Overly random behaviors — can the player form a mental model and make a plan?
- AI that ignores the world — does it interact with pickups, hazards, environment?
- No pacing modulation — does intensity ever release, or is it constant pressure?

**When writing AI code:**

Include design-aware comments — not just what the code does, but why it exists from a gameplay perspective. Always include a TODO for the telegraph/feedback layer; it's the most commonly forgotten piece.

**When brainstorming:**

Start from the player experience and work backward:
1. What should this encounter **feel** like?
2. What AI behaviors create that feeling?
3. What systems support those behaviors?
4. What technical implementation do those systems need?

## Installation

```bash
/plugin marketplace add sharkjets/claude-plugins
/plugin install ai-rubric@sharkjets
```

## Version

1.0.0
