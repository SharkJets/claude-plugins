# gamedev-mentor Consolidated Plugin Contract

**Created**: 2026-02-21
**Confidence Score**: 96/100
**Status**: Draft

## Problem Statement

The gamedev-mentor plugin currently acts as a triage and routing tool — it diagnoses design problems and then tells users to install separate specialist plugins (ai-rubric, combat-design, player-protection, ethical-engagement-design, game-design-problem-solving, puzzle-design) to get deep guidance. This creates friction: a developer who just wants expert game design help must discover, install, and manage multiple plugins to get the full benefit.

The existing architecture made sense for a marketplace of independent tools, but from a user perspective it's unnecessarily complex. If you just want a knowledgeable game dev mentor in your Claude Code session, you shouldn't need to install seven things.

## Goals

1. Replace the existing `gamedev-mentor` plugin with an expanded version that bundles all 8 skills in a single installable unit.
2. A developer can install `gamedev-mentor@sharkjets` and immediately have access to every skill — mentor-review, mentor-consult, ai-design, combat-design, player-protection-design, ethical-engagement-design, game-design-problem-solving, and puzzle-design.
3. The mentor skills are updated to stop recommending external plugin installs, since the specialist skills are already present.
4. All existing skill behavior, reference files, and domain knowledge is preserved exactly — this is a packaging change, not a content change.

## Success Criteria

- [ ] Single `plugin.json` covers all 8 skills
- [ ] All 8 SKILL.md files and their `references/` subdirectories are present under `plugins/gamedev-mentor/`
- [ ] `mentor-review` and `mentor-consult` SKILL.md files no longer reference installing external plugins — they reference the bundled specialist skills by name
- [ ] The plugin can be installed with one command: `/plugin install gamedev-mentor@sharkjets`
- [ ] Each specialist skill remains directly user-invocable (e.g., `/ai-design`, `/combat-design`)
- [ ] `marketplace.json` is updated to reflect the consolidated plugin (individual specialist plugin entries removed or marked deprecated)
- [ ] README accurately documents all 8 skills and usage

## Scope Boundaries

### In Scope

- Restructure `plugins/gamedev-mentor/` to include all skills from the 6 specialist plugins
- Copy `SKILL.md` and `references/` from each specialist plugin into `gamedev-mentor/skills/`
- Update `mentor-review` and `mentor-consult` SKILL.md to reference bundled skills instead of recommending external installs
- Update `plugin.json` to declare all 8 skills
- Update `gamedev-mentor/README.md` to document the full skill suite
- Update `marketplace.json` to reflect the new consolidated structure

### Out of Scope

- Changing any skill's core behavior, prompts, or reference content
- Merging or combining the specialist skills into fewer SKILL.md files
- Creating new skills not already in the codebase
- Modifying the individual standalone plugins (they can remain as-is)

### Future Considerations

- Deprecate or remove the individual standalone plugins from the marketplace once the consolidated plugin is confirmed working
- Version bump strategy if individual plugins continue to be maintained separately

---

_This contract was generated from brain dump input. Review and approve before proceeding to specification._
