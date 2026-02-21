# Implementation Spec: gamedev-mentor Consolidated Plugin

**Contract**: ./contract.md
**Estimated Effort**: S

## Technical Approach

This is a pure file restructuring task — no code, no build step, no dependencies. The goal is to copy the six specialist skill directories into `plugins/gamedev-mentor/skills/`, then update a handful of files to reflect the consolidated structure.

The Claude Code plugin system auto-discovers skills by directory structure: any subdirectory under `skills/` containing a `SKILL.md` becomes an available skill. No `plugin.json` registration of individual skills is required — the file just carries plugin-level metadata. So copying the SKILL.md files and their `references/` subdirectories is sufficient to make the skills available.

The two mentor SKILL.md files currently end with "Specialist Plugin Recommendations" sections that tell the user to install external plugins. Those sections are replaced with a note pointing to the bundled skills instead.

## Feedback Strategy

**Inner-loop command**: `ls plugins/gamedev-mentor/skills/`

**Playground**: CLI — verify directory structure at each step.

**Why this approach**: This is a file copy + edit task. There's no runtime to test. The fastest validation is listing the skills directory to confirm files are in place, then spot-checking SKILL.md edits with a quick read.

## File Changes

### New Files

| File Path | Purpose |
|-----------|---------|
| `plugins/gamedev-mentor/skills/ai-design/SKILL.md` | Copied from `plugins/ai-rubric/skills/ai-design/SKILL.md` |
| `plugins/gamedev-mentor/skills/ai-design/references/patterns.md` | Copied from `plugins/ai-rubric/skills/ai-design/references/patterns.md` |
| `plugins/gamedev-mentor/skills/combat-design/SKILL.md` | Copied from `plugins/combat-design/skills/combat-design/SKILL.md` |
| `plugins/gamedev-mentor/skills/combat-design/references/patterns.md` | Copied from `plugins/combat-design/skills/combat-design/references/patterns.md` |
| `plugins/gamedev-mentor/skills/player-protection-design/SKILL.md` | Copied from `plugins/player-protection/skills/player-protection-design/SKILL.md` |
| `plugins/gamedev-mentor/skills/player-protection-design/references/patterns.md` | Copied from `plugins/player-protection/skills/player-protection-design/references/patterns.md` |
| `plugins/gamedev-mentor/skills/ethical-engagement-design/SKILL.md` | Copied from `plugins/ethical-engagement-design/skills/ethical-engagement-design/SKILL.md` |
| `plugins/gamedev-mentor/skills/ethical-engagement-design/references/patterns.md` | Copied from `plugins/ethical-engagement-design/skills/ethical-engagement-design/references/patterns.md` |
| `plugins/gamedev-mentor/skills/game-design-problem-solving/SKILL.md` | Copied from `plugins/game-design-problem-solving/skills/game-design-problem-solving/SKILL.md` |
| `plugins/gamedev-mentor/skills/game-design-problem-solving/references/methodology.md` | Copied from `plugins/game-design-problem-solving/skills/game-design-problem-solving/references/methodology.md` |
| `plugins/gamedev-mentor/skills/puzzle-design/SKILL.md` | Copied from `plugins/puzzle-design/skills/puzzle-design/SKILL.md` |
| `plugins/gamedev-mentor/skills/puzzle-design/references/frameworks.md` | Copied from `plugins/puzzle-design/skills/puzzle-design/references/frameworks.md` |

### Modified Files

| File Path | Changes |
|-----------|---------|
| `plugins/gamedev-mentor/.claude-plugin/plugin.json` | Update description to reflect the full skill suite |
| `plugins/gamedev-mentor/skills/mentor-review/SKILL.md` | Replace "Specialist Plugin Recommendations" section (lines 160–171) with a bundled skills reference |
| `plugins/gamedev-mentor/skills/mentor-consult/SKILL.md` | Replace Step 4's closing plugin-install paragraph (lines 128–132) with a bundled skills reference |
| `plugins/gamedev-mentor/README.md` | Rewrite to document all 8 skills |
| `.claude-plugin/marketplace.json` | Update gamedev-mentor description; remove the 6 individual specialist plugin entries |

## Implementation Details

### 1. Copy Specialist Skills

**Pattern to follow**: Existing structure at `plugins/gamedev-mentor/skills/mentor-review/`

