# Combat Design Implementation Patterns

Reusable patterns for building melee combat systems that feel satisfying. Engine-agnostic pseudocode — adapt to your target language and engine.

## Table of Contents

1. [Animation Phase System](#1-animation-phase-system)
2. [Hit Stop and Impact Juice](#2-hit-stop-and-impact-juice)
3. [Attack Trade-Off Matrix](#3-attack-trade-off-matrix)
4. [Defensive Mechanic Tuning](#4-defensive-mechanic-tuning)
5. [Enemy Poise / Super Armor](#5-enemy-poise--super-armor)
6. [Target Stickiness System](#6-target-stickiness-system)
7. [Combat Style Scorer](#7-combat-style-scorer)
8. [Enemy Tell / Telegraph System](#8-enemy-tell--telegraph-system)

---

## 1. Animation Phase System

**Problem:** Attacks feel like a single blob of animation with no distinct commitment, timing, or punishment window.

**Design intent:** Every attack has three distinct phases — anticipation, contact, recovery — each with gameplay meaning. The player commits during anticipation, gets rewarded at contact, and is vulnerable during recovery.

```
class AttackPhaseSystem:
    # DESIGN: The three phases ARE your combat feel.
    # Anticipation = risk. Contact = reward. Recovery = cost.
    # Tuning these durations changes the entire character of combat.

    enum Phase { IDLE, ANTICIPATION, CONTACT, RECOVERY }
    current_phase = IDLE

    # Per-attack timing (in seconds at 60fps)
    attack_data = {
        "light_attack": {
            anticipation: 0.12,     # 7 frames — fast, low commitment
            contact: 0.05,          # 3 frames — brief hit window
            recovery: 0.15,         # 9 frames — quick return to neutral
            damage: 10,
            can_cancel_into: ["dodge", "light_attack"],  # Free-flowing
        },
        "heavy_attack": {
            anticipation: 0.35,     # 21 frames — big wind-up, big commitment
            contact: 0.08,          # 5 frames — slightly longer hit window
            recovery: 0.40,         # 24 frames — long vulnerability
            damage: 35,
            can_cancel_into: [],    # Fully committed, no cancels
        },
        "launcher": {
            anticipation: 0.20,     # 12 frames — medium risk
            contact: 0.05,          # 3 frames
            recovery: 0.25,         # 15 frames
            damage: 15,
            can_cancel_into: ["aerial_attack"],  # Chains into air combo
        }
    }

    func start_attack(attack_name):
        data = attack_data[attack_name]
        current_phase = ANTICIPATION
        current_attack = data
        phase_timer = data.anticipation

        # DESIGN: Anticipation is the "tell" for both the player and enemies.
        # Slow-mo the first few frames of anticipation for heavy attacks
        # to sell the weight and give the player a "oh no I committed" moment.
        play_anticipation_animation(attack_name)

    func update(delta):
        if current_phase == IDLE:
            return

        phase_timer -= delta

        if phase_timer <= 0:
            advance_phase()

    func advance_phase():
        match current_phase:
            ANTICIPATION:
                current_phase = CONTACT
                phase_timer = current_attack.contact
                activate_hitbox()
                apply_hit_stop_on_contact()
                # DESIGN: The SNAP from anticipation to contact should be instant.
                # If anticipation ends at frame 7, contact begins at frame 8.
                # No easing. No interpolation. Hard cut = impact.

            CONTACT:
                current_phase = RECOVERY
                phase_timer = current_attack.recovery
                deactivate_hitbox()
                # DESIGN: Recovery is the "punishment window."
                # The player is vulnerable here. Enemies should be programmed
                # to exploit recovery frames of heavy attacks.

            RECOVERY:
                current_phase = IDLE
                current_attack = null

    func try_cancel(action):
        # DESIGN: Cancel windows define your combat philosophy.
        # More cancels = DMC (free-flowing). Fewer = Dark Souls (deliberate).
        if current_phase == RECOVERY and action in current_attack.can_cancel_into:
            current_phase = IDLE
            return true     # Allow the cancel
        if current_phase == ANTICIPATION and action == "dodge":
            # DESIGN: Many games allow dodge-canceling during anticipation
            # as a "changed my mind" safety valve. This prevents frustration
            # without removing commitment (you lose the attack but save yourself).
            current_phase = IDLE
            return true
        return false        # Locked in
```

**Key details:**
- Frame counts are at 60fps. Scale for your target framerate.
- The anticipation→contact snap is the #1 thing that creates "weight." If this transition is smoothly interpolated, combat will feel floaty no matter what.
- Heavy attacks should have roughly 2-3x the anticipation AND recovery of light attacks. The ratio matters more than the absolute values.

---

## 2. Hit Stop and Impact Juice

**Problem:** Attacks connect but don't feel like they connect. Combat is "floaty."

**Design intent:** Stack multiple small feedback effects at the moment of contact to create visceral impact. Each layer is subtle alone. Together they create "crunch."

```
class ImpactJuice:
    # DESIGN: Impact is built from layers. Add them one at a time
    # during development and playtest after each addition.
    # Order of importance: hit stop > sound > screen shake > particles > trails

    # === HIT STOP (most important) ===
    func apply_hit_stop(attack_power):
        # Freeze both attacker and target for a few frames
        # Scale duration with attack power
        frames = lerp(2, 6, attack_power / max_attack_power)
        freeze_animation(attacker, frames)
        freeze_animation(target, frames)
        # DESIGN: Hit stop on BOTH characters sells mutual impact.
        # The attacker's weapon "sticks" in the target for an instant.
        # This is the single most important piece of game feel.
        # Without it, nothing else matters.

    # === SNAP (anticipation → contact speed) ===
    func apply_attack_snap(attack_data):
        # Play anticipation at normal or slow speed
        set_animation_speed(anticipation_anim, 0.8)  # Slightly slow for weight
        # Then SNAP to contact at 2-3x speed
        set_animation_speed(contact_anim, 2.5)
        # DESIGN: The speed DIFFERENTIAL is the impact.
        # Slow wind-up → instant crack. Like a whip.

    # === SCREEN SHAKE ===
    func apply_screen_shake(attack_power, direction):
        # Small, directional shake. NOT random jitter.
        intensity = lerp(1.0, 4.0, attack_power / max_attack_power)
        duration = lerp(0.05, 0.15, attack_power / max_attack_power)
        shake_camera(direction * intensity, duration, decay=EXPONENTIAL)
        # DESIGN: Directional shake (in the direction of the swing)
        # feels more intentional than random shake.
        # ALWAYS use exponential decay — linear looks mechanical.
        # Keep it SHORT. Long shakes are nauseating, not impactful.

    # === PARTICLES ===
    func spawn_hit_particles(contact_point, attack_type):
        # Sparks, blood, dust — genre-dependent
        spawn_burst(contact_point,
            count=lerp(5, 20, attack_power / max_attack_power),
            spread=45,          # Degrees — cone shape, not sphere
            direction=attack_direction,
            speed=fast,
            lifetime=0.3        # Brief. Don't litter the screen.
        )
        # DESIGN: Particles should burst OUTWARD from the contact point
        # in the direction of the attack. This reinforces the sense of force.

    # === MOTION TRAILS ===
    func update_weapon_trail(weapon):
        if current_phase == ANTICIPATION or current_phase == CONTACT:
            trail.add_point(weapon.tip_position)
            trail.set_width(lerp(0, max_trail_width, attack_speed))
            trail.set_opacity(1.0)
        else:
            trail.fade_out(0.1)
        # DESIGN: Trails define the SHAPE of the swing arc.
        # They help the player read their own attacks in fast combat.
        # Trail color can encode attack type (red = heavy, blue = light).

    # === KNOCKBACK / HIT REACTION ===
    func apply_hit_reaction(target, attack_data):
        # Push the target in the attack direction
        knockback_force = attack_data.knockback * attack_direction
        target.apply_impulse(knockback_force)
        
        # Play hit reaction animation
        target.play_animation(get_hit_reaction(attack_data.type))
        # DESIGN: Hit reactions should be PROPORTIONAL to attack weight.
        # Light attack: small flinch, barely interrupts.
        # Heavy attack: full stumble, drops guard.
        # Critical hit: ragdoll or stagger animation.

    # === AUDIO ===
    func play_hit_sound(attack_data, target):
        # Layer multiple sounds for one hit
        play_sound(attack_data.swing_sound)        # Whoosh of the swing
        play_sound(target.material_hit_sound)       # Clang/thud/squelch
        play_sound(attack_data.impact_sound)        # The "crunch"
        # DESIGN: Three layers minimum for a satisfying hit:
        # 1. The MOVEMENT sound (whoosh, tells speed)
        # 2. The MATERIAL sound (what was hit — metal, flesh, wood)
        # 3. The IMPACT sound (the bass "thump" that sells weight)
        # Mix each layer independently. The impact layer should be loudest.

    # === SLOW-MO (use sparingly) ===
    func apply_critical_slow_mo():
        # Brief time dilation on critical hits or kill shots
        set_time_scale(0.3)
        wait(0.15)       # ~150ms of slow-mo
        set_time_scale(1.0)
        # DESIGN: Slow-mo is the nuclear option of game feel.
        # Incredible when rare. Annoying when frequent.
        # Reserve for: killing blows, critical hits, parry successes.
        # NEVER use on every hit.


# === Combined "on hit" function ===
func on_attack_hit(attacker, target, attack_data, contact_point):
    # Fire ALL juice layers together
    apply_hit_stop(attack_data.power)
    apply_screen_shake(attack_data.power, attack_data.direction)
    spawn_hit_particles(contact_point, attack_data.type)
    apply_hit_reaction(target, attack_data)
    play_hit_sound(attack_data, target)
    
    if is_killing_blow(target, attack_data):
        apply_critical_slow_mo()
    
    # THEN apply damage (after all the feel)
    target.take_damage(attack_data.damage)
```

---

## 3. Attack Trade-Off Matrix

**Problem:** One attack dominates because it has no meaningful trade-off compared to others.

**Design intent:** Map every attack across multiple dimensions to ensure each one has a unique niche. If two attacks overlap too much, merge them or differentiate harder.

```
ATTACK TRADE-OFF MATRIX

Fill in for every attack in your game. No two attacks should
have the same "best at" column.

┌──────────────┬────────┬───────┬───────┬──────────┬──────────┬────────────┐
│ Attack       │ Damage │ Speed │ Range │ AOE      │ Recovery │ Best For   │
├──────────────┼────────┼───────┼───────┼──────────┼──────────┼────────────┤
│ Light Jab    │ Low    │ Fast  │ Short │ Single   │ Quick    │ Poking,    │
│              │        │       │       │          │          │ combo start│
├──────────────┼────────┼───────┼───────┼──────────┼──────────┼────────────┤
│ Heavy Slash  │ High   │ Slow  │ Med   │ Single   │ Long     │ Punishing  │
│              │        │       │       │          │          │ openings   │
├──────────────┼────────┼───────┼───────┼──────────┼──────────┼────────────┤
│ Sweep        │ Med    │ Med   │ Med   │ Wide arc │ Med      │ Crowd      │
│              │        │       │       │          │          │ control    │
├──────────────┼────────┼───────┼───────┼──────────┼──────────┼────────────┤
│ Launcher     │ Low    │ Med   │ Short │ Single   │ Med      │ Starting   │
│              │        │       │       │          │          │ air combos │
├──────────────┼────────┼───────┼───────┼──────────┼──────────┼────────────┤
│ Thrust       │ Med    │ Med   │ Long  │ Narrow   │ Long     │ Gap closing│
│              │        │       │       │          │          │ single tgt │
└──────────────┴────────┴───────┴───────┴──────────┴──────────┴────────────┘

VALIDATION CHECKS:
[ ] Every attack has a unique "Best For" column.
[ ] No attack is strictly better than another on ALL dimensions.
[ ] Every attack has at least one clear WEAKNESS.
[ ] High-damage attacks are always slow OR have long recovery.
[ ] Wide-area attacks are always lower damage OR slower.
[ ] The fastest attack does the least damage.
```

### Template

```
┌──────────────┬────────┬───────┬───────┬──────────┬──────────┬────────────┐
│ Attack       │ Damage │ Speed │ Range │ AOE      │ Recovery │ Best For   │
├──────────────┼────────┼───────┼───────┼──────────┼──────────┼────────────┤
│              │        │       │       │          │          │            │
├──────────────┼────────┼───────┼───────┼──────────┼──────────┼────────────┤
│              │        │       │       │          │          │            │
├──────────────┼────────┼───────┼───────┼──────────┼──────────┼────────────┤
│              │        │       │       │          │          │            │
├──────────────┼────────┼───────┼───────┼──────────┼──────────┼────────────┤
│              │        │       │       │          │          │            │
└──────────────┴────────┴───────┴───────┴──────────┴──────────┴────────────┘
```

---

## 4. Defensive Mechanic Tuning

**Problem:** One defensive option dominates (usually block), making the others pointless.

**Design intent:** Each defensive mechanic should have a distinct situation where it's the *best* choice, with clear costs that prevent universal use.

```
class DefensiveMechanics:
    # DESIGN: Block, dodge, and parry should form a triangle of trade-offs.
    # No single option should be "always correct."

    # === BLOCK ===
    func block(incoming_attack):
        stamina_cost = incoming_attack.damage * 0.5
        player.stamina -= stamina_cost
        
        if player.stamina <= 0:
            trigger_guard_break()    # DESIGN: Block has a breaking point.
            return                   # Guard-break = stunned, big punishment window.
        
        damage_mitigated = incoming_attack.damage * 0.8   # Block 80%, chip 20%
        player.take_damage(incoming_attack.damage - damage_mitigated)
        player.play_animation("block_recoil")
        
        # DESIGN: Block is the SAFE option. Reliable, low-skill.
        # Cost: stamina drain + chip damage + no counterattack window.
        # Beats: fast multi-hit combos (dodge would need perfect timing for each).
        # Loses to: guard-break attacks, heavy single hits (stamina cost too high).

    # === DODGE (i-frames) ===
    func dodge(direction):
        if player.stamina < dodge_stamina_cost:
            return false    # Can't dodge when exhausted
        
        player.stamina -= dodge_stamina_cost
        player.set_invincible(dodge_iframes)    # e.g., 0.2 seconds
        player.move(direction * dodge_distance)
        player.play_animation("dodge_roll")
        
        # DESIGN: Dodge is the EVASIVE option. Full invincibility but brief.
        # Cost: stamina + repositioning (you end up somewhere new).
        # Beats: heavy single hits (full avoidance, no chip damage, no stamina drain).
        # Loses to: fast multi-hit combos (must dodge each hit separately),
        #           tracking attacks that follow the dodge.

    # === PARRY ===
    parry_window = 0.12           # 7 frames at 60fps — tight!
    parry_recovery_on_whiff = 0.5 # Half second of vulnerability on failure

    func attempt_parry():
        parry_active = true
        parry_timer = parry_window

    func on_hit_during_parry(incoming_attack):
        if parry_active and parry_timer > 0:
            # SUCCESS
            player.heal(parry_heal_amount)
            enemy.stagger(parry_stagger_duration)
            play_parry_effects()    # Big flash, time slow, satisfying crunch
            # DESIGN: Parry success is the MOST rewarding defensive action.
            # Full damage negation + heal + enemy stagger + style points.
            # This justifies the high risk.
        else:
            # FAILED PARRY — worse than if you'd just blocked
            player.take_damage(incoming_attack.damage * 1.2)  # 20% bonus damage!
            player.stagger(parry_recovery_on_whiff)
            # DESIGN: Failed parry MUST be punishing. If it's not,
            # players will spam parry attempts with no consequences.
            # The risk must match the reward.

    # === TUNING REFERENCE ===
    # 
    # If parry is too dominant:
    #   → Shrink parry_window (below 0.1s = very hard)
    #   → Increase whiff punishment (longer stagger, more bonus damage)
    #   → Add resource cost (parry uses a consumable)
    #   → Add un-parryable attacks to certain enemies
    #
    # If dodge is too dominant:
    #   → Reduce i-frames (below 0.15s = tight)
    #   → Add tracking attacks that follow dodges
    #   → Add multi-hit combos that punish dodge spam
    #   → Increase stamina cost
    #
    # If block is too dominant:
    #   → Add guard-break attacks
    #   → Increase chip damage
    #   → Faster stamina drain
    #   → Enemies that grab through blocks
```

---

## 5. Enemy Poise / Super Armor

**Problem:** The player stunlocks every enemy with fast attacks, reducing combat to mashing.

**Design intent:** Poise creates a rhythm — the player gets a window to attack, then must respect the enemy's retaliation. Different poise levels across enemies force different combat approaches.

```
class PoiseSystem:
    # DESIGN: Poise prevents stunlock and creates combat rhythm.
    # Without it: mash light attack until everything dies.
    # With it: attack during windows, defend during enemy turns.

    max_poise = 100.0
    current_poise = max_poise
    poise_recovery_rate = 20.0     # Per second while not being hit
    poise_recovery_delay = 1.0     # Seconds after last hit before recovery starts
    stagger_threshold = 0          # Staggers when poise hits 0
    last_hit_time = 0

    # Poise damage per attack type
    poise_damage = {
        "light_attack": 15,     # ~7 lights to break poise
        "heavy_attack": 50,     # 2 heavies to break poise
        "launcher":     30,     # ~3-4 to break
    }

    func take_poise_damage(attack_type):
        current_poise -= poise_damage[attack_type]
        last_hit_time = current_time

        if current_poise <= 0:
            trigger_stagger()
            # DESIGN: Breaking poise = big reward window for the player.
            # Enemy stumbles, drops guard, player gets free hits.
            # This is the "earned" aggression moment.
        else:
            # Poise held — enemy does NOT flinch
            continue_current_action()
            # DESIGN: The enemy keeps attacking through the player's hits.
            # This is the "respect the enemy" moment. Player must back off
            # or eat damage. Creates the attack/defend rhythm.

    func trigger_stagger():
        current_poise = max_poise    # Reset poise after stagger
        play_stagger_animation()
        is_vulnerable = true
        vulnerability_timer = stagger_duration
        # DESIGN: Stagger duration should be long enough for 2-3 heavy attacks.
        # This is the player's reward for breaking poise. Make it feel good.

    func update(delta):
        # Poise regenerates after a delay
        if current_time - last_hit_time > poise_recovery_delay:
            current_poise = min(max_poise, current_poise + poise_recovery_rate * delta)


# === POISE PRESETS BY ENEMY ARCHETYPE ===

enemy_poise_presets = {
    "fodder": {
        max_poise: 0,       # No poise — stunlocked by anything. Feels fragile.
        # DESIGN: Fodder enemies exist to make the player feel powerful.
        # Zero poise = the player can mash and enjoy the crunch.
    },
    "soldier": {
        max_poise: 50,      # 3-4 light hits to break. Creates a short rhythm.
        # DESIGN: The baseline enemy. Player gets a combo, then must dodge
        # or block the counter, then combo again.
    },
    "brute": {
        max_poise: 150,     # Takes sustained damage to break. Fights through hits.
        # DESIGN: The "patience test" enemy. Player must commit to a strategy
        # (sustained aggression OR wait for openings after the brute attacks).
    },
    "boss": {
        max_poise: 300,     # Phase-based poise. Breaks once per phase.
        # DESIGN: Boss poise should break at dramatic moments — phase transitions,
        # signature attack recovery, etc. The stagger IS the climax.
    }
}
```

---

## 6. Target Stickiness System

**Problem:** Either the player constantly whiffs (low stickiness, frustrating) or feels like they're on autopilot (high stickiness, boring).

**Design intent:** Calibrate auto-targeting to match your game's intended skill curve. High for cinematic brawlers, low for precision action games.

```
class TargetStickiness:
    # DESIGN: Stickiness controls how much the game "helps" the player aim.
    # Arkham-level = high (lunge across rooms). Souls-level = low (you aim).

    stickiness = 0.7    # 0.0 = no assist, 1.0 = full auto-lock

    # Detection
    lock_on_range = 8.0
    lock_on_angle = 60.0    # Degrees from facing direction

    func get_attack_target():
        enemies_in_range = find_enemies_within(lock_on_range)
        enemies_in_cone = filter_by_angle(enemies_in_range, lock_on_angle)

        if enemies_in_cone.is_empty:
            return null     # No assist — attack goes where player faces

        # Score each target
        best = null
        best_score = -1
        for enemy in enemies_in_cone:
            distance_score = 1.0 - (enemy.distance / lock_on_range)
            angle_score = 1.0 - (enemy.angle / lock_on_angle)
            threat_score = enemy.is_attacking ? 0.3 : 0.0  # Bias toward threats

            total = distance_score * 0.4 + angle_score * 0.4 + threat_score * 0.2
            if total > best_score:
                best = enemy
                best_score = total

        return best

    func apply_stickiness(target):
        if target == null:
            return

        # Rotate player toward target
        angle_to_target = angle_between(player.facing, target.position)
        rotation_amount = angle_to_target * stickiness
        player.rotate(rotation_amount)

        # Lunge toward target (optional, for high-stickiness games)
        if stickiness > 0.5:
            distance_to_target = player.distance_to(target)
            lunge_distance = min(distance_to_target * stickiness, max_lunge)
            player.move_toward(target.position, lunge_distance)
            # DESIGN: Lunging is what makes Arkham-style combat feel "magnetic."
            # The player presses attack and ZOOMS to the enemy.
            # Great for crowd combat. Bad for precision combat.

    # === STICKINESS PRESETS ===
    # Arkham Asylum / Spider-Man:    stickiness = 0.9, big lunge
    # God of War (2018):             stickiness = 0.6, moderate rotation, small lunge
    # Dark Souls / Bloodborne:       stickiness = 0.2, slight rotation only
    # Sekiro:                        stickiness = 0.4, rotation + lock-on system
```

---

## 7. Combat Style Scorer

**Problem:** Players find the safest move and spam it. Combat becomes monotonous.

**Design intent:** Reward variety, risk-taking, and defensive skill with a visible grade that drives intrinsic motivation to play well.

```
class CombatStyleScorer:
    # DESIGN: Connects directly with player-protection-design's style meter.
    # This version is combat-specific with attack/defense tracking.

    score = 0.0
    grade_thresholds = { "D": 0, "C": 100, "B": 300, "A": 600, "S": 1000 }
    decay_rate = 8.0

    # Track recent moves for variety calculation
    recent_attacks = []
    max_recent = 6

    # Multipliers
    no_damage_multiplier = 1.0     # Grows while player avoids damage

    func on_attack_hit(attack_type):
        base = attack_base_scores[attack_type]

        # Variety bonus — new move = more points
        if attack_type not in recent_attacks:
            base *= 1.5
        else:
            repeat_count = recent_attacks.count(attack_type)
            base *= max(0.2, 1.0 - repeat_count * 0.25)
            # Rapid diminishing returns: 75%, 50%, 25%, floor 20%

        # Risk bonus — heavy attacks, aerials, parry-counters score higher
        if attack_type in high_risk_attacks:
            base *= 1.3

        score += base * no_damage_multiplier
        recent_attacks.append(attack_type)
        if len(recent_attacks) > max_recent:
            recent_attacks.pop(0)

    func on_successful_parry():
        score += 150 * no_damage_multiplier
        no_damage_multiplier += 0.2    # Reward keeps growing
        # DESIGN: Parries are the highest-scoring defensive action.
        # This incentivizes the hardest (riskiest) defensive option.

    func on_successful_dodge():
        score += 50 * no_damage_multiplier
        # DESIGN: Dodges score less than parries — they're safer.

    func on_player_damaged():
        no_damage_multiplier = 1.0     # Reset multiplier
        score *= 0.7                   # Drop 30%
        # DESIGN: Getting hit is costly to score but NOT a reset.
        # Player can recover a dropped grade with good play.

    func update(delta):
        score = max(0, score - decay_rate * delta)
        # DESIGN: Constant decay creates urgency. Stop fighting = lose grade.

    func get_grade():
        for grade in ["S", "A", "B", "C", "D"]:
            if score >= grade_thresholds[grade]:
                return grade
        return "D"
```

---

## 8. Enemy Tell / Telegraph System

**Problem:** Enemy attacks land before the player can react, or all attacks look the same.

**Design intent:** Every enemy attack should be readable through visual, audio, and timing cues. The tell duration scales inversely with the player's expected reaction time — bigger threats need bigger tells.

```
class EnemyTelegraphSystem:
    # DESIGN: Tells are the enemy's ANTICIPATION phase.
    # They're both a gameplay signal ("incoming!") and a 
    # characterization tool ("this brute is slow but devastating").

    telegraph_data = {
        "light_swipe": {
            wind_up_duration: 0.3,          # Quick — test reflexes
            visual: "weapon_glint",          # Brief flash on weapon
            audio: "short_grunt",            # Subtle audio cue
            ground_indicator: null,          # No ground marker needed
            # DESIGN: Light attacks have short, subtle tells.
            # The player learns to read these through repetition.
        },
        "heavy_overhead": {
            wind_up_duration: 0.8,          # Long — gives time to react
            visual: "weapon_raise + glow",   # Big, unmissable pose
            audio: "battle_cry",             # Loud, distinct
            ground_indicator: "impact_zone", # Red circle on ground
            # DESIGN: Heavy attacks have dramatic, exaggerated tells.
            # The player sees this and immediately knows: "dodge NOW."
        },
        "unblockable_grab": {
            wind_up_duration: 0.6,
            visual: "arms_wide + red_flash",  # Red = unblockable convention
            audio: "roar",
            ground_indicator: "grab_range",
            ui_icon: "unblockable_symbol",    # Extra UI warning
            # DESIGN: Unblockable/special moves need EXTRA telegraphing
            # because the player's default defense won't work.
            # Use a universal color code (red = unblockable, blue = must dodge,
            # yellow = must parry, etc.)
        },
        "aoe_slam": {
            wind_up_duration: 1.2,           # Longest — dodge out of zone
            visual: "jump_into_air + glow",
            audio: "wind_up_whoosh",
            ground_indicator: "large_danger_zone",
            camera_cue: true,                 # Brief camera pull-back to show area
            # DESIGN: AOE attacks need the camera itself to help communicate.
            # A brief zoom-out or slow-mo moment lets the player see the
            # full danger zone before the slam hits.
        }
    }

    func start_enemy_attack(enemy, attack_name):
        data = telegraph_data[attack_name]

        # Start visual tell
        if data.visual:
            enemy.play_telegraph_animation(data.visual)
        if data.ground_indicator:
            show_ground_indicator(enemy.target_position, data.ground_indicator)
        if data.audio:
            play_3d_sound(enemy.position, data.audio)
        if data.ui_icon:
            show_ui_warning(data.ui_icon)
        if data.camera_cue:
            brief_camera_pullback(0.3)

        # Wait for wind-up, then execute
        wait(data.wind_up_duration)
        execute_attack(enemy, attack_name)
        hide_ground_indicator()

    # === TELL DURATION GUIDE ===
    #
    # REACTION TIME REFERENCE:
    #   Average human visual reaction: ~250ms
    #   With game context + prediction:  ~200ms
    #   Expert players in flow state:    ~150ms
    #
    # TELL DURATION BY THREAT LEVEL:
    #   Trivial attack (chip damage):     0.2-0.3s (reflex test)
    #   Standard attack (meaningful dmg): 0.4-0.6s (read and react)
    #   Heavy attack (big damage):        0.7-1.0s (clear warning)
    #   Devastating (one-shot potential):  1.0-1.5s (unmissable signal)
    #
    # TELL DURATION BY DIFFICULTY:
    #   Easy mode:    multiply all by 1.3
    #   Normal:       as designed
    #   Hard:         multiply all by 0.8
    #   Very hard:    multiply all by 0.6, remove ground indicators
```
