# Combat Design — Claude Code Skill

A Claude Code skill that helps game developers build melee combat systems that feel satisfying — with structured methodology for attack design, defensive mechanics, game feel/juice, enemy telegraphs, and style scoring.

Based on proven design principles from Dark Souls, Bayonetta, Devil May Cry, Bloodborne, Sekiro, Batman: Arkham, God of War, and more.

## What It Does

When you ask Claude Code for help with combat design, this skill activates and ensures Claude:

- Structures attacks around **three animation phases** (anticipation/contact/recovery) with correct timing
- Implements **hit stop, screen shake, and juice layers** that create visceral impact
- Designs **attack trade-off matrices** so every move has a unique niche
- Balances **block vs. dodge vs. parry** as situationally distinct choices
- Implements **enemy poise/super armor** to prevent stunlock and create combat rhythm
- Calibrates **target stickiness** to match your game's intended feel (Arkham → Souls spectrum)
- Builds **style scoring** that rewards variety and risk-taking
- Designs **enemy telegraphs** with correct timing per threat level
- Works with **any engine** — Unity, Unreal, Godot, or custom

## Installation

```bash
/plugin marketplace add sharkjets/claude-plugins
/plugin install combat-design@sharkjets
```

## Updating

```bash
/plugin update combat-design@sharkjets
```

## Usage

Claude Code auto-detects and uses the skill. Just ask naturally:

- "Combat feels floaty, what's missing?"
- "Design a moveset with 5 distinct attacks"
- "Implement hit stop in my combat system"
- "Balance parry so it's not the only option"
- "Enemies get stunlocked too easily"
- "Build a Souls-like combat system"
- "Add juice to my attack animations"

Or invoke directly: `/combat-design`

## The Full Stack

| Plugin | Layer | Focus |
|---|---|---|
| **gamedev-mentor** | Entry Point | How to *surface* design problems across all domains |
| **ai-design** (ai-rubric) | Moment-to-moment | How enemies *behave* |
| **combat-design** | Moment-to-moment | How combat *feels* and plays |
| **player-protection-design** (player-protection) | Per-encounter | How mechanics *incentivize* fun play |
| **ethical-engagement-design** | Session-level | How the experience *sustains engagement* |
| **game-design-problem-solving** | Meta / Process | How to *diagnose and fix* design issues |
| **puzzle-design** | Content / Level | How to *craft puzzles* that produce eureka moments |

## Files

| File | Purpose |
|------|---------|
| `SKILL.md` | Core skill — 5 design pillars + application guidance |
| `references/patterns.md` | 8 patterns: animation phases, hit stop/juice, attack matrix, defensive tuning, poise, stickiness, style scoring, enemy telegraphs |

## Principles Covered

1. Attack fundamentals and animation (phases, canceling, stickiness)
2. Defensive mechanics (block/dodge/parry triangle, cost differentiation)
3. Enemy design and AI (visual clarity, tells, soft constraints, poise)
4. Game feel and impact (hit stop, snap, juice layers, audio)
5. Player motivation and mastery (style scoring, risk incentives, adaptation)