**Overview**: Create six new skill directories under `plugins/gamedev-mentor/skills/`, each mirroring the source plugin's skill directory.

**Implementation steps**:

1. For each specialist plugin, copy its skill directory into `plugins/gamedev-mentor/skills/`:

```bash
# Run from repo root
cp -r plugins/ai-rubric/skills/ai-design plugins/gamedev-mentor/skills/ai-design
cp -r plugins/combat-design/skills/combat-design plugins/gamedev-mentor/skills/combat-design
cp -r plugins/player-protection/skills/player-protection-design plugins/gamedev-mentor/skills/player-protection-design
cp -r plugins/ethical-engagement-design/skills/ethical-engagement-design plugins/gamedev-mentor/skills/ethical-engagement-design
cp -r plugins/game-design-problem-solving/skills/game-design-problem-solving plugins/gamedev-mentor/skills/game-design-problem-solving
cp -r plugins/puzzle-design/skills/puzzle-design plugins/gamedev-mentor/skills/puzzle-design
```

2. Verify all 8 skill directories are present:

```bash
ls plugins/gamedev-mentor/skills/
# Expected: ai-design  combat-design  ethical-engagement-design  game-design-problem-solving  mentor-consult  mentor-review  player-protection-design  puzzle-design
```

---

### 2. Update mentor-review SKILL.md

**Pattern to follow**: `plugins/gamedev-mentor/skills/mentor-review/SKILL.md` (existing file)

**Overview**: The "Specialist Plugin Recommendations" section at the end of the file tells users to install external plugins. Since those skills are now bundled, replace it with a note directing users to the specialist skills they already have.

**Current content to remove** (lines 160–171):

```markdown
### Specialist Plugin Recommendations

Based on the findings, recommend which specialist plugins to install for deep implementation help:

- Heavy AI findings → `ai-rubric@sharkjets`
- Heavy combat findings → `combat-design@sharkjets`
- Heavy incentive findings → `player-protection@sharkjets`
- Heavy engagement findings → `ethical-engagement-design@sharkjets`
- Heavy puzzle findings → `puzzle-design@sharkjets`
- Systemic design methodology problems → `game-design-problem-solving@sharkjets`

Only recommend plugins that address actual findings from this review.
```

**Replace with**:

```markdown
### Next Steps with Specialist Skills

Based on the findings, point the developer to the relevant bundled skill for deep implementation help. Each skill is already included in this plugin — no additional install needed.

- Heavy AI findings → use the `/ai-design` skill
- Heavy combat findings → use the `/combat-design` skill
- Heavy incentive findings → use the `/player-protection-design` skill
- Heavy engagement findings → use the `/ethical-engagement-design` skill
- Heavy puzzle findings → use the `/puzzle-design` skill
- Systemic design methodology problems → use the `/game-design-problem-solving` skill

Only surface skills that address actual findings from this review.
```

---

### 3. Update mentor-consult SKILL.md

**Pattern to follow**: `plugins/gamedev-mentor/skills/mentor-consult/SKILL.md` (existing file)

**Overview**: Step 4's closing paragraph tells the user to install external plugins. Replace it with a reference to the bundled skills.

**Current content to remove** (lines 128–132):

```markdown
> For deep implementation help on [the relevant domain(s)], install the specialist plugin:
> - `[plugin-name]@sharkjets` — [one sentence on what it adds]

Only recommend plugins where the findings were significant. List the installation command.
```

**Replace with**:

```markdown
> For deep implementation help on [the relevant domain(s)], use the relevant bundled skill — no additional install needed:
> - `/ai-design` — detailed AI behavior rubric and implementation patterns
> - `/combat-design` — combat feel, animation phases, hit stop, and defensive mechanics
> - `/player-protection-design` — player incentives, reward timing, and push-forward design
> - `/ethical-engagement-design` — pacing, novelty curves, session loops, and dark pattern avoidance
> - `/game-design-problem-solving` — root cause analysis and playtesting methodology
> - `/puzzle-design` — catch/revelation structure, hint systems, and difficulty curves

Only surface skills that are relevant to the actual findings from this consultation.
```

---

### 4. Update plugin.json

**Overview**: Update the description to reflect the expanded scope.

**New content**:

