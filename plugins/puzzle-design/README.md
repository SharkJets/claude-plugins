# Puzzle Design — Claude Code Skill

A Claude Code skill that helps game developers design puzzles that make players feel smart — with structured methodology for crafting catches, revelations, misdirection, difficulty curves, and feedback.

Based on proven design principles from Portal, Baba Is You, The Witness, Zelda, Stephen's Sausage Roll, and more.

## What It Does

When you ask Claude Code for help with puzzle design, this skill activates and ensures Claude:

- Structures puzzles around a **catch** (logical contradiction) and **revelation** (eureka moment)
- Designs **misdirection** that guides players through a productive wrong answer first
- Audits puzzles for **minimalism** — removes elements that don't serve the catch or revelation
- Builds **feedback systems** so players fight the puzzle logic, not the interface
- **Quantifies difficulty** across 4 dimensions for systematic ordering
- Plans **difficulty curves** with vertical building and rest puzzles
- Runs **playtesting protocols** that distinguish "smart" from "tricked"
- Works with **any engine** — Unity, Unreal, Godot, or custom

## Installation

```bash
/plugin marketplace add sharkjets/claude-plugins
/plugin install puzzle-design@sharkjets
```

## Updating

```bash
/plugin marketplace update .
```



## Usage

Claude Code auto-detects and uses the skill. Just ask naturally:

- "Design a puzzle using my portal mechanic"
- "Players keep getting stuck on level 7"
- "How do I order these 20 puzzles into a good curve?"
- "This puzzle feels unfair, help me fix it"
- "I have a gravity-flip mechanic, what puzzles can I make?"
- "How do I teach this mechanic without a tutorial popup?"

Or invoke directly: `/puzzle-design`

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
| `SKILL.md` | Core skill — 6 design principles + application guidance |
| `references/frameworks.md` | 8 frameworks: construction process, catch/revelation templates, misdirection patterns, minimalism audit, feedback checklist, difficulty quantification, curve planner, playtesting protocol |

## Principles Covered

1. Establish your foundation (ironclad rules, clear goals)
2. Design the catch and revelation (logical contradiction → eureka)
3. Use misdirection and assumptions (productive wrong answers)
4. Refine presentation and feedback (minimalism, clarity, failure legibility)
5. Structure the difficulty curve (vertical building, quantified ordering)
6. Iterate and test (observation-based playtesting, "smart" vs "tricked")
