# Ethical Engagement Implementation Patterns

Reusable patterns for building engagement systems that respect the player. Engine-agnostic pseudocode — adapt to your target language and engine.

## Table of Contents

1. [Pacing Manager](#1-pacing-manager)
2. [Novelty Scheduler](#2-novelty-scheduler)
3. [Foreshadowing System](#3-foreshadowing-system)
4. [Goal Layer Manager](#4-goal-layer-manager)
5. [Exponential Growth Curve](#5-exponential-growth-curve)
6. [Flow State Monitor](#6-flow-state-monitor)
7. [Productive Failure System](#7-productive-failure-system)
8. [Dark Pattern Detector](#8-dark-pattern-detector)

---

## 1. Pacing Manager

**Problem:** The game stays at one intensity level for too long, causing either exhaustion or boredom.

**Design intent:** Track what the player has been doing and gently steer toward variety. In linear games, validate level designs against pacing curves. In open worlds, ensure activity diversity is always available nearby.

```
class PacingManager:
    # DESIGN: Modulate intensity to prevent desensitization
    # The goal is a wave pattern — build, peak, breathe, build

    # Gameplay pillars — define what your game's core activities are
    enum Pillar { COMBAT, EXPLORATION, PUZZLE, NARRATIVE, TRAVERSAL, SOCIAL }

    current_pillar = null
    pillar_duration = 0.0               # How long player has been on this pillar
    pillar_fatigue = {}                  # Per-pillar fatigue accumulation
    fatigue_rate = 1.0                   # Points per second on one pillar
    fatigue_decay_rate = 0.5            # Recovery rate when NOT on that pillar
    fatigue_warning_threshold = 300.0    # 5 minutes — suggest rotation
    fatigue_critical_threshold = 600.0   # 10 minutes — strongly suggest rotation

    # Intensity tracking
    intensity_history = []               # Rolling window of intensity samples
    intensity_sample_rate = 5.0          # Sample every 5 seconds
    sustained_high_threshold = 120.0     # 2 min sustained high = needs a valley
    sustained_low_threshold = 180.0      # 3 min sustained low = needs a ramp

    func update(delta):
        update_pillar_fatigue(delta)
        sample_intensity(delta)
        check_pacing_health()

    func update_pillar_fatigue(delta):
        if current_pillar:
            pillar_fatigue[current_pillar] += fatigue_rate * delta
            pillar_duration += delta
        # Decay fatigue for inactive pillars
        for pillar in Pillar:
            if pillar != current_pillar:
                pillar_fatigue[pillar] = max(0, 
                    pillar_fatigue[pillar] - fatigue_decay_rate * delta)

    func on_pillar_changed(new_pillar):
        # DESIGN: Switching pillars is itself a micro-novelty moment.
        # The transition should feel good — a door opening to a new area,
        # a cutscene bridge, a change in music.
        current_pillar = new_pillar
        pillar_duration = 0.0

    func check_pacing_health():
        # Check for pillar fatigue
        if current_pillar and pillar_fatigue[current_pillar] > fatigue_critical_threshold:
            emit_signal("pacing_warning", {
                type: "PILLAR_FATIGUE",
                pillar: current_pillar,
                duration: pillar_duration,
                message: f"Player has been in {current_pillar} for {pillar_duration:.0f}s. "
                       + "Consider transitioning to a different activity."
            })

        # Check for sustained intensity
        if get_sustained_high_duration() > sustained_high_threshold:
            emit_signal("pacing_warning", {
                type: "SUSTAINED_HIGH",
                message: "High intensity for over 2 minutes. "
                       + "Schedule a recovery beat — exploration, narrative, or safe zone."
            })

        if get_sustained_low_duration() > sustained_low_threshold:
            emit_signal("pacing_warning", {
                type: "SUSTAINED_LOW",
                message: "Low intensity for over 3 minutes. "
                       + "Player may be getting bored. Introduce a challenge or novelty."
            })

    func get_freshest_pillar():
        # Returns the pillar with the lowest fatigue — best for rotation
        return min(pillar_fatigue, key=lambda p: pillar_fatigue[p])

    # === For level designers / debug tools ===
    func visualize_pacing_curve():
        # Outputs an intensity-over-time graph for playtesting review
        # Plot intensity_history as a line graph
        # Overlay pillar changes as colored bands
        # Mark sustained-high and sustained-low warnings
        return generate_pacing_chart(intensity_history, pillar_changes)
```

**Key details:**
- In linear games, use this during playtesting to validate that your authored pacing curve matches the actual player experience.
- In open worlds, use `get_freshest_pillar()` to bias NPC hints, quest markers, or random events toward activities the player hasn't done recently.
- The thresholds are starting points. Some games (horror) sustain tension longer by design. Tune to your genre.

---

## 2. Novelty Scheduler

**Problem:** The game introduces everything upfront and then coasts, or novelty is unevenly distributed.

**Design intent:** Track when the last "new thing" happened and ensure novelty is distributed throughout the experience, not front-loaded.

```
class NoveltyScheduler:
    # DESIGN: Players are novelty-seeking. New elements create forward pull.
    # Distribute new content throughout the game, not just the tutorial.

    novelty_events = []              # Log of {time, type, description}
    target_interval_minutes = 12.0   # New element every ~12 minutes of play
    warning_multiplier = 1.5         # Warn at 1.5x target (18 min with no novelty)
    total_play_time = 0.0

    # Types of novelty — any of these "count"
    enum NoveltyType {
        NEW_ENEMY,        # A type the player hasn't seen before
        NEW_MECHANIC,     # A new ability, tool, or interaction
        NEW_ENVIRONMENT,  # A visually/mechanically distinct area
        NEW_NARRATIVE,    # A significant story revelation
        NEW_TWIST,        # A subversion of established mechanics
        NEW_CHALLENGE,    # A challenge type not previously seen
    }

    func register_novelty(type, description):
        novelty_events.add({
            time: total_play_time,
            type: type,
            description: description
        })

    func update(delta):
        total_play_time += delta
        check_novelty_gap()

    func check_novelty_gap():
        if novelty_events.is_empty:
            gap = total_play_time
        else:
            gap = total_play_time - novelty_events.last.time

        gap_minutes = gap / 60.0

        if gap_minutes > target_interval_minutes * warning_multiplier:
            emit_signal("novelty_warning", {
                gap_minutes: gap_minutes,
                message: f"No new element introduced in {gap_minutes:.0f} minutes. "
                       + "Player curiosity may be waning. Consider introducing: "
                       + suggest_novelty_type()
            })

    func suggest_novelty_type():
        # Suggest the least-recently-used novelty type
        type_last_seen = {}
        for event in novelty_events:
            type_last_seen[event.type] = event.time
        
        # Find which type hasn't appeared in the longest time
        stalest = null
        stalest_time = total_play_time
        for type in NoveltyType:
            last = type_last_seen.get(type, 0)
            if last < stalest_time:
                stalest = type
                stalest_time = last
        
        return stalest

    # === For designers: audit the novelty distribution ===
    func generate_novelty_report():
        report = {
            total_events: novelty_events.length,
            average_gap_minutes: calculate_average_gap() / 60.0,
            longest_gap_minutes: calculate_longest_gap() / 60.0,
            type_distribution: count_by_type(),
            timeline: novelty_events
        }
        
        # Flag front-loading
        first_half_events = [e for e in novelty_events if e.time < total_play_time / 2]
        second_half_events = [e for e in novelty_events if e.time >= total_play_time / 2]
        
        if len(first_half_events) > len(second_half_events) * 2:
            report.warnings.add(
                "FRONT_LOADED: The first half of the game has 2x+ more novelty than "
                + "the second half. Players may feel the game 'runs out of ideas.'")
        
        return report
```

**Key details:**
- 12 minutes is a starting point borrowed from good linear games. Open-world games can go longer if exploration itself feels novel.
- Novelty doesn't have to be *big*. A new enemy variant, a weapon mod, a biome change, a plot twist, a new NPC personality — all count.
- Front-loading detection is critical. Many games teach 90% of their mechanics in hour one and coast for hours 2-20.

---

## 3. Foreshadowing System

**Problem:** Players don't feel pulled forward because there's nothing to wonder about.

**Design intent:** Scatter "mental itches" throughout the game — things the player notices, can't interact with yet, and will remember when they finally can. This creates forward pull through curiosity, not obligation.

```
class ForeshadowingManager:
    # DESIGN: Show players things they can't have yet.
    # The locked door they'll open in 3 hours. The ledge they'll reach
    # when they unlock double-jump. The NPC who says "come back later."
    # These create a pull toward the future that's entirely player-driven.

    planted_hooks = {}      # hook_id → {description, planted_at, resolved, area}
    resolved_hooks = {}

    func plant_hook(hook_id, description, area, resolution_hint=null):
        # DESIGN: A hook is anything that creates a question in the player's mind
        planted_hooks[hook_id] = {
            description: description,       # "Locked red door in the tower"
            area: area,                     # Where the player saw it
            planted_at: current_play_time,
            resolved: false,
            resolution_hint: resolution_hint  # Optional: "Find the red key"
        }
        # DESIGN: The plant moment should be noticeable but not intrusive.
        # A locked door the player walks past naturally. A glimpse of an area
        # below them. NOT a popup saying "YOU'LL COME BACK HERE LATER!"

    func resolve_hook(hook_id):
        if hook_id in planted_hooks:
            hook = planted_hooks[hook_id]
            hook.resolved = true
            hook.resolved_at = current_play_time
            resolved_hooks[hook_id] = hook
            
            wait_time = hook.resolved_at - hook.planted_at
            # DESIGN: The resolution should acknowledge the wait.
            # "Remember that locked door? HERE'S what's behind it."
            # The longer the wait, the bigger the payoff should feel.
            emit_signal("hook_resolved", {
                hook: hook,
                wait_minutes: wait_time / 60.0
            })

    func get_active_hooks():
        return [h for h in planted_hooks.values() if not h.resolved]

    func get_nearby_hooks(player_area):
        # For quest hints or map markers — show unresolved hooks near the player
        return [h for h in get_active_hooks() if h.area == player_area]

    # === Audit tool ===
    func check_hook_health():
        active = get_active_hooks()
        warnings = []

        if len(active) == 0:
            warnings.add("NO_ACTIVE_HOOKS: Player has nothing to wonder about. "
                        + "Plant some foreshadowing — a locked door, a mysterious NPC, "
                        + "a glimpsed area they can't reach yet.")

        if len(active) > 8:
            warnings.add("TOO_MANY_HOOKS: Player has 8+ unresolved mysteries. "
                        + "Risk of confusion or forgetting. Resolve some soon.")

        for hook in active:
            age_minutes = (current_play_time - hook.planted_at) / 60.0
            if age_minutes > 180:  # 3 hours unresolved
                warnings.add(f"STALE_HOOK: '{hook.description}' planted {age_minutes:.0f} "
                           + "minutes ago and still unresolved. Player may have forgotten it. "
                           + "Consider a reminder or resolve it soon.")

        return warnings
```

**Key details:**
- The sweet spot is 3-5 active hooks at any time. Fewer and there's no pull. More and the player loses track.
- Hooks should resolve in a satisfying order — don't plant 5 hooks and resolve them all at once. Stagger.
- The best hooks are ones the player discovers naturally (walking past a locked door) rather than ones you point at (an NPC saying "you should come back").

---

## 4. Goal Layer Manager

**Problem:** Players feel directionless (no clear goals) or overwhelmed (too many goals).

**Design intent:** Always maintain at least one short-term and one long-term goal. When one completes, the next is immediately visible. Never leave the player with zero goals or too many competing goals.

```
class GoalLayerManager:
    # DESIGN: Players need both "what am I doing RIGHT NOW" and
    # "what am I working TOWARD." Both layers must always be populated.

    enum GoalScale { IMMEDIATE, SHORT, MEDIUM, LONG, ASPIRATIONAL }
    # IMMEDIATE: seconds (reach that ledge, kill that enemy)
    # SHORT: minutes (clear this room, finish this quest step)
    # MEDIUM: session (complete this quest chain, build this structure)
    # LONG: multi-session (story arc, major unlock, zone completion)
    # ASPIRATIONAL: endgame (100% completion, max level, true ending)

    active_goals = {}    # scale → [goal]
    completed_goals = []

    func add_goal(goal_id, scale, description, visible=true):
        goal = {
            id: goal_id,
            scale: scale,
            description: description,
            visible: visible,        # Some goals are implicit, not shown in UI
            added_at: current_time,
            progress: 0.0
        }
        active_goals[scale].add(goal)
        
        if visible:
            update_goal_ui()

    func complete_goal(goal_id):
        goal = find_goal(goal_id)
        completed_goals.add(goal)
        active_goals[goal.scale].remove(goal)
        
        # DESIGN: Completion should feel good. Brief celebration UI,
        # satisfying sound, maybe a camera moment. Scale the celebration
        # to the goal's scale — don't fanfare an IMMEDIATE goal,
        # but a LONG goal deserves a proper reward beat.
        emit_signal("goal_completed", goal)
        
        # Check if this creates a gap
        check_goal_layers()

    func check_goal_layers():
        warnings = []
        
        # Must always have at least one SHORT and one LONG/MEDIUM
        has_short = len(active_goals[SHORT]) > 0 or len(active_goals[IMMEDIATE]) > 0
        has_long = (len(active_goals[LONG]) > 0 or 
                   len(active_goals[MEDIUM]) > 0 or 
                   len(active_goals[ASPIRATIONAL]) > 0)

        if not has_short:
            warnings.add({
                type: "NO_SHORT_TERM",
                message: "Player has no immediate objective. They may feel directionless. "
                       + "Provide a clear next step — a waypoint, a prompt, or a new task."
            })

        if not has_long:
            warnings.add({
                type: "NO_LONG_TERM",
                message: "Player has no overarching goal to work toward. "
                       + "Introduce a long-term aspiration — a quest chain, a major unlock, "
                       + "or a visible milestone on the horizon."
            })

        # Check for goal overload
        total_visible = sum(len([g for g in goals if g.visible]) 
                          for goals in active_goals.values())
        if total_visible > 6:
            warnings.add({
                type: "GOAL_OVERLOAD",
                message: f"Player has {total_visible} visible active goals. "
                       + "Risk of decision paralysis. Consider hiding or deprioritizing some."
            })

        return warnings

    # === Fantasy preview — show the player their future ===
    func get_upcoming_unlocks(current_progress):
        # DESIGN: Skill tree previews, equipment previews, blueprint systems
        # Let the player SEE what they're working toward.
        # "When I get to level 15, I'll unlock..." is powerful motivation.
        upcoming = []
        for unlock in all_unlocks:
            if unlock.requirement > current_progress:
                upcoming.add({
                    unlock: unlock,
                    distance: unlock.requirement - current_progress,
                    preview: unlock.preview_description
                })
        return sorted(upcoming, key=lambda u: u.distance)[:5]
        # DESIGN: Show the next ~5 unlocks. Not all of them.
        # Too many and the player can't focus. Too few and there's no horizon.
```

---

## 5. Exponential Growth Curve

**Problem:** Progression feels flat — the player isn't getting noticeably more powerful or capable over time.

**Design intent:** Design growth so small early successes compound into dramatic late-game capability. The player should look back at their early game and think "I can't believe I used to do it that way."

```
class GrowthCurve:
    # DESIGN: Stardew Valley progression model
    # Early: Hand-water 15 crops. Slow, tedious, but manageable.
    # Mid: Sprinklers water 8 crops each. Player designs efficient layouts.
    # Late: Iridium sprinklers + greenhouse. The farm runs itself.
    # The ARC is: manual drudgery → earned automation → mastery/optimization

    func calculate_effective_power(base_power, multipliers):
        # Multiplicative scaling creates the exponential feel
        # Each upgrade MULTIPLIES rather than adds
        total = base_power
        for mult in multipliers:
            total *= mult.value
        return total
        # DESIGN: Additive upgrades (+5 damage, +5 damage, +5 damage) feel flat.
        # Multiplicative upgrades (x1.5 damage, then x1.5 again = x2.25) feel exponential.
        # Mix both, but the exciting upgrades should be multiplicative.

    # === Automation milestone tracker ===
    # DESIGN: Each tier should make the PREVIOUS tier's tedium disappear
    automation_tiers = [
        { name: "Manual",     description: "Player does everything by hand" },
        { name: "Tools",      description: "Player has tools that speed up tasks" },
        { name: "Helpers",    description: "NPCs or machines handle routine tasks" },
        { name: "Systems",    description: "Interconnected automation runs the basics" },
        { name: "Mastery",    description: "Player optimizes the optimization" }
    ]

    func get_current_tier(player_progress):
        # Each tier should take roughly 2x as long to reach as the previous
        # But the POWER gained should be more than 2x
        # This creates the "acceleration" feel
        for tier in reversed(automation_tiers):
            if player_progress >= tier.threshold:
                return tier

    func calculate_time_saved(current_tier, task):
        # DESIGN: Show the player how much time their upgrades save
        # "Sprinklers saved you 45 seconds this morning!" 
        # This reinforces the value of their progression
        manual_time = task.base_duration
        current_time = task.base_duration * current_tier.speed_multiplier
        return manual_time - current_time
```

---

## 6. Flow State Monitor

**Problem:** Players are either bored (too easy) or frustrated (too hard) and you can't tell which.

**Design intent:** Track behavioral signals that indicate flow, boredom, or frustration, and surface them for dynamic difficulty adjustment or playtesting analysis.

```
class FlowStateMonitor:
    # DESIGN: Flow = skill matches challenge. We can't read the player's
    # mind, but we can read their behavior for signals.

    enum FlowState { BORED, FLOW, ANXIOUS, FRUSTRATED }

    # Signal tracking
    recent_deaths = []              # Timestamps of deaths
    recent_kills = []               # Timestamps of kills
    idle_time = 0.0                 # Time not providing input
    reaction_times = []             # How fast player responds to threats
    health_history = []             # Rolling health percentage
    retry_count = 0                 # Times retrying same section

    # Thresholds (tune per game)
    deaths_per_minute_frustrated = 0.5    # Dying every 2 minutes
    idle_seconds_bored = 15.0             # 15s no input = likely bored
    retry_limit_frustrated = 3            # 3+ retries = likely frustrated

    func update(delta):
        prune_old_data()
        idle_time += delta  # Reset on any input

    func on_player_input():
        idle_time = 0.0

    func on_player_death():
        recent_deaths.add(current_time)
        retry_count += 1

    func on_section_changed():
        retry_count = 0

    func estimate_flow_state():
        death_rate = len(recent_deaths) / max(1, time_window_minutes)
        avg_health = average(health_history)

        # FRUSTRATED: Dying a lot, retrying repeatedly
        if death_rate > deaths_per_minute_frustrated or retry_count >= retry_limit_frustrated:
            return FRUSTRATED

        # ANXIOUS: Barely surviving, low health, slow reactions
        if avg_health < 0.25 and death_rate > 0.2:
            return ANXIOUS

        # BORED: Long idle times, high health, not dying
        if idle_time > idle_seconds_bored or (avg_health > 0.9 and death_rate == 0):
            return BORED

        # FLOW: Active input, moderate health, occasional deaths
        return FLOW

    func get_difficulty_adjustment():
        state = estimate_flow_state()
        match state:
            FRUSTRATED:
                return {
                    enemy_damage_mult: 0.85,
                    health_pickup_mult: 1.3,
                    hint_enabled: true,
                    # DESIGN: Subtle. Never tell the player. If they knew,
                    # the help would feel patronizing instead of invisible.
                    message: "Player struggling. Quietly reducing difficulty."
                }
            BORED:
                return {
                    enemy_damage_mult: 1.15,
                    spawn_rate_mult: 1.2,
                    # DESIGN: Also subtle. Don't punish boredom — add spice.
                    message: "Player coasting. Increasing challenge slightly."
                }
            ANXIOUS:
                return {
                    # Anxious is close to flow — nudge gently
                    health_pickup_mult: 1.1,
                    message: "Player anxious but engaged. Minor assist."
                }
            FLOW:
                return {
                    # Don't touch anything — the player is in the zone
                    message: "Player in flow. No adjustment needed."
                }
```

---

## 7. Productive Failure System

**Problem:** Death feels like wasted time instead of a learning experience.

**Design intent:** Every death should give the player something — knowledge, progress, or at minimum an interesting story. The loop is: die → learn → apply → succeed → feel smart.

```
class ProductiveFailure:
    # DESIGN: Roguelike/Souls model — death teaches, not just punishes

    # Knowledge persistence — what carries through death
    revealed_map_areas = {}       # Map knowledge persists
    enemy_bestiary = {}           # Enemies the player has encountered
    discovered_secrets = {}       # Puzzle solutions, hidden paths

    # Metagame progression — tangible upgrades across runs
    permanent_unlocks = []        # New items in the starting pool
    currency_bank = 0             # Currency earned during run, partially kept

    death_retention_rate = 0.3    # Keep 30% of currency on death

    func on_player_death(run_data):
        # 1. Preserve knowledge
        revealed_map_areas.merge(run_data.explored_areas)
        enemy_bestiary.merge(run_data.encountered_enemies)
        discovered_secrets.merge(run_data.found_secrets)

        # 2. Partial currency retention
        retained = floor(run_data.currency * death_retention_rate)
        currency_bank += retained
        
        # 3. Check for milestone unlocks
        check_unlock_thresholds(run_data)
        
        # 4. Generate the "what you learned" summary
        death_summary = generate_death_summary(run_data)
        
        # DESIGN: The death screen is a TEACHING moment, not a shame moment.
        # Show what the player accomplished, what they learned, and
        # what's different for next time.
        show_death_screen(death_summary)

    func generate_death_summary(run_data):
        summary = {
            # Celebrate progress, even in failure
            "furthest_reached": run_data.furthest_area,
            "enemies_defeated": run_data.total_kills,
            "time_survived": run_data.duration,
            
            # Show what's new
            "new_discoveries": run_data.first_time_encounters,
            "new_map_areas": run_data.newly_explored,
            
            # Hint at what to try differently
            "cause_of_death": run_data.death_cause,
            "tip": generate_contextual_tip(run_data.death_cause),
            
            # Show tangible carry-over
            "currency_retained": retained,
            "unlocks_progress": get_next_unlock_progress()
        }
        # DESIGN: "You made it further than last time!"
        # "New enemy discovered: Fire Elemental — weak to ice."
        # "Tip: The boss's slam attack has a 2-second telegraph."
        # These turn death into INFORMATION, not just loss.
        return summary

    func generate_contextual_tip(death_cause):
        # DESIGN: Tips should be specific to HOW the player died,
        # not generic "try harder" advice.
        tips = {
            "boss_slam":   "The boss raises both arms before slamming. Dodge sideways.",
            "poison_area":  "Poison zones have visible green mist. Craft antidotes at a workbench.",
            "fall_damage":  "Look for vines or ledges to break long falls.",
            "swarmed":      "Fighting in narrow corridors limits how many enemies can reach you."
        }
        return tips.get(death_cause, null)
```

---

## 8. Dark Pattern Detector

**Problem:** Well-intentioned engagement features can drift into manipulative territory during development.

**Design intent:** A diagnostic tool that flags when a system resembles known dark patterns, prompting the designer to consciously choose whether to proceed.

```
class DarkPatternDetector:
    # DESIGN: This is a design review tool, not a hard block.
    # Flag potential issues and let the designer decide.

    func audit_system(system_description):
        flags = []

        # === Loss Aversion / FOMO ===
        if system_has_expiring_rewards(system_description):
            flags.add({
                pattern: "FOMO / EXPIRING REWARDS",
                severity: "HIGH",
                description: "Rewards that expire if not claimed create anxiety about "
                           + "NOT playing, rather than excitement about playing.",
                examples: "Daily login streaks, timed event rewards, decaying resources.",
                alternative: "Make rewards accumulate while offline (rest bonus model). "
                           + "Or make them permanent unlocks with no time pressure."
            })

        # === Artificial Time Gates ===
        if system_has_time_gates(system_description):
            flags.add({
                pattern: "ARTIFICIAL TIME GATE",
                severity: "MEDIUM",
                description: "Forcing the player to wait real-world time (not gameplay time) "
                           + "for progress feels manipulative unless the wait serves a "
                           + "genuine design purpose.",
                examples: "Energy systems, construction timers, crop growth timers.",
                alternative: "If the wait serves pacing (crops in Stardew Valley), it's fine. "
                           + "If it serves monetization (pay to skip), reconsider. "
                           + "Ask: would the player CHOOSE to wait if skipping were free?"
            })

        # === Variable Ratio Reinforcement ===
        if system_has_random_rewards(system_description):
            flags.add({
                pattern: "VARIABLE RATIO REINFORCEMENT",
                severity: "MEDIUM",
                description: "Random reward schedules (loot boxes, gacha, random drops) "
                           + "are the most addictive reinforcement pattern. "
                           + "They work, but they work the same way slot machines work.",
                alternative: "Use guaranteed progression with random bonus on top. "
                           + "Bad: 'open box, maybe get rare item.' "
                           + "Better: 'open box, always get materials + chance at rare item.' "
                           + "Best: 'earn rare item at milestone 10. Boxes give cosmetic extras.'"
            })

        # === Sunk Cost Exploitation ===
        if system_punishes_quitting(system_description):
            flags.add({
                pattern: "SUNK COST EXPLOITATION",
                severity: "HIGH",
                description: "Systems that make the player feel they'll 'waste' prior "
                           + "investment by stopping are manipulative.",
                examples: "Streaks that reset, seasonal progress that vanishes, "
                        + "ranked decay during absence.",
                alternative: "Let progress be permanent. If you must reset, "
                           + "give a meaningful reward for the completed cycle."
            })

        # === Infinite Loops Without Stopping Points ===
        if system_lacks_natural_endpoints(system_description):
            flags.add({
                pattern: "NO NATURAL STOPPING POINT",
                severity: "LOW",
                description: "If there's no natural moment to stop playing, the player "
                           + "keeps going past enjoyment into compulsion.",
                examples: "Infinite scroll feeds, auto-play next level, no save points.",
                alternative: "Create deliberate session boundaries. Post-level summary screens. "
                           + "Save prompts. 'Good stopping point' indicators. "
                           + "The player should close the game feeling SATISFIED, not guilty."
            })

        return flags

    # === Ethics check shorthand ===
    func quick_ethics_check(system_name):
        checks = {
            "player_thanked":  "Would the player thank you for this system if they "
                             + "understood exactly how it works?",
            "time_respected":  "Does this create genuine value for the player's time, "
                             + "or artificial time sinks?",
            "stop_feeling":    "Can the player stop playing and feel GOOD about it?",
            "intrinsic":       "Is the engagement intrinsic (fun) or extrinsic (fear of loss)?"
        }
        return checks
        # DESIGN: If any answer is "no," the system needs redesign.
        # The designer can override, but they should do it consciously.
```

**Key details:**
- This is a development tool, not a runtime system. Run it during design reviews.
- The severity levels are starting points. Some "MEDIUM" flags are fine in context (random loot in a single-player game is different from random loot in a monetized multiplayer game).
- The alternatives are suggestions, not mandates. The goal is to make designers *aware* of the pattern and choose consciously.
