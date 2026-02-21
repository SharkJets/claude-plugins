# Game AI Implementation Patterns

This file contains reusable implementation patterns for common game AI systems. These are engine-agnostic pseudocode patterns — adapt to your target language and engine.

## Table of Contents

1. [Simultaneous Attacker Limiter](#1-simultaneous-attacker-limiter)
2. [Detection Grace Period](#2-detection-grace-period)
3. [AI Bark / Telegraph System](#3-ai-bark--telegraph-system)
4. [Player Habit Tracker](#4-player-habit-tracker)
5. [Dynamic Tension Director](#5-dynamic-tension-director)
6. [Consistent-but-Varied Response System](#6-consistent-but-varied-response-system)
7. [Independent AI Schedule System](#7-independent-ai-schedule-system)
8. [Behavior Tree Template](#8-behavior-tree-template)

---

## 1. Simultaneous Attacker Limiter

**Problem:** When many enemies can attack at once, the player feels overwhelmed unfairly.

**Design intent:** Only N enemies actively engage at a time. Others reposition, taunt, or circle — creating the *illusion* of being surrounded while keeping damage manageable. The player feels outnumbered but not helpless.

```
class AttackerManager:
    max_simultaneous_attackers = 3
    active_attackers = []
    waiting_queue = []

    func request_attack_slot(enemy):
        if active_attackers.count < max_simultaneous_attackers:
            active_attackers.add(enemy)
            return GRANTED
        else:
            waiting_queue.add(enemy)
            enemy.set_behavior(CIRCLE_OR_TAUNT)  # Don't just stand idle
            return DENIED

    func release_attack_slot(enemy):
        active_attackers.remove(enemy)
        if waiting_queue.not_empty:
            next = waiting_queue.pop_front()
            active_attackers.add(next)
            next.set_behavior(ENGAGE)

    # Scale max attackers with difficulty or player skill
    func adjust_for_difficulty(level):
        max_simultaneous_attackers = clamp(2 + level, 2, 6)
```

**Key details:**
- Enemies without attack slots should still *do things* — circle, take cover, shout threats, reload. Standing idle breaks the illusion.
- Consider proximity-based priority: closer enemies get slots first.
- Scale `max_simultaneous_attackers` with difficulty, player count (co-op), or arena size.

---

## 2. Detection Grace Period

**Problem:** Instant detection feels unfair. The player gets no chance to react.

**Design intent:** When an enemy spots the player, there's a brief window where the enemy is "processing" — this gives the player time to duck back into cover, creating tense near-miss moments instead of instant failure.

```
class DetectionSystem:
    detection_threshold = 1.0
    detection_rate = 0.4          # How fast the meter fills per second
    grace_period = 0.8            # Seconds of 0% accuracy after full detection
    current_detection = 0.0
    grace_timer = 0.0

    func update(delta, can_see_player):
        if can_see_player:
            current_detection += detection_rate * delta

            if current_detection >= detection_threshold:
                trigger_alert()
                grace_timer = grace_period
        else:
            # Detection decays when line of sight is lost
            current_detection = max(0, current_detection - detection_rate * 0.5 * delta)

    func trigger_alert():
        play_bark("I see you!")           # Telegraph! (Principle 3)
        play_animation("alert_stance")     # Visual feedback
        transition_to_combat_state()

    func get_accuracy_modifier():
        if grace_timer > 0:
            grace_timer -= delta
            return 0.0   # Can't hit player during grace period
        return 1.0

    # Expose detection level for UI (vision cone fill, alert meter)
    func get_detection_percentage():
        return current_detection / detection_threshold
```

**Key details:**
- The detection *meter* is great for stealth UI — a filling ring or changing vision cone color.
- Grace period should be invisible to the player. They just perceive that they barely escaped.
- Detection rate can vary by enemy type: basic guards are slow, elite enemies detect fast.
- Consider modifiers: darkness reduces detection rate, running increases it.

---

## 3. AI Bark / Telegraph System

**Problem:** AI makes smart decisions, but the player doesn't know it.

**Design intent:** Every significant AI state transition or tactical decision produces audible/visible feedback, so the player can read the battlefield and respond to what enemies are doing.

```
class BarkSystem:
    bark_cooldown = 2.0           # Prevent overlapping barks
    max_simultaneous_barks = 2    # Don't overwhelm with audio
    last_bark_time = 0.0
    bark_priority_queue = []

    # Define barks per AI action — the player learns to read these
    bark_map = {
        FLANKING:     ["Going around!", "Flanking!"],
        RELOADING:    ["Reloading!", "Cover me!"],
        GRENADE:      ["Frag out!", "Grenade!"],
        RETREATING:   ["Falling back!", "Too hot!"],
        SPOTTED:      ["Contact!", "I see one!"],
        INVESTIGATING: ["What was that?", "Did you hear that?"],
        SEARCHING:    ["Where'd they go?", "Come out..."],
        CALLING_BACKUP: ["Need backup!", "All units!"]
    }

    func request_bark(enemy, action, priority):
        if time_since(last_bark_time) < bark_cooldown:
            if priority <= current_priority:
                return  # Skip low-priority bark during cooldown

        bark_text = bark_map[action].random()
        play_bark(enemy, bark_text)
        play_contextual_animation(enemy, action)  # Point, gesture, etc.
        last_bark_time = current_time

    func play_bark(enemy, text):
        # Spatial audio — player hears nearby enemies louder
        play_3d_audio(enemy.position, get_voice_clip(text))
        # Optional: show subtitle if enemy is on-screen
        if enemy.is_on_screen():
            show_subtitle(enemy, text, duration=1.5)
```

**Key details:**
- Barks should happen *before* the action, not during — they're warnings, not narration.
- Use a priority system so critical barks (grenade!) override casual ones (searching).
- Give different enemy types different voice styles — the player learns to identify threats by sound.
- Subtitles/captions are an accessibility win and also help players in noisy environments.

---

## 4. Player Habit Tracker

**Problem:** The player finds one dominant strategy and the game becomes trivial.

**Design intent:** Track the player's tendencies over time and gradually shift AI behavior to counter them, keeping the gameplay fresh. Changes should feel like the world adapting, not the game punishing.

```
class PlayerHabitTracker:
    # Track frequencies of player behaviors
    habits = {
        "headshots": 0,
        "stealth_kills": 0,
        "night_attacks": 0,
        "explosives": 0,
        "melee": 0,
        "same_entry_point": 0
    }
    total_encounters = 0
    adaptation_threshold = 0.4   # Adapt when a tactic exceeds 40% usage

    func record_kill(kill_data):
        total_encounters += 1
        if kill_data.was_headshot: habits["headshots"] += 1
        if kill_data.was_stealth: habits["stealth_kills"] += 1
        if kill_data.time_of_day == NIGHT: habits["night_attacks"] += 1
        # ... etc

    func get_adaptations():
        adaptations = []
        if total_encounters < 10:
            return adaptations  # Don't adapt too early

        for habit, count in habits:
            ratio = count / total_encounters
            if ratio > adaptation_threshold:
                adaptations.add(ADAPTATION_MAP[habit])
        return adaptations

    # Map habits to world-visible countermeasures
    ADAPTATION_MAP = {
        "headshots":      ADD_HELMETS,         # Visual — player sees the change
        "stealth_kills":  PATROL_IN_PAIRS,     # Behavioral change
        "night_attacks":  EQUIP_FLASHLIGHTS,   # Environmental feedback
        "explosives":     SPREAD_FORMATION,     # Tactical counter
        "melee":          MAINTAIN_DISTANCE     # Positioning change
    }
```

**Key details:**
- Adaptations must be **visible** to the player. If you add helmets, the player needs to *see* the helmets and understand why headshots stopped working. Consider a radio chatter line: "HQ says wear your helmets — this one's a sharpshooter."
- Don't adapt too aggressively — the player should still feel their chosen playstyle is viable, just not the *only* viable approach.
- Consider a cooldown or max adaptation level so the game doesn't become impossible.
- Reset or decay habits over time so the player can shift strategies.

---

## 5. Dynamic Tension Director

**Problem:** Constant intensity leads to fatigue, not excitement.

**Design intent:** Monitor the player's stress level and modulate encounter intensity to create satisfying waves of tension and release. Think rollercoaster, not treadmill.

```
class TensionDirector:
    # Player stress estimation (0.0 = calm, 1.0 = max stress)
    player_stress = 0.0

    # Pacing targets
    target_high_tension = 0.8
    target_low_tension = 0.3
    current_phase = BUILDING       # BUILDING, PEAK, RELEASE, CALM

    func update(delta):
        estimate_player_stress()
        update_phase()
        apply_phase_modifiers()

    func estimate_player_stress():
        # Combine multiple signals
        health_factor = 1.0 - (player.health / player.max_health)
        ammo_factor = 1.0 - (player.ammo / player.max_ammo)
        combat_recency = time_since_last_combat < 10.0 ? 0.5 : 0.0
        enemy_count_factor = nearby_enemies.count / 8.0

        player_stress = clamp(
            health_factor * 0.3 +
            ammo_factor * 0.2 +
            combat_recency * 0.3 +
            enemy_count_factor * 0.2,
            0.0, 1.0
        )

    func update_phase():
        match current_phase:
            BUILDING:
                if player_stress >= target_high_tension:
                    current_phase = PEAK
                    peak_timer = rand_range(15, 30)  # Hold peak for a bit
            PEAK:
                peak_timer -= delta
                if peak_timer <= 0 or player_stress >= 0.95:
                    current_phase = RELEASE
            RELEASE:
                if player_stress <= target_low_tension:
                    current_phase = CALM
                    calm_timer = rand_range(20, 45)  # Let them breathe
            CALM:
                calm_timer -= delta
                if calm_timer <= 0:
                    current_phase = BUILDING

    func apply_phase_modifiers():
        match current_phase:
            BUILDING:  set_spawn_rate(NORMAL); set_aggression(NORMAL)
            PEAK:      set_spawn_rate(HIGH); set_aggression(HIGH)
            RELEASE:   set_spawn_rate(LOW); set_aggression(LOW)
            CALM:      set_spawn_rate(NONE); set_aggression(PASSIVE)
```

**Key details:**
- This works best for games with dynamic encounters (horde shooters, survival games). For authored levels, you can use a lighter version that just adjusts aggression.
- The calm phase is critical. Players need time to explore, loot, and feel safe before the next wave.
- Log the phase transitions for playtesting — if the game never reaches CALM, something is wrong.
- Consider exposing a `tension_override` for scripted sequences or boss encounters.

---

## 6. Consistent-but-Varied Response System

**Problem:** Predictable AI feels exploitable; random AI feels unfair.

**Design intent:** Make the *trigger* deterministic and the *execution* varied. The player can always rely on *what* the enemy will do, but *how* they do it keeps things fresh.

```
class ConsistentResponse:
    # The trigger is ALWAYS the same — 100% reliable
    func on_stimulus(stimulus):
        match stimulus:
            HEARD_NOISE:      investigate(stimulus.source)    # Always
            HEALTH_LOW:       flee()                          # Always
            SAW_DEAD_ALLY:    alert_and_search()              # Always
            GENERATOR_OFF:    go_check_generator()            # Always

    # The execution has variation
    func investigate(location):
        approach_path = choose_random_path_to(location)  # Different route each time
        investigation_time = rand_range(5, 15)           # Variable duration
        bark_variation = barks["investigating"].random()  # Different line

    func flee():
        # Always flees, but destination varies
        flee_target = find_nearest_from([
            nearest_ally,
            nearest_cover,
            nearest_exit,
            random_direction
        ], weights=[0.4, 0.3, 0.2, 0.1])
        run_to(flee_target)
```

**Key details:**
- The player should be able to predict the trigger→response mapping with 100% confidence.
- Variation in execution prevents rote memorization while maintaining planability.
- Document each stimulus→response pair clearly — this is a contract with the player.

---

## 7. Independent AI Schedule System

**Problem:** Enemies feel like they exist only to fight the player.

**Design intent:** Give NPCs autonomous routines that operate independently of the player, creating a lived-in world that the player disrupts by entering it.

```
class AISchedule:
    schedule = [
        { time: 0600, activity: WAKE_UP, location: barracks },
        { time: 0630, activity: EAT, location: mess_hall },
        { time: 0700, activity: PATROL, location: patrol_route_a },
        { time: 1200, activity: EAT, location: mess_hall },
        { time: 1300, activity: GUARD, location: gate_post },
        { time: 1800, activity: EAT, location: mess_hall },
        { time: 1900, activity: SOCIALIZE, location: common_area },
        { time: 2200, activity: SLEEP, location: barracks }
    ]

    func get_current_activity():
        for i in range(schedule.length - 1, -1, -1):
            if current_game_time >= schedule[i].time:
                return schedule[i]
        return schedule.last  # Wrap around

    func update():
        if is_in_combat or is_alerted:
            return  # Combat overrides schedule

        target_activity = get_current_activity()
        if current_activity != target_activity:
            transition_to(target_activity)

    # Flavor behaviors during activities
    func execute_activity(activity):
        match activity.type:
            EAT:        sit_at_table(); play_eating_anim(); chat_with_nearby()
            SOCIALIZE:  play_cards(); tell_jokes(); laugh()
            GUARD:      stand_at_post(); occasionally_yawn(); check_watch()
            SLEEP:      lie_in_bed(); snore()
```

**Key details:**
- Schedules make the world feel alive even when the player just watches.
- They also create tactical opportunities: attack during meal time when guards are clustered, or at night when patrols are thin.
- NPCs with schedules that get disrupted by the player's actions create emergent stories.
- Keep schedules simple — 5-8 activities per day is plenty.

---

## 8. Behavior Tree Template

A minimal, well-structured behavior tree skeleton that incorporates the design principles.

```
ROOT (Selector)
├── COMBAT (Sequence) [when: is_alerted AND has_target]
│   ├── Request attack slot from AttackerManager
│   ├── IF slot granted:
│   │   ├── Telegraph intention (bark + animation)    ← Principle 3
│   │   ├── Wait grace period if target just left cover ← Principle 2
│   │   ├── Execute attack (with accuracy modified by grace timer)
│   │   └── Release attack slot
│   └── IF slot denied:
│       ├── Reposition / take cover / taunt
│       └── Wait for slot
│
├── ALERT (Sequence) [when: stimulus detected]
│   ├── Play investigation bark                      ← Principle 3
│   ├── Always investigate stimulus source           ← Principle 4
│   ├── Search area for variable duration            ← Principle 6
│   └── Return to schedule or heightened patrol
│
├── ADAPTATION CHECK (Decorator) [periodic]
│   └── Query PlayerHabitTracker
│       └── Apply countermeasures if needed           ← Principle 6
│
├── ENVIRONMENTAL (Selector)
│   ├── Pick up nearby weapon if better than current  ← Principle 5
│   ├── Use nearby health pickup if injured           ← Principle 5
│   └── React to environmental hazard                 ← Principle 5
│
└── IDLE (Sequence) [default]
    └── Follow AISchedule                             ← Principle 8
        ├── Execute current scheduled activity
        └── Perform flavor behaviors
```

**Key details:**
- The tree structure naturally encodes priority: combat > alerts > adaptation > environment > idle.
- Each leaf node should have associated telegraphs/feedback.
- The behavior tree is a *framework* — not every game needs every branch. Start with Combat + Idle and add layers as needed.
- Test each branch independently before combining.
