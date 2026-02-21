# Player Protection Implementation Patterns

Reusable patterns for building systems that align player incentives with intended gameplay. Engine-agnostic pseudocode — adapt to your target language and engine.

## Table of Contents

1. [Push-Forward Health Loop](#1-push-forward-health-loop)
2. [Push-Forward Resource Loop](#2-push-forward-resource-loop)
3. [Rest Bonus / Positive Reframe System](#3-rest-bonus--positive-reframe-system)
4. [Style Meter / Combo Grading](#4-style-meter--combo-grading)
5. [Post-Level Grading Breakdown](#5-post-level-grading-breakdown)
6. [Soft Discouragement Manager](#6-soft-discouragement-manager)
7. [Dominant Strategy Detector](#7-dominant-strategy-detector)
8. [Fragility-Based Stealth Incentive](#8-fragility-based-stealth-incentive)

---

## 1. Push-Forward Health Loop

**Problem:** Players play cautiously, hiding behind cover and waiting to regenerate. Combat becomes a boring cycle of peek-shoot-hide.

**Design intent:** Health recovery is tied to aggressive action. The only way to survive is to push forward. Retreating is a death sentence. Think Doom's Glory Kills or Bloodborne's Rally.

```
class PushForwardHealth:
    # DESIGN: Health comes from aggression, not passivity (Principle 3)
    # This makes the fun playstyle (aggressive) also the smart playstyle (survival)
    
    rally_window = 3.0            # Seconds after taking damage where attacks heal
    rally_recovery_percent = 0.5  # Recover up to 50% of damage taken
    glory_kill_threshold = 0.3    # Enemy health % to become finishable
    glory_kill_heal = 25          # Flat HP from a glory kill

    # Track recent damage for rally mechanic
    recent_damage = 0
    rally_timer = 0

    func on_player_damaged(amount):
        recent_damage += amount
        rally_timer = rally_window
        # DESIGN: Visual feedback is critical — screen edge glow, 
        # heartbeat audio, enemy highlight to say "attack NOW to heal"

    func on_player_attacks_enemy(damage_dealt):
        if rally_timer > 0:
            # Recover portion of recently lost health by attacking back
            recovery = min(damage_dealt * 0.5, recent_damage * rally_recovery_percent)
            player.heal(recovery)
            show_rally_vfx()        # Green health numbers, satisfying sound
            # DESIGN: The feedback here should feel GOOD — this is the reward
            # for playing aggressively. Don't make it subtle.

    func update(delta):
        if rally_timer > 0:
            rally_timer -= delta
            if rally_timer <= 0:
                recent_damage = 0   # Window closed, recovery forfeited

    func check_glory_kill(enemy):
        if enemy.health_percent <= glory_kill_threshold:
            enemy.set_staggered(true)       # Visual: stumbling, glowing
            enemy.show_finisher_prompt()     # "Press X to finish"
            # DESIGN: Staggered enemies are bait — drawing the player
            # into close range where the real combat happens

    func execute_glory_kill(enemy):
        player.heal(glory_kill_heal)
        drop_ammo(enemy.position)           # Double incentive: health AND ammo
        play_glory_kill_animation(enemy)
        # DESIGN: The animation is a micro-reward break in the action
        # Also grants brief invincibility — the player feels powerful
```

**Key details:**
- The rally window should be *generous* — you want players to succeed at this, not fail. 3 seconds is a good starting point.
- Visual/audio feedback for rally heals should be extremely satisfying. This is the moment you're rewarding.
- Glory kill stagger state should be visually unmistakable. Players need to read it instantly mid-combat.
- Consider making glory kills grant brief invincibility or push back nearby enemies, so the player doesn't get punished for engaging.

---

## 2. Push-Forward Resource Loop

**Problem:** Players hoard resources (ammo, abilities, consumables) "for later" and never use them, making combat less interesting.

**Design intent:** Resources recharge through the behavior you want to encourage, creating a cycle where using resources aggressively is the path to getting more resources.

```
class ResourceRiskLoop:
    # DESIGN: Ammo/abilities tied to risky behavior (Principle 3)
    # Hyper Light Drifter model: ranged ammo recharges through melee
    
    max_ammo = 6
    current_ammo = 6
    ammo_per_melee_hit = 2
    
    ability_charge = 0.0
    charge_per_close_range_kill = 0.35    # ~3 kills to fully charge
    close_range_threshold = 5.0           # Distance in units

    func on_melee_hit(enemy):
        # DESIGN: Melee is high-risk (close range) but rewards with ranged ammo
        # This creates an oscillating rhythm: close → far → close → far
        current_ammo = min(max_ammo, current_ammo + ammo_per_melee_hit)
        show_ammo_restore_vfx()
        play_ammo_clink_sfx()
        # DESIGN: Satisfying audio/visual on resource gain reinforces the loop

    func on_ranged_shot():
        if current_ammo > 0:
            current_ammo -= 1
        # DESIGN: No ammo pickups in the world (or very rare ones).
        # The ONLY reliable source is melee. Player learns fast.

    func on_enemy_killed(enemy, distance):
        if distance <= close_range_threshold:
            ability_charge = min(1.0, ability_charge + charge_per_close_range_kill)
            # DESIGN: Abilities earned through dangerous play = power fantasy
            # Player narrative: "I earned this by being brave"

    func try_use_ability():
        if ability_charge >= 1.0:
            ability_charge = 0.0
            activate_ultimate()
            # DESIGN: The ultimate should feel proportional to the risk
            # taken to earn it. Big, flashy, screen-clearing.
```

**Anti-hoarding patterns:**
```
class AntiHoarding:
    # DESIGN: These systems prevent "saving for later" syndrome
    
    # Option A: Ammo decays over time — use it or lose it
    func update_ammo_decay(delta):
        if current_ammo > base_ammo:
            decay_timer -= delta
            if decay_timer <= 0:
                current_ammo -= 1
                decay_timer = decay_interval
    
    # Option B: "Overflow" bonus — extra resources convert to something else
    func on_resource_gained_while_full(resource_type):
        # Instead of wasting the pickup, convert to score/XP
        add_score(overflow_bonus)
        show_text("OVERFLOW +500")
        # DESIGN: Player sees they're "leaving value on the table" 
        # by being full, which encourages spending

    # Option C: Escalating power — consecutive uses get stronger
    func get_ability_multiplier():
        # Using abilities in quick succession gets a damage boost
        if time_since_last_use < combo_window:
            consecutive_uses += 1
        else:
            consecutive_uses = 0
        return 1.0 + (consecutive_uses * 0.2)   # 20% bonus per chain
        # DESIGN: Saving one big ability is LESS effective than 
        # chaining multiple uses. Spending is optimal.
```

---

## 3. Rest Bonus / Positive Reframe System

**Problem:** You need to limit a behavior (grinding, long sessions, farming) but penalties feel punitive.

**Design intent:** Flip every penalty into a reward for the opposite behavior. The math can be identical — the framing is what matters.

```
class PositiveReframeSystem:
    # DESIGN: The WoW rest bonus principle (Principle 2)
    # Instead of "you played too long, XP reduced"
    # → "Welcome back! You're well-rested, XP boosted!"
    
    base_xp_rate = 1.0
    rested_bonus_rate = 2.0       # What was "normal" is now the "bonus"
    rest_accumulation_rate = 0.1  # Rest points per real-world minute offline
    max_rest_points = 1440        # 24 hours worth
    current_rest_points = 0

    func on_player_login():
        offline_minutes = get_minutes_since_last_logout()
        current_rest_points = min(max_rest_points,
            current_rest_points + offline_minutes * rest_accumulation_rate)
        
        if current_rest_points > 0:
            show_notification("Well Rested! Bonus XP active!")
            show_rest_bar_ui()
            # DESIGN: Celebrate the return. Make the player feel good
            # about having taken a break. Never say "you were gone too long."

    func get_xp_multiplier():
        if current_rest_points > 0:
            return rested_bonus_rate    # "Bonus" (actually the intended baseline)
        else:
            return base_xp_rate         # "Normal" (actually the reduced rate)
        # DESIGN: Mathematically identical to an XP penalty for long sessions.
        # Psychologically, completely different. Player feels rewarded, not punished.

    func on_xp_gained(amount):
        if current_rest_points > 0:
            bonus = amount  # Double XP while rested
            current_rest_points -= amount
            show_bonus_xp_popup(bonus)    # "+50 XP (Rested Bonus!)"
            # DESIGN: Every XP gain with the bonus is a tiny dopamine hit
            # that reinforces "taking breaks is good"


# Same principle applied to other systems:
class TimeBonus:
    # DESIGN: Instead of "you're too slow, time penalty"
    # → "Speed bonus! +500 points!"
    
    par_time = 120.0   # seconds
    
    func calculate_time_score(completion_time):
        if completion_time < par_time:
            bonus = (par_time - completion_time) * points_per_second
            return base_score + bonus     # "Wow, a speed bonus!"
        else:
            return base_score             # No penalty, just no bonus
        # DESIGN: The fast player feels rewarded.
        # The slow player doesn't feel punished.
        # Both complete the level. One gets extra sparkle.


class FirstTryBonus:
    # DESIGN: Instead of "you died, lose progress"
    # → "Flawless! No-death bonus!"
    
    func on_level_complete(deaths):
        base_reward = calculate_base_reward()
        if deaths == 0:
            return base_reward * 1.5, show_badge("FLAWLESS")
        else:
            return base_reward, null
        # DESIGN: Death is its own punishment (lost time). 
        # No need to pile on. Reward perfection instead.
```

---

## 4. Style Meter / Combo Grading

**Problem:** Players find one effective move and spam it. Combat becomes monotonous.

**Design intent:** Reward variety, creativity, and risk-taking through a visible grading system that makes stylish play feel incredible and repetitive play feel stale.

```
class StyleMeter:
    # DESIGN: Devil May Cry model (Principle 4)
    # Grade: D → C → B → A → S → SS → SSS
    # Rises with varied, skillful play. Drops with repetition or damage.

    grades = ["D", "C", "B", "A", "S", "SS", "SSS"]
    current_score = 0.0
    grade_thresholds = [0, 100, 250, 500, 800, 1200, 1800]
    decay_rate = 15.0             # Points lost per second (constant pressure)
    repetition_penalty = 0.5      # Multiplier for repeating the same move
    
    # Track recently used moves to detect repetition
    recent_moves = []             # Ring buffer, last 5 moves
    max_recent = 5

    func on_attack(move_id, was_risky, distance_to_enemy):
        # Base points for the attack
        points = move_base_points[move_id]
        
        # DESIGN: Variety bonus — new moves score higher
        if move_id in recent_moves:
            repetition_count = recent_moves.count(move_id)
            points *= pow(repetition_penalty, repetition_count)
            # First repeat: 50%. Second: 25%. Rapidly diminishing.
            show_text("Stale!")   # Gentle feedback, not aggressive
        else:
            points *= 1.5
            show_text("Fresh!")
        
        # DESIGN: Risk bonus — close range, low health, airborne = more style
        if was_risky:
            points *= 1.5
        if player.health_percent < 0.3:
            points *= 1.3         # "Low health courage" bonus
        if player.is_airborne:
            points *= 1.2
        
        current_score += points
        update_recent_moves(move_id)
        update_grade_display()

    func on_player_hit():
        # DESIGN: Getting hit drops the meter — encourages skillful dodging
        # But don't zero it out. That's too punishing.
        current_score *= 0.7      # Lose 30%, keep progress
        show_grade_drop_vfx()

    func update(delta):
        # DESIGN: Constant decay creates urgency — style is a treadmill
        # Stop attacking and you lose your grade. Keep the pressure on.
        current_score = max(0, current_score - decay_rate * delta)
        update_grade_display()

    func get_current_grade():
        for i in range(grades.length - 1, -1, -1):
            if current_score >= grade_thresholds[i]:
                return grades[i]
        return grades[0]

    func update_grade_display():
        grade = get_current_grade()
        # DESIGN: The grade display should be BIG, screen-edge, always visible.
        # It's the primary feedback loop. Make it feel alive — pulsing,
        # shaking, glowing brighter at higher grades.
        ui.set_grade(grade)
        ui.set_grade_intensity(current_score / grade_thresholds[-1])
```

**Key details:**
- Decay should be aggressive enough that the player can't "coast" but not so fast that a brief pause resets everything.
- The repetition penalty should be obvious to the player. "Stale!" text or a dulling visual effect teaches the system fast.
- Consider tying the style grade to tangible rewards: higher grades = more resource drops, XP multipliers, or cosmetic effects (the music gets more intense, the screen gets more stylized).

---

## 5. Post-Level Grading Breakdown

**Problem:** Players finish a level and don't know what they missed or how they could improve.

**Design intent:** Show the player the gap between what they did and what's possible, creating intrinsic motivation to replay and improve.

```
class LevelGradeBreakdown:
    # DESIGN: Hitman / Dishonored model (Principle 4)
    # Show what happened, what was possible, and what they earned.

    func generate_breakdown(level_stats):
        breakdown = {
            # Core metrics
            "time":          { value: level_stats.time, par: level.par_time },
            "enemies_found": { value: level_stats.kills + level_stats.stealths, 
                              total: level.total_enemies },
            "secrets_found": { value: level_stats.secrets, total: level.total_secrets },
            "damage_taken":  { value: level_stats.damage_taken },
            
            # Behavior-specific grades (only show relevant ones)
            "ghost":         level_stats.times_detected == 0,
            "pacifist":      level_stats.kills == 0,
            "speed_demon":   level_stats.time < level.par_time * 0.5,
            "completionist": level_stats.secrets == level.total_secrets,
        }
        
        # DESIGN: Reveal challenges the player ALMOST got
        # "Ghost: Failed (detected 1 time)" is more motivating than
        # just not showing the Ghost category at all.
        # Near-misses drive replay more than distant goals.
        
        # Calculate letter grade
        grade = calculate_overall_grade(breakdown)
        
        # DESIGN: Show undiscovered secrets as "???"/silhouettes
        # Presence of unknowns creates curiosity → replay motivation
        for secret in level.all_secrets:
            if secret not in level_stats.found_secrets:
                breakdown.hidden_secrets.add(secret.hint)  # "A hidden room near the waterfall"
        
        return breakdown

    func calculate_overall_grade(breakdown):
        # Weight categories by what you want to incentivize most
        score = 0
        score += grade_time(breakdown.time) * 0.2
        score += grade_stealth(breakdown) * 0.3       # Stealth weighted highest
        score += grade_completion(breakdown) * 0.3
        score += grade_efficiency(breakdown) * 0.2
        return score_to_letter(score)
        # DESIGN: The weighting IS your design intent.
        # Heavy stealth weight = stealth game. Heavy speed weight = action game.
        # Players will optimize for the highest-weighted category.
```

---

## 6. Soft Discouragement Manager

**Problem:** Players camp, turtle, or otherwise exploit static positions.

**Design intent:** The world dynamically responds to stagnation, making camping a temporary tactic rather than a permanent strategy — without explicit timers or penalties.

```
class StagnationResponse:
    # DESIGN: Environmental pressure instead of UI penalties (Principle 6)
    
    player_position_history = []
    stagnation_check_interval = 5.0  # Check every 5 seconds
    stagnation_radius = 8.0          # Player hasn't moved beyond this
    stagnation_levels = 0            # Escalates over time
    max_stagnation = 4

    func update(delta):
        stagnation_timer -= delta
        if stagnation_timer <= 0:
            stagnation_timer = stagnation_check_interval
            check_stagnation()

    func check_stagnation():
        if player_has_stayed_in_radius(stagnation_radius, duration=15.0):
            stagnation_levels = min(max_stagnation, stagnation_levels + 1)
            apply_pressure(stagnation_levels)
        else:
            stagnation_levels = max(0, stagnation_levels - 1)  # Relax if player moves

    func apply_pressure(level):
        # DESIGN: Escalating responses — each feels like a natural world reaction
        match level:
            1:  # Mild: enemies become aware of player's position
                nearby_enemies.foreach(e -> e.investigate(player.position))
                # A bark: "They're behind that wall!" — player hears the shift
                
            2:  # Moderate: grenades / flushing attacks
                assign_grenade_thrower(nearest_enemy, player.cover_position)
                # Player's cover is now temporary, not permanent
                
            3:  # Serious: flanking maneuver
                spawn_flanking_squad(player.position)
                # New enemies approach from behind — camping is now dangerous
                
            4:  # Critical: environmental hazard
                trigger_environmental_hazard(player.area)
                # Fire, collapse, gas, flooding — the SPACE becomes hostile
                # Player MUST move. But it feels like the world, not a timer.

        # DESIGN: NEVER show a "Move or die!" message. Never show a countdown.
        # The pressure should feel organic. "Those enemies are getting smarter"
        # not "the game is punishing me for camping."
```

---

## 7. Dominant Strategy Detector

**Problem:** You need to detect during development/playtesting when one strategy is crowding out all others.

**Design intent:** Analytics tool that flags when player behavior is collapsing to a single dominant strategy so you can address it in design.

```
class DominantStrategyDetector:
    # DESIGN: Diagnostic tool for Principle 1
    # Run during playtesting. Flag issues for designers.

    strategy_usage = {}          # strategy_name → usage count
    total_encounters = 0
    dominance_threshold = 0.6    # Flag if one strategy exceeds 60%
    variety_threshold = 0.1      # Flag if any strategy is below 10%

    func record_encounter(strategy_used):
        total_encounters += 1
        strategy_usage[strategy_used] = strategy_usage.get(strategy_used, 0) + 1

    func analyze():
        if total_encounters < 20:
            return null  # Not enough data

        report = []
        for strategy, count in strategy_usage:
            ratio = count / total_encounters
            
            if ratio > dominance_threshold:
                report.add({
                    type: "DOMINANT",
                    strategy: strategy,
                    usage: ratio,
                    message: f"'{strategy}' used {ratio*100:.0f}% of the time. "
                           + "Consider: Is this the intended primary strategy? "
                           + "If not, what makes alternatives less attractive?"
                })
            
            if ratio < variety_threshold:
                report.add({
                    type: "NEGLECTED",
                    strategy: strategy,
                    usage: ratio,
                    message: f"'{strategy}' used only {ratio*100:.0f}% of the time. "
                           + "Consider: Is the risk/reward unattractive? "
                           + "Is the mechanic poorly taught? "
                           + "Does another strategy obsolete it?"
                })

        # Check overall variety
        entropy = calculate_entropy(strategy_usage, total_encounters)
        max_entropy = log2(len(strategy_usage))
        variety_score = entropy / max_entropy  # 0 = one strategy, 1 = perfectly even
        
        if variety_score < 0.5:
            report.add({
                type: "LOW_VARIETY",
                score: variety_score,
                message: "Overall strategy variety is low. "
                       + "Players are converging on a small number of approaches."
            })

        return report
```

---

## 8. Fragility-Based Stealth Incentive

**Problem:** You want a stealth game but players keep going loud because combat is available.

**Design intent:** Make the player character genuinely fragile in direct combat so stealth is the *smart* choice, not the *mandated* choice. The player chooses stealth because they want to survive, not because a fail screen told them to.

```
class FragilityIncentive:
    # DESIGN: Batman: Arkham / Mark of the Ninja model (Principle 7)
    # Combat exists, but it's risky. Stealth is safer AND more rewarding.

    # Player is fragile
    player_max_health = 3           # Can only take 3 hits before death
    enemy_damage = 1                # Each hit matters

    # But player is powerful in stealth
    stealth_kill_speed = instant    # One-hit takedowns from stealth
    detection_to_combat_delay = 2.0 # Brief window to escape or takedown

    # Combat is simple and costly (Principle 5 — shallow secondary system)
    combat_moves = [light_attack, dodge]   # That's it. Two options.
    combat_recovery_items = rare           # Can't heal easily mid-fight

    # Stealth is deep and rewarding (Principle 5 — deep primary system)
    stealth_tools = [
        distraction_throw,
        vent_crawl,
        ceiling_takedown,
        lure_with_noise,
        disable_lights,
        hack_device,
        environmental_trap,
        smoke_bomb
    ]
    # DESIGN: Count the verbs. Stealth has 8 options. Combat has 2.
    # The depth disparity tells the player where the fun is.

    # Reward structure reinforces stealth
    func on_stealth_kill():
        award_xp(100)
        award_style_points(50)
        # Show satisfying takedown animation
        # Optional: brief slow-mo to make it feel cinematic
    
    func on_combat_kill():
        award_xp(25)                # Less XP — not punitive, just less rewarding
        award_style_points(0)       # No style for brute force
        player.make_noise(high)     # Alert nearby enemies — cascade risk
        # DESIGN: Combat "works" but creates problems (noise, risk, low reward).
        # Stealth "works" and creates opportunities (XP, style, silence).
        # The player learns which path is better without being told.

    func on_detected():
        start_escape_window(detection_to_combat_delay)
        play_alert_sound()
        # DESIGN: Detection isn't failure. It's a tense moment.
        # "Can I vanish before they confirm?" is exciting.
        # "MISSION FAILED: You were detected" is frustrating.
```

**Key details:**
- The health value (3 hits) should be tuned so combat is *survivable* but clearly punishing. Players should be able to escape a bad situation, not guaranteed to die.
- Stealth tools should have obvious, distinct use cases. If the player doesn't know when to use a smoke bomb vs. a distraction, the depth is wasted.
- Consider having detection *escalate* rather than immediately trigger full combat. Suspicion → investigation → alert → combat gives the player multiple chances to recover.
