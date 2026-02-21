# Game Design Problem-Solving Methodology

Step-by-step frameworks, lever worksheets, reframing techniques, and worked examples for diagnosing and fixing game design problems. Engine-agnostic — adapt to any project.

## Table of Contents

1. [Root Cause Analysis Framework](#1-root-cause-analysis-framework)
2. [Lever Identification Worksheet](#2-lever-identification-worksheet)
3. [Dramatic Iteration Protocol](#3-dramatic-iteration-protocol)
4. [Reframing Toolkit](#4-reframing-toolkit)
5. [Second-Order Effect Mapper](#5-second-order-effect-mapper)
6. [Hidden Buff Designer](#6-hidden-buff-designer)
7. [Constraint-Driven Design Generator](#7-constraint-driven-design-generator)
8. [Blind Playtest Protocol](#8-blind-playtest-protocol)

---

## 1. Root Cause Analysis Framework

**Use when:** A player, playtester, or team member reports a problem and you need to find what's actually wrong before jumping to solutions.

### The "Five Whys" for Game Design

Start with the reported symptom and ask "why" until you reach something actionable:

```
SYMPTOM: "Players say weapons break too fast."
│
├─ Why? → Weapons last about 15 kills. Players expect 30+.
│   │
│   ├─ Why do players expect 30+? → Each combat encounter has 5-8 enemies.
│   │   Players use 1 weapon per encounter and expect 4-6 encounters per weapon.
│   │   │
│   │   └─ ROOT CAUSE A: Encounters have too many enemies for the durability model.
│   │       FIX: Reduce enemies per encounter from 5-8 to 3-5.
│   │
│   └─ Why do weapons only last 15 kills? → Each enemy takes 3-4 hits to kill.
│       │
│       └─ ROOT CAUSE B: Enemy health is too high relative to weapon durability.
│           FIX: Lower enemy health so kills take 1-2 hits instead of 3-4.
│
└─ NAIVE FIX (avoid): Double weapon durability.
    PROBLEM: Breaks resource economy, makes weapon crafting trivial,
    removes scarcity tension the game relies on.
```

### The Problem Alignment Check

Before proposing solutions in a team setting, verify everyone agrees on scope:

```
PROBLEM SCOPE CHECKLIST:

[ ] Is this a SYSTEM issue or a FEEL issue?
    System: "The economy is broken — players have too much gold by act 2."
    Feel:   "Buying items doesn't feel satisfying."
    (System needs math changes. Feel needs UX/feedback changes.)

[ ] Is this a DESIGN issue or an IMPLEMENTATION issue?
    Design: "Players shouldn't be able to stack these two buffs."
    Implementation: "The buff stacking works but causes a frame rate drop."
    (Design needs a rule change. Implementation needs code optimization.)

[ ] Is this UNIVERSAL or SKILL-DEPENDENT?
    Universal: "Everyone finds this boss frustrating."
    Skill-dependent: "New players can't beat this, veterans say it's too easy."
    (Universal needs a redesign. Skill-dependent needs dynamic scaling.)

[ ] Is the reporter experiencing the problem or relaying someone else's feedback?
    (Secondhand reports often lose the nuance of the original complaint.)
```

---

## 2. Lever Identification Worksheet

**Use when:** You've identified the problem and need to figure out what you can safely change without breaking identity or creating cascading issues.

### Mapping Levers for Any Game Element

For any weapon, ability, character, mechanic, or system, fill out:

```
ELEMENT: [e.g., "Sniper Rifle"]

IDENTITY LEVERS (DO NOT TOUCH — these define what this thing IS):
┌─────────────────────────────────────────────────────┐
│ • High single-shot damage                           │
│ • Long effective range                              │
│ • Precision-rewarding (headshots matter)             │
│ • Slow, deliberate pace                             │
│                                                      │
│ If you change these, it stops being a sniper rifle.  │
└─────────────────────────────────────────────────────┘

SECONDARY LEVERS (SAFE TO PULL — change the experience without losing identity):
┌─────────────────────────────────────────────────────┐
│ • Reload speed          (slower = more punishing)    │
│ • Scope sway            (more = harder to use)       │
│ • ADS speed             (slower = worse in CQC)      │
│ • Movement speed scoped (slower = more vulnerable)   │
│ • Ammo capacity         (less = more scarcity)       │
│ • Audio signature       (louder = reveals position)  │
│ • Bullet trail          (visible = enemies track you)│
│ • Rate of fire          (already slow, but can go    │
│                          slower without losing feel)  │
│ • Flinch when hit       (more = punishes exposure)   │
└─────────────────────────────────────────────────────┘

CONTEXTUAL LEVERS (changes to OTHER systems that affect this element):
┌─────────────────────────────────────────────────────┐
│ • Map design            (less long sightlines)       │
│ • Counter availability  (give enemies smoke grenades)│
│ • Spawn positions       (don't give snipers free LoS)│
│ • Class limits          (max 1 sniper per team)      │
│ • Economy cost          (make sniper ammo expensive) │
└─────────────────────────────────────────────────────┘
```

### Template for Any Element

```
ELEMENT: _______________

IDENTITY LEVERS (off-limits):
  1. _______________
  2. _______________
  3. _______________

SECONDARY LEVERS (safe to adjust):
  1. _______________ (range: ___ to ___)
  2. _______________ (range: ___ to ___)
  3. _______________ (range: ___ to ___)
  4. _______________ (range: ___ to ___)
  5. _______________ (range: ___ to ___)

CONTEXTUAL LEVERS (other systems):
  1. _______________
  2. _______________
  3. _______________
```

---

## 3. Dramatic Iteration Protocol

**Use when:** You're stuck in the "5% adjustment" trap — making tiny tweaks that don't reveal whether you're heading in the right direction.

### The Double/Halve Method

```
PROBLEM: "Pacing feels slow in the mid-game."

STEP 1: IDENTIFY THE VARIABLE
  → Level length? Enemy count? Resource scarcity? Travel distance? Dialogue length?
  → Pick the one you suspect most.

STEP 2: MAKE A DRAMATIC CHANGE
  BAD:  Reduce level length by 10%.
  GOOD: Cut the level in HALF. Remove the entire second half.

STEP 3: PLAYTEST THE EXTREME VERSION
  Observe:
  [ ] Is the problem gone? (You found the right variable.)
  [ ] Is a NEW problem introduced? (The variable matters but needs moderation.)
  [ ] Is the problem unchanged? (Wrong variable. Try a different one.)
  [ ] Is the problem WORSE? (You were pulling in the wrong direction.)

STEP 4: FINE-TUNE TOWARD THE SWEET SPOT
  If halving fixed it: try 60%, 70%, 75% — narrow in.
  If halving created a new problem: the answer is somewhere between 50% and 100%.
  If halving did nothing: go back to Step 1 with a different variable.

STEP 5: VALIDATE
  Blind playtest the tuned version (see Blind Playtest Protocol).
```

### Worked Example

```
PROBLEM: "Boss fight takes too long, players get bored."

Round 1: Halve the boss health.
  Result: Fight is exciting but boss feels like a pushover. No tension.
  Learning: Health matters, but 50% is too low.

Round 2: Set health to 75% of original.
  Result: Better. Still slightly long in phase 2.
  Learning: Phase 2 specifically is the problem.

Round 3: Keep total health at 75%. Redistribute: give Phase 1 more health,
  Phase 2 less. Phase 2 is now short and intense.
  Result: Players report the fight feels great.

Total iterations: 3. Time: ~2 hours.
Versus: 5% tweaks would have taken 15+ iterations across days.
```

---

## 4. Reframing Toolkit

**Use when:** The obvious solution feels wrong, punitive, or unintuitive, and you need a fresh angle.

### The Penalty-to-Choice Flip

For any mechanic that feels like a punishment, try inverting it into a voluntary risk/reward choice:

```
CURRENT DESIGN (punitive):
  "Players pay gold to save the game."
  Player feels: Taxed. Saving is a basic need, not a luxury.

REFRAME (voluntary):
  "Saving is free. But you can DESTROY the checkpoint for a gold reward."
  Player feels: Empowered by a choice. Risk-takers get rewarded. 
  Cautious players lose nothing.

─────────────────────────────────────

CURRENT DESIGN (punitive):
  "Items are lost on death."
  Player feels: Punished. All that progress gone.

REFRAME (retrieval challenge):
  "Items drop at your death location. Retrieve them before dying again."
  Player feels: Tense, motivated. The loss is reversible with skill.

─────────────────────────────────────

CURRENT DESIGN (punitive):
  "Limited inventory forces players to drop items."
  Player feels: Restricted. "I want to keep everything."

REFRAME (spatial puzzle):
  "Inventory is a grid. Items have Tetris-like shapes. Fitting more = skill."
  Player feels: Challenged by a mini-game. Organization is satisfying.

─────────────────────────────────────

TEMPLATE:
  Current: "Player is forced to [unpleasant thing]."
  Reframe: "Player can CHOOSE to [risky thing] for [reward]."
  Key: The default should be safe. The risk is opt-in.
```

### The "Pull It Into the World" Technique

When a menu or UI system feels clunky:

```
MENU PROBLEM → WORLD SOLUTION

Upgrade menu with 50 options
  → Physical upgrade bench at specific locations
  Benefits: Pacing (player must explore to find benches),
  immersion (upgrading feels physical), reduced choice overload
  (show relevant upgrades per bench type)

Crafting recipe list
  → Visual crafting station with ingredient slots
  Benefits: Spatial reasoning replaces menu scanning,
  experimentation is encouraged, less overwhelming

Skill tree screen
  → Abilities unlock by DOING (use fire 10 times → unlock fireball)
  Benefits: "Learning by doing" feels natural, no menu needed,
  progression is visible through gameplay not UI

Map with quest markers
  → NPCs give verbal/visual directions, environmental landmarks
  Benefits: Exploration feels organic, world feels lived-in,
  player pays attention to the environment

Fast travel menu
  → In-world transit system (bus stops, portals, rideable creatures)
  Benefits: Travel has presence, world feels connected,
  player encounters things along the way
```

### The Miyamoto Multi-Solve Check

When evaluating a proposed solution, ask:

```
PROPOSED SOLUTION: _______________

[ ] Does this fix the primary problem?
    Problem: _______________
    How it fixes it: _______________

[ ] Does this ALSO help with any of:
    [ ] A pacing issue? How: _______________
    [ ] A balance issue? How: _______________
    [ ] An onboarding issue? How: _______________
    [ ] A narrative issue? How: _______________
    [ ] A variety issue? How: _______________
    [ ] An accessibility issue? How: _______________

MULTI-SOLVE SCORE: ___/7

If 1/7: Keep looking. There's probably a solution that does more.
If 2-3/7: Good candidate. Consider refinements.
If 4+/7: Excellent. This is likely the right solution.
```

---

## 5. Second-Order Effect Mapper

**Use when:** You're about to ship a balance change and need to trace the ripple effects before they hit players.

```
PROPOSED CHANGE: "Reduce shotgun damage by 20%"

FIRST-ORDER EFFECTS (direct):
├─ Shotgun kills take 1 more shot on average
├─ Shotgun time-to-kill increases by ~25%
└─ Shotgun users must land more shots per encounter

SECOND-ORDER EFFECTS (one step removed):
├─ Shotgun is now weaker than SMG at close range
│   └─ SMG may become new dominant close-range weapon
├─ Shotgun-focused characters/classes become less viable
│   └─ Their pick rate drops → team composition shifts
├─ Defender side (which relies on shotguns) loses win rate
│   └─ Map balance shifts toward attackers
└─ Shotgun + ability combos that relied on one-shot potential break
    └─ Those combos need re-evaluation

THIRD-ORDER EFFECTS (two steps removed):
├─ SMG becomes meta → SMG nerfs needed in next patch
├─ Defender win rate drop → Round time may need adjustment
├─ Shotgun-focused characters may need buffs elsewhere
└─ Players who mained shotgun feel betrayed → community backlash

MITIGATION PLAN:
  1. Monitor SMG pick rate for 1 week after change.
  2. Track Attacker vs Defender win rates.
  3. Prepare compensatory buff to shotgun-focused characters.
  4. Communicate reasoning to community before patch ships.
```

### Template

```
PROPOSED CHANGE: _______________

FIRST-ORDER EFFECTS:
  1. _______________
  2. _______________
  3. _______________

SECOND-ORDER EFFECTS:
  1. _______________ → because of first-order #___
  2. _______________ → because of first-order #___
  3. _______________ → because of first-order #___

THIRD-ORDER EFFECTS:
  1. _______________
  2. _______________

MITIGATION PLAN:
  1. Monitor: _______________
  2. Prepare: _______________
  3. Communicate: _______________
```

---

## 6. Hidden Buff Designer

**Use when:** Playtest data shows a skill gap problem — beginners struggle while experts are fine, and you need to help beginners without dumbing down the game for everyone.

### The Invisible Assistance Pattern

```
class HiddenBuff:
    # DESIGN: Identify what EXPERTS do that BEGINNERS don't.
    # Then build an invisible system that compensates for the gap.
    # Experts never notice it because they never trigger it.

    # === Example: Magic Bullets (Gears of War) ===
    # Expert behavior: Manually reloads between fights, always has full clip.
    # Beginner behavior: Never reloads, empties entire clip including last rounds.
    # Hidden buff: Last 3 bullets in the clip get +30% accuracy.
    # Experts never fire those bullets. Beginners rely on them.

    func apply_accuracy_modifier(bullets_remaining, clip_size):
        if bullets_remaining <= 3:
            return base_accuracy * 1.3    # Invisible buff
        return base_accuracy

    # === Example: Coyote Jump (platformers) ===
    # Expert behavior: Jumps at the very edge of platforms.
    # Beginner behavior: Jumps slightly late, after walking off the edge.
    # Hidden buff: Allow jumping for ~100ms after leaving a platform.
    # Experts jump on time and never notice. Beginners get saved.

    coyote_time = 0.1   # seconds
    time_since_grounded = 0.0

    func can_jump():
        return is_grounded or time_since_grounded < coyote_time

    # === Example: Enemy Aim Delay (shooters) ===
    # Expert behavior: Takes cover proactively, minimizes exposure.
    # Beginner behavior: Stands in the open, reacts slowly.
    # Hidden buff: Enemies delay shooting for 0.5s after acquiring the player.
    # Experts are already in cover by then. Beginners get a grace period.

    func get_enemy_fire_delay(player_skill_estimate):
        base_delay = 0.3
        if player_skill_estimate < NOVICE_THRESHOLD:
            return base_delay + 0.3    # Extra grace for beginners
        return base_delay
        # NEVER tell the player this exists.

    # === Design checklist for hidden buffs ===
    # 1. Identify the skill gap: What do experts do that beginners don't?
    # 2. Find the behavioral trigger: When does beginner behavior diverge?
    # 3. Design the invisible assist: Only activates on beginner behavior.
    # 4. Verify expert blindness: Experts should NEVER trigger or notice it.
    # 5. Never document it in-game: Tooltips, tutorials, patch notes — none.
```

---

## 7. Constraint-Driven Design Generator

**Use when:** You need more content, variety, or scope but have limited resources (art, engineering, time).

### Low-Cost Content Patterns

```
CONSTRAINT: Limited animation budget

LOW-COST ENEMY DESIGNS:
┌──────────────────────────────────────────────────────────────────┐
│ Invisible Enemy                                                   │
│ Cost: 0 models, 0 animations. Just audio + particle effects.     │
│ Gameplay: Tension through sound design. Player listens, not looks.│
│ Examples: Shimmering distortion, footstep sounds, displaced dust. │
├──────────────────────────────────────────────────────────────────┤
│ Physics-Object Enemy                                              │
│ Cost: Reuses existing physics objects (barrels, crates, debris).  │
│ Gameplay: Environmental hazard that moves. Unpredictable.         │
│ Examples: Possessed furniture, rolling boulder, animated rubble.  │
├──────────────────────────────────────────────────────────────────┤
│ Swarm / Particle Enemy                                            │
│ Cost: One small model instanced many times. Particle system.      │
│ Gameplay: Volume and pressure instead of individual threats.      │
│ Examples: Insect swarm, nanobots, magic motes.                   │
├──────────────────────────────────────────────────────────────────┤
│ Reskinned Hybrid                                                  │
│ Cost: Existing model + color swap + mixed behavior tree.          │
│ Gameplay: Familiar shape, surprising behavior.                    │
│ Examples: "Elite" variant with new abilities, corrupted version.  │
├──────────────────────────────────────────────────────────────────┤
│ Environmental Hazard                                              │
│ Cost: Level geometry + trigger volume + damage.                   │
│ Gameplay: Spatial challenge, no AI needed.                        │
│ Examples: Lava floor, crushing walls, poison gas zones.           │
└──────────────────────────────────────────────────────────────────┘

CONSTRAINT: Limited level design time

LOW-COST VARIETY TECHNIQUES:
  • Remix existing levels: Play them backward, add night/weather variants,
    flood them, add a time pressure, lock off sections.
  • Modifier layers: Same level, different rules. "This time there's fog."
    "This time enemies respawn." "This time you have no gun."
  • Procedural recombination: Hand-craft 20 room templates, connect randomly.
    Less work than 5 unique levels, more variety than 1.

CONSTRAINT: Limited narrative budget

LOW-COST STORY TECHNIQUES:
  • Environmental storytelling: Arrange existing objects to tell a story.
    A skeleton near a locked door. An overturned table near a window.
  • Audio logs / notes: Text or short voice lines. No cinematics needed.
  • NPC barks: Short, contextual lines that build world. "I heard the
    bridge collapsed last week." Cheap to write and record.
```

---

## 8. Blind Playtest Protocol

**Use when:** You've implemented a fix and need to verify it actually works.

```
BLIND PLAYTEST PROTOCOL

SETUP:
  1. Prepare TWO builds: the old version and the fixed version.
  2. Assign testers randomly to each build.
  3. DO NOT tell testers which build they have.
  4. DO NOT tell testers what was changed.
  5. Give identical play instructions to both groups.

OBSERVATION:
  Record the following for BOTH groups:
  [ ] Does the original problem occur? (Y/N, frequency)
  [ ] Do any NEW problems appear? (Describe)
  [ ] Does the tester mention anything about the changed system?
      - Unprompted positive comment → Change improved feel.
      - Unprompted negative comment → Change may be too heavy.
      - No mention at all → Change is invisible (ideal for most fixes).

POST-PLAY INTERVIEW (for both groups):
  Ask these questions in order. Do NOT mention the change until Q4.
  
  Q1: "How did the game feel overall?"
      (Open-ended. Listen for the problem symptom without prompting.)
  
  Q2: "Was there anything that felt frustrating or off?"
      (If they mention the original problem, the fix didn't work.)
  
  Q3: "Was there anything that felt surprisingly good?"
      (If they mention the changed system positively, the fix worked AND improved feel.)
  
  Q4: "We changed [specific system]. Did you notice?"
      (Only ask after Q1-Q3 to avoid biasing responses.)
      - "No" → Invisible fix (good for most changes)
      - "Yes, it felt better" → Noticeable improvement (good)
      - "Yes, it felt weird" → Overcorrection (pull back)

ANALYSIS:
  Compare results between old and new build groups.
  If the problem group reports the issue and the fix group doesn't → Success.
  If both groups report it → Fix didn't address the root cause. Return to diagnosis.
  If the fix group reports NEW issues → The fix introduced side effects. Trace second-order effects.
```
