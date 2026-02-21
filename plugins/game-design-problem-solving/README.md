# Game Design Problem-Solving Plugin

Systematically diagnose and fix game design problems. Covers root cause analysis, lever identification, creative reframing, second-order effect tracing, and playtesting methodology — for any engine and any genre.

Based on proven methodology from Bungie (Halo), Epic (Gears of War), Yacht Club (Shovel Knight), Ubisoft (Rainbow Six Siege), and Nintendo.

## Skills

### game-design-problem-solving

Activates a game design problem-solving advisor that helps you find root causes before jumping to solutions. Evaluates your thinking against the core principle: **don't fix the symptom — fix the cause.**

Use this when something in your game feels wrong but you're not sure why, when balance changes keep creating new problems, when playtests surface complaints you can't diagnose, or when you're stuck making 5% tweaks that never quite fix anything.

**How to invoke:**

```
Use the game-design-problem-solving skill

[describe the problem, complaint, or symptom — share code, design docs, or just explain what players are saying]
```

Or just describe the problem naturally — the skill triggers on any game design diagnosis or iteration topic:

```
Players say the sniper is overpowered but every time I nerf it I break
something else. I've been tuning it for two weeks and nothing feels right.
```

**When it activates:**

The skill triggers on any of the following — including tangential phrasing:

- Balance problems: "weapon is OP," "ability is broken," "nerf/buff," "players complain about X"
- Feel problems: "doesn't feel right," "feels clunky," "feels punitive," "feels unfair"
- Iteration problems: "I've been tweaking this for weeks," "nothing is working," "every fix breaks something"
- Playtesting problems: "players keep doing the wrong thing," "playtesters all got stuck at the same point"
- Scope/variety problems: "I need more content but I'm out of budget," "the mid-game is empty"
- Design methodology: "how do I balance," "how do I know if my fix worked," "how do I run a playtest"

Applies to Unity, Unreal, Godot, and custom engines.

**The advisor applies 5-step methodology:**

1. **Identify the root cause** — ask "why" three times before proposing any solution; align on problem scope before debating fixes
2. **Map and pull the right levers** — separate identity levers (off-limits) from secondary levers (safe to adjust); pull hard, not cautiously
3. **Reframe creatively** — flip penalties into risk/reward choices; pull systems out of menus and into the world; find solutions that solve multiple problems at once
4. **Design for how players actually behave** — use invisible assistance to bridge the skill gap; trace second-order effects before shipping any change
5. **Validate without bias** — blind playtests with identical instructions; read behavior before asking questions; distinguish invisible fixes from overcorrections

**Bundled references:**

- `references/methodology.md` — 8 structured frameworks with worksheets and worked examples (pseudocode, engine-agnostic)

## Frameworks

The skill references `references/methodology.md` when you need a structured approach or concrete template. It contains:

| Framework | Use When |
| --- | --- |
| Root Cause Analysis | A complaint has surfaced and you need to find what's actually wrong before proposing anything |
| Lever Identification Worksheet | You know the problem and need to figure out what you can safely change without destroying identity |
| Dramatic Iteration Protocol | You're stuck in the 5% tweak trap and can't tell if you're heading in the right direction |
| Reframing Toolkit | The obvious solution feels punitive, unintuitive, or only solves one thing |
| Second-Order Effect Mapper | You're about to ship a balance change and need to trace ripple effects first |
| Hidden Buff Designer | Playtest data shows beginners struggling while experts are fine |
| Constraint-Driven Design Generator | You need more content or variety but have limited art, engineering, or time budget |
| Blind Playtest Protocol | You've implemented a fix and need to verify it actually worked |

## Design Principle Quick Reference

**The core rule:** Don't fix the symptom — fix the cause. When players say "X feels bad," that's a symptom. Proposing solutions before diagnosing causes is how balance changes break five things while fixing one.

**When reviewing a balance complaint, check:**

- Is this a system issue (the economy is broken) or a feel issue (this one attack doesn't feel good)? These need completely different solutions.
- Are you touching an identity lever (what makes this thing *this thing*) or a secondary lever (adjustable without losing identity)?
- Have you traced the second-order effects? A weapon nerf can shift the meta, unbalance team compositions, and invalidate a quest reward.
- Can you reframe the solution — flip a penalty into a risk/reward choice, or pull it out of a menu and into the world?

**When playtests reveal a recurring problem:**

1. Restate the problem as you understand it — get agreement before proposing anything
2. Ask "why" at least three times to reach something actionable
3. Map the levers: what's off-limits (identity), what's safe (secondary), what's in adjacent systems (contextual)
4. Make a dramatic change first — halve it or double it — to confirm you're pulling the right lever
5. Blind-playtest the fix; if testers don't mention the change, it worked

**When scope or variety is the problem:**

Constraints are creative forcing functions. Invisible enemies cost zero art budget. Reskinned hybrids reuse existing animations with new behavior trees. Level remixes (backward, night variant, flooded, timed) cost a fraction of new levels. The answer to "we can't afford more content" is almost always a combination pattern, not a budget increase.

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
/plugin install game-design-problem-solving@sharkjets
```

## Updating

```bash
/plugin update game-design-problem-solving@sharkjets
```

## Version

1.0.0