```json
{
  "name": "gamedev-mentor",
  "description": "Complete game design skill suite — mentor review and consultation for design audits, plus specialist skills for AI behavior, combat feel, player incentives, engagement pacing, puzzle design, and design problem-solving. One install, all skills included.",
  "version": "2.0.0",
  "author": {
    "name": "Skid Vis"
  }
}
```

---

### 5. Update gamedev-mentor README.md

**Overview**: Rewrite the README to document all 8 skills, how to use them, and what each covers. Remove references to installing other plugins.

**Structure for the new README**:

```markdown
# gamedev-mentor

Complete game design skill suite for Claude Code. One install gives you a senior game design mentor plus six specialist skills covering every major domain of game design.

## Install

\`\`\`
/plugin install gamedev-mentor@sharkjets
\`\`\`

## Skills

### Entry Points (start here if you're unsure what you need)

**`/mentor-review [path/to/project]`**
Reads your actual game code and audits it against design principles across all domains.
Produces severity-ranked findings (CRITICAL / NOTABLE / MINOR) with file:line references.

**`/mentor-consult`**
Structured interview — no code required. Diagnoses design problems through conversation.
Identifies root causes, maps them to principles, gives concrete recommendations.

### Specialist Skills (use for deep implementation help)

**`/ai-design`**
Enemy behaviors, NPC logic, behavior trees, dynamic difficulty, telegraphing, attacker limits.

**`/combat-design`**
Melee combat feel, attack animation phases, hit stop, defensive mechanics, combo structure.

**`/player-protection-design`**
Player incentives, reward timing, push-forward mechanics, optimal vs. intended strategy alignment.

**`/ethical-engagement-design`**
Pacing, novelty curves, session loop structure, flow state, onboarding, dark pattern avoidance.

**`/game-design-problem-solving`**
Root cause analysis, lever management, creative reframing, playtesting methodology.

**`/puzzle-design`**
Catch and revelation structure, hint systems, misdirection, minimalism, difficulty curves.

## Workflow

1. Start with `/mentor-review` (if you have code) or `/mentor-consult` (if you want a conversation).
2. The mentor will identify which domains have problems.
3. Follow up with the relevant specialist skill for deep implementation guidance.
```

---

### 6. Update marketplace.json

**Overview**: Update the `gamedev-mentor` entry description and remove the six individual specialist plugin entries. They still exist as standalone plugins in the repo, but they're no longer advertised in the marketplace — `gamedev-mentor` covers them.

**Implementation steps**:

1. Update the `gamedev-mentor` entry:
   - Bump version to `"2.0.0"`
   - Update description to match `plugin.json`
   - Add all specialist keywords to `keywords`

2. Remove these six entries from the `plugins` array:
   - `ai-rubric`
   - `player-protection`
   - `ethical-engagement-design`
   - `game-design-problem-solving`
   - `combat-design`
   - `puzzle-design`

**Resulting marketplace.json** should contain only the `gamedev-mentor` entry.

---

## Validation Commands

```bash
# Verify all 8 skill directories exist
ls plugins/gamedev-mentor/skills/
# Expected: ai-design  combat-design  ethical-engagement-design  game-design-problem-solving  mentor-consult  mentor-review  player-protection-design  puzzle-design

# Verify references are present in each
ls plugins/gamedev-mentor/skills/ai-design/references/
ls plugins/gamedev-mentor/skills/combat-design/references/
ls plugins/gamedev-mentor/skills/player-protection-design/references/
ls plugins/gamedev-mentor/skills/ethical-engagement-design/references/
ls plugins/gamedev-mentor/skills/game-design-problem-solving/references/
ls plugins/gamedev-mentor/skills/puzzle-design/references/

# Confirm mentor-review no longer references external install commands
grep -n "sharkjets" plugins/gamedev-mentor/skills/mentor-review/SKILL.md
# Expected: no output

# Confirm mentor-consult no longer references external install commands
grep -n "sharkjets" plugins/gamedev-mentor/skills/mentor-consult/SKILL.md
# Expected: no output

# Confirm marketplace only has one plugin entry
cat .claude-plugin/marketplace.json
```

## Open Items

- [ ] Decide whether to delete the individual specialist plugin directories from the repo entirely, or leave them in place for developers who prefer standalone installs. The spec leaves them in place — remove later if desired.

---

_This spec is ready for implementation. Follow the patterns and validate at each step._
