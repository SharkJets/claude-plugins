# Puzzle Design Frameworks

Step-by-step puzzle construction processes, catch/revelation templates, misdirection patterns, difficulty quantification, curve planning, and playtesting protocols. Engine-agnostic — adapt to any project.

## Table of Contents

1. [Puzzle Construction Process](#1-puzzle-construction-process)
2. [Catch/Revelation Template](#2-catchrevelation-template)
3. [Misdirection Patterns](#3-misdirection-patterns)
4. [Minimalism Audit](#4-minimalism-audit)
5. [Feedback Checklist](#5-feedback-checklist)
6. [Difficulty Quantification System](#6-difficulty-quantification-system)
7. [Difficulty Curve Planner](#7-difficulty-curve-planner)
8. [Puzzle Playtesting Protocol](#8-puzzle-playtesting-protocol)

---

## 1. Puzzle Construction Process

**Use when:** Designing a new puzzle from scratch. Work through these steps in order.

### Step-by-Step

```
STEP 1: START WITH YOUR RULES
  List the mechanics available to the player:
  ┌─────────────────────────────────────────────────┐
  │ Mechanic 1: _______________                     │
  │ Mechanic 2: _______________                     │
  │ Mechanic 3: _______________                     │
  │                                                  │
  │ Key interactions between mechanics:              │
  │ • [M1] + [M2] = _______________                 │
  │ • [M1] + [M3] = _______________                 │
  │ • [M2] + [M3] = _______________                 │
  │                                                  │
  │ Key limitations / restrictions:                  │
  │ • Player cannot _______________                  │
  │ • [Mechanic] only works when _______________     │
  │ • [Object] cannot be _______________             │
  └─────────────────────────────────────────────────┘

STEP 2: DESIGN THE CATCH
  Create a scenario where two necessary actions conflict:
  ┌─────────────────────────────────────────────────┐
  │ "The player needs to _______________, but they   │
  │  also need to _______________, and doing one     │
  │  prevents the other because _______________."    │
  └─────────────────────────────────────────────────┘
  
  The catch IS the puzzle. Everything else is scaffolding.

STEP 3: DESIGN THE REVELATION
  The solution should exploit a non-obvious (but logical) consequence
  of the rules:
  ┌─────────────────────────────────────────────────┐
  │ "The player realizes they can _______________,   │
  │  which resolves the conflict because             │
  │  _______________."                               │
  │                                                  │
  │ This works because of rule: _______________      │
  │ This is surprising because: _______________      │
  │ This is reusable in future puzzles: [Y/N]        │
  └─────────────────────────────────────────────────┘

STEP 4: DESIGN THE MISDIRECTION
  What will the player try first? It should fail clearly.
  ┌─────────────────────────────────────────────────┐
  │ Obvious approach: "The player will probably      │
  │  try to _______________."                        │
  │                                                  │
  │ Why it fails: "This doesn't work because         │
  │  _______________."                               │
  │                                                  │
  │ What the failure teaches: "This forces the       │
  │  player to realize _______________."             │
  └─────────────────────────────────────────────────┘

STEP 5: BUILD THE PHYSICAL LAYOUT
  Place only the elements needed for the catch, revelation,
  and misdirection. Nothing extra.
  
  Elements needed:
  [ ] _______________ (for the catch)
  [ ] _______________ (for the catch)
  [ ] _______________ (for the revelation)
  [ ] _______________ (for the misdirection)
  
  Elements to REMOVE: anything not checked above.

STEP 6: ADD FEEDBACK
  For every player action, define the feedback:
  [ ] Goal is visible and clear
  [ ] Connections between elements are shown (lines, colors, audio)
  [ ] Correct actions produce positive feedback
  [ ] Wrong approach fails CLEARLY and LOGICALLY
  [ ] State changes are visible (what moved, what activated)
```

### Worked Example

```
GAME: 2D puzzle-platformer with a "gravity flip" mechanic.

RULES:
  - Player can walk, jump, and flip gravity (up becomes down).
  - Boxes fall with gravity and can be stood on.
  - Buttons must be held down to open corresponding doors.
  - Player can only carry one box at a time.

CATCH:
  "The player needs a box on the floor button to open the door,
   but the door is on the CEILING, and the box is too heavy to
   carry while gravity-flipped."

REVELATION:
  "If the player flips gravity while the box is in midair (just
   after dropping it), the box 'falls' upward to the ceiling,
   landing on a ceiling-mounted button that opens a ceiling door."
  
  Rule exploited: Gravity flip affects ALL objects, not just the player.
  Surprising because: Players assume gravity flip only moves themselves.
  Reusable: YES — "flip objects in transit" becomes a key tool.

MISDIRECTION:
  "The player will try to carry the box to the floor button (there IS
   a floor button visible). This works — but the door it opens leads
   to a dead end. The REAL exit requires the ceiling button."
  
  What failure teaches: "The floor button isn't the solution. I need
   to get something onto the ceiling. How do I get a box up there?"

LAYOUT:
  - 1 box (for the catch)
  - 1 floor button (for the misdirection — leads to dead end)
  - 1 ceiling button (for the revelation)
  - 1 ceiling door (the real exit)
  - 1 dead-end door (connected to floor button)
  
  That's it. 5 elements. No extras.

FEEDBACK:
  - Colored lines connect each button to its door.
  - Box landing on button plays a click + door-open animation.
  - Dead-end door opens to a visible wall — immediately clear it's wrong.
  - Ceiling button is visible but seemingly unreachable — creates the "how?" tension.
```

---

## 2. Catch/Revelation Template

**Use when:** You need to brainstorm catches and revelations quickly. Fill in the blanks with your game's specific mechanics.

### Common Catch Patterns

```
POSITIONAL CONFLICT:
  "I need to be at [A] to activate [X], but I need to be at [B]
   to benefit from [X]."
  Resolution types: Remote activation, delayed trigger, proxy object,
  self-moving element.

RESOURCE CONFLICT:
  "I need [resource] to do [A], but doing [A] consumes the only
   [resource] I need for [B]."
  Resolution types: Resource regeneration, alternative source,
  combining partial resources, transforming one resource into another.

ORDER CONFLICT:
  "I need to do [A] before [B], but [A] blocks the path to [B]."
  Resolution types: Undo mechanic, alternate route created by [A],
  rewind/time mechanic, doing [A] from a different position.

SCALE CONFLICT:
  "The solution requires [big thing], but only [small thing] is available."
  Resolution types: Growth mechanic, stacking, combining, perspective trick.

VISIBILITY CONFLICT:
  "I can see the solution but can't reach it / I can reach the area
   but can't see what I'm doing."
  Resolution types: Mirrors, portals, indirect control, mapping,
  sound-based navigation.
```

### Common Revelation Types

```
ROLE REVERSAL:
  "An element I thought was an obstacle is actually a tool."
  Example: The enemy IS the weight for the pressure plate.

RULE INTERACTION:
  "Two mechanics I've been using separately combine in a new way."
  Example: Portal + momentum = launched across the room.

PERSPECTIVE SHIFT:
  "The solution requires looking at the problem from a different
   angle — literally or figuratively."
  Example: The impossible wall is actually a floor when gravity flips.

CONSTRAINT EXPLOITATION:
  "A limitation I thought was restrictive is actually the key."
  Example: "I can only carry one box" means the OTHER box stays put
  and holds the button.

DELAYED EFFECT:
  "An action I do NOW produces the result I need LATER."
  Example: Set up a domino chain that triggers after you've moved
  to where you need to be.

ENVIRONMENT AS TOOL:
  "The room layout itself is part of the solution."
  Example: Bouncing a projectile off walls to hit a switch behind you.
```

---

## 3. Misdirection Patterns

**Use when:** You've designed the catch and revelation but need to guide the player's initial approach to fail productively.

### Pattern Library

```
THE OBVIOUS-BUT-INCOMPLETE:
  Setup: Place an element that solves PART of the puzzle trivially.
  Player tries: Uses the obvious element. Gets partial success.
  Failure reveals: "I solved the easy part. Now I see the REAL problem."
  
  Example: A button opens Door 1, but behind Door 1 is Door 2 with
  no button. The player now knows the real puzzle is Door 2.

THE TANTALIZINGLY CLOSE:
  Setup: The goal is visible and ALMOST reachable through the obvious path.
  Player tries: Goes straight for the goal. Falls just short.
  Failure reveals: "I need to go the OTHER way first to get what I need."
  
  Example: The exit ledge is 1 unit too high to jump to. There's a box
  on the far side of the room. Player realizes they need the box as a step.

THE DECOY SOLUTION:
  Setup: Place a valid-looking path that leads to a dead end.
  Player tries: Follows the decoy. Reaches a dead end.
  Failure reveals: "This path wasn't the answer. What did I miss?"
  
  Example: A door opens with a key. Behind it is a wall. The key was
  actually needed to unlock a mechanism on the OTHER side of the room.

THE ASSUMED CONSTRAINT:
  Setup: The puzzle layout implies a constraint that doesn't actually exist.
  Player assumes: "I can't do X because of the layout."
  Revelation: "Wait — nothing actually prevents me from doing X. I assumed
  a wall that wasn't there."
  
  Example: Two rooms with a doorway between them. Player assumes they
  must solve each room separately. Actually, an object can be carried
  between rooms. The rooms are ONE puzzle.

THE SEQUENCE TRAP:
  Setup: Elements suggest a specific order of operations.
  Player tries: Does A → B → C. Gets stuck at C.
  Failure reveals: "I need to do B → A → C instead."
  
  Example: Three levers labeled 1, 2, 3. Pulling them in numerical order
  doesn't work. The correct sequence is based on the colors of their
  connected doors, not the numbers.
```

### Misdirection Quality Check

```
For each misdirection, verify:

[ ] The "wrong" approach is genuinely the FIRST thing most players will try.
    (If it's not obvious, it's not misdirection — it's just a wrong answer.)

[ ] The failure is CLEARLY impossible, not almost-possible.
    (Player must think "this CAN'T work" not "maybe I wasn't precise enough.")

[ ] The failure teaches something about the CATCH.
    (The wrong path should illuminate why the puzzle is hard.)

[ ] The player doesn't waste more than 30-60 seconds on the wrong path.
    (Misdirection should redirect quickly. Extended failure = frustration.)

[ ] The wrong path doesn't require complex execution.
    (If the player spent 5 minutes executing the wrong approach,
     they'll be angry, not enlightened.)
```

---

## 4. Minimalism Audit

**Use when:** A puzzle feels cluttered, confusing, or harder than it should be.

### The Removal Test

For every element in the puzzle, ask:

```
ELEMENT: _______________

[ ] Does this element contribute to the CATCH?
[ ] Does this element contribute to the REVELATION?
[ ] Does this element contribute to the MISDIRECTION?
[ ] Does this element provide essential FEEDBACK?

If none are checked → REMOVE IT.

If one is checked → Keep it, but check if another element
already serves that purpose. If so, merge or remove the weaker one.

If two or more are checked → Essential. Keep it.
```

### Complexity Budget

```
PUZZLE COMPLEXITY GUIDE:

TUTORIAL PUZZLE (teaching one mechanic):
  Target: 2-3 interactive elements
  Catches: 0-1 (may be a pure demonstration)
  Steps to solve: 1-3

EARLY PUZZLE (applying learned mechanic):
  Target: 3-4 interactive elements
  Catches: 1
  Steps to solve: 3-5

STANDARD PUZZLE (combining mechanics):
  Target: 4-6 interactive elements
  Catches: 1, possibly with a sub-catch
  Steps to solve: 4-8

ADVANCED PUZZLE (multiple revelations):
  Target: 5-8 interactive elements
  Catches: 1-2 (layered)
  Steps to solve: 6-12

CAPSTONE PUZZLE (everything comes together):
  Target: 6-10 interactive elements
  Catches: 2-3 (combining multiple revelations from the game)
  Steps to solve: 8-15

If your puzzle exceeds these targets, try splitting it into
two connected puzzles or removing elements.
```

---

## 5. Feedback Checklist

**Use when:** Playtesting reveals that players are confused about the puzzle state, not the puzzle logic.

```
PUZZLE FEEDBACK AUDIT

GOAL CLARITY:
[ ] The player can see or understand the goal within 5 seconds of entering.
[ ] The goal is visually distinct (glowing exit, marked target, highlighted area).
[ ] If the goal isn't visible, there's a clear indicator of direction.

CONNECTION VISIBILITY:
[ ] Every cause-effect pair is visually linked.
    Button → Door: Line, color match, label, or proximity.
    Switch → Mechanism: Animation, sound, particle trail.
    Input → Output: Immediate response with visual/audio confirmation.
[ ] The player can trace the chain from input to output without guessing.

STATE VISIBILITY:
[ ] Active elements look different from inactive ones (on/off, lit/unlit).
[ ] The player can see the CURRENT state of the entire puzzle at a glance.
[ ] State changes are animated, not instant (so the player notices them).
[ ] Reversible actions look reversible (toggles, not one-way switches — unless
    the one-way nature is the point).

FAILURE CLARITY:
[ ] When the player's approach is wrong, the failure is IMMEDIATE.
    Not: they get 90% through and fail at the end.
    Yes: they hit the contradiction within seconds.
[ ] The failure is LOGICAL, not MECHANICAL.
    Not: "I wasn't fast enough" (when speed isn't the solution).
    Yes: "This path is clearly blocked / doesn't lead anywhere."
[ ] The failure is RECOVERABLE.
    Not: the puzzle is now stuck and must be reset.
    Yes: the player can undo or try again immediately.

PROGRESS FEEDBACK:
[ ] Partial solutions produce partial feedback.
    (2 of 3 buttons pressed = 2 of 3 lights on.)
[ ] The player can tell how "close" they are to solving it.
[ ] Correct steps produce a distinctly POSITIVE sound/visual.
[ ] Incorrect steps produce a distinctly NEUTRAL sound/visual.
    (Not punishing — just "nope, that's not it.")
```

---

## 6. Difficulty Quantification System

**Use when:** You need to order puzzles in a sequence and want a more systematic approach than gut feeling.

### The Four Dimensions

Score each puzzle 1-5 on each dimension:

```
PUZZLE: _______________

DIMENSION 1: SOLUTION SPACE (fewer solutions = harder)
  1 = Many valid solutions (5+). Player can stumble into one.
  2 = Several valid solutions (3-4). Multiple viable approaches.
  3 = A few valid solutions (2). Player must think but has options.
  4 = One primary solution with minor variation.
  5 = Exactly one solution. Must find THE answer.
  SCORE: ___

DIMENSION 2: STEP COUNT (more steps = harder to hold in memory)
  1 = 1-2 steps. Almost trivial to execute.
  2 = 3-4 steps. Straightforward sequence.
  3 = 5-6 steps. Requires planning ahead.
  4 = 7-9 steps. Must visualize the full chain.
  5 = 10+ steps. Demands significant working memory.
  SCORE: ___

DIMENSION 3: OPTION BREADTH (more choices per moment = harder)
  1 = 1-2 choices at each decision point.
  2 = 3 choices at each point. Manageable branching.
  3 = 4-5 choices. Significant mental exploration needed.
  4 = 6-8 choices. Overwhelming without careful analysis.
  5 = 9+ choices or continuous (analog) options.
  SCORE: ___

DIMENSION 4: MECHANIC LOAD (more concepts to juggle = harder)
  1 = Uses 1 mechanic the player already knows.
  2 = Uses 2 known mechanics.
  3 = Uses 2-3 known mechanics + 1 new interaction.
  4 = Uses 3-4 mechanics in combination.
  5 = Uses 4+ mechanics simultaneously, requiring mastery of all.
  SCORE: ___

TOTAL DIFFICULTY SCORE: ___ / 20

DIFFICULTY BAND:
  4-7:   TUTORIAL (gentle introduction)
  8-10:  EASY (comfortable application)
  11-13: MEDIUM (requires real thought)
  14-16: HARD (multiple revelations needed)
  17-20: EXPERT (capstone-level challenge)
```

---

## 7. Difficulty Curve Planner

**Use when:** You have a set of puzzles and need to order them into a satisfying sequence.

```
DIFFICULTY CURVE TEMPLATE

Score each puzzle using the Difficulty Quantification System,
then plot them on this curve:

INTENSITY
  20 │                                              ★ FINAL
     │                                         ●
  16 │                                    ●
     │                               ●
  12 │                          ●  ○         ← Rest puzzle
     │                     ●
   8 │               ●  ○                   ← Rest puzzle
     │          ●
   4 │     ●
     │  ●
     └──────────────────────────────────────────────
       1  2  3  4  5  6  7  8  9  10 11 12 13 14 15
                        PUZZLE NUMBER

KEY:
  ● = Standard puzzle (advancing difficulty)
  ○ = Rest puzzle (reinforces known concepts, slightly easier)
  ★ = Capstone puzzle (combines everything)

RULES:
  1. Never increase difficulty by more than 3 points between
     consecutive puzzles.
  2. After every 3-4 difficulty increases, insert a rest puzzle
     (uses known mechanics in comfortable new arrangement).
  3. New mechanics are introduced in LOW-difficulty puzzles
     (score 4-7), then tested in progressively harder ones.
  4. Capstone puzzles should combine 3+ mechanics from
     the current chapter/world.
  5. The first puzzle after a new mechanic introduction should
     test ONLY that mechanic, not combine it with others.
```

### Vertical Building Plan

```
MECHANIC INTRODUCTION SEQUENCE:

MECHANIC A:
  Puzzle 1: Demonstrate A (score: 4-5)
  Puzzle 2: Apply A with one catch (score: 6-8)
  Puzzle 3: A + mild complexity (score: 8-10)

MECHANIC B:
  Puzzle 4: Demonstrate B (score: 4-5)
  Puzzle 5: Apply B with one catch (score: 6-8)

COMBINATION A+B:
  Puzzle 6: A and B in same puzzle but independent (score: 8-10)
  Puzzle 7: A and B must interact to solve (score: 10-13)
  Puzzle 8: REST — A or B alone, comfortable (score: 7-8)

MECHANIC C:
  Puzzle 9: Demonstrate C (score: 4-5)
  Puzzle 10: Apply C (score: 6-8)

COMBINATION A+B+C:
  Puzzle 11: Two of three interact (score: 11-13)
  Puzzle 12: CAPSTONE — all three required (score: 14-16)

Each mechanic gets 2-3 puzzles solo before being combined.
Each combination gets at least 1 gentle introduction before
the full-complexity version.
```

---

## 8. Puzzle Playtesting Protocol

**Use when:** You need to validate that your puzzles work for actual human brains, not just your designer brain.

```
PUZZLE PLAYTEST PROTOCOL

SETUP:
  - Fresh player who has NOT seen the puzzle before.
  - Do NOT explain the puzzle. Do NOT give hints.
  - Record the session (screen + face cam if possible).
  - Have a stopwatch.

OBSERVATION SHEET (fill one per puzzle per tester):

Puzzle: _______________     Tester: _______________

TIME TO SOLVE: ___ minutes  (or DNF after ___ minutes)

FIRST ACTION:
  What did the player try first? _______________
  Was this the intended misdirection? [ ] Yes  [ ] No
  If no, what did they try instead? _______________

STUCK POINT(S):
  Where did they get stuck? _______________
  WHY were they stuck?
  [ ] Didn't understand the GOAL (clarity problem)
  [ ] Didn't see the CATCH (presentation problem)
  [ ] Saw the catch but couldn't find the REVELATION (difficulty problem)
  [ ] Found the revelation but couldn't EXECUTE it (mechanical problem)
  [ ] Other: _______________

SOLUTION MOMENT:
  How did the "aha" happen?
  [ ] Gradual — slowly worked toward it (systematic)
  [ ] Sudden — insight hit all at once (eureka)
  [ ] Accidental — stumbled into it (bad sign)
  [ ] Gave up — needed hint / quit (puzzle needs redesign)

EMOTIONAL READ:
  When they solved it, they seemed:
  [ ] Satisfied / proud ("I'm smart!")       ← GOAL
  [ ] Relieved ("finally, it's over")        ← Too hard or too tedious
  [ ] Neutral / nothing ("ok, next")         ← Too easy or no revelation
  [ ] Frustrated / tricked ("that's unfair") ← Unclear rules or leap of logic
  [ ] Amused / delighted ("oh, clever!")     ← EXCELLENT

INTERFACE ISSUES:
  Did the player struggle with anything NON-puzzle?
  [ ] Couldn't see connections between elements
  [ ] Didn't understand what an element does
  [ ] Thought the puzzle was broken / bugged
  [ ] Missed an interactive element entirely
  [ ] Spent time on execution, not thinking

NOTES: _______________________________________________
```

### Interpreting Results

```
IF most testers solve it with "eureka" + "satisfied/proud":
  → Puzzle is working. Ship it.

IF most testers solve it with "gradual" + "relieved":
  → Puzzle is too long or too tedious. Reduce step count.
    The catch might be fine but the execution is bloated.

IF most testers solve it with "accidental":
  → The solution space is too wide or the layout accidentally
    guides them to the answer. Tighten constraints.

IF most testers get stuck at "didn't see the catch":
  → Presentation problem. The puzzle elements don't communicate.
    Add feedback, visual connections, or simplify the layout.

IF most testers get stuck at "couldn't find the revelation":
  → Either the revelation is too obscure, or an earlier puzzle
    failed to teach the underlying mechanic. Check the vertical
    progression — did they learn the building blocks?

IF most testers feel "tricked":
  → The rules are unclear or the solution requires a logical leap
    not supported by the game's established mechanics.
    Either teach the mechanic earlier or change the solution.

IF testers have "interface issues":
  → These are separate from puzzle quality. Fix feedback, clarity,
    and interaction FIRST, then re-test the puzzle logic.
```
