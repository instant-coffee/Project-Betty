# Betty — No start: key does nothing

**Status:** Open
**Opened:** 2026-09-16 (diagnosis began earlier in a chat session, see Round 1)
**Vehicle:** 1993 Range Rover Classic 4.2 LWB

## Symptom

Turning the key does nothing: no dash lights, no fuel pump prime, no crank, in Park or Neutral. The door chime still sounds with the key in, but it runs off its own supply and proves nothing about the ignition circuit. Started after standing: parked summer 2025, moved once, then the battery was found completely flat and was recharged.

**Hazard while open:** the ignition switch shorts the unfused permanent battery feed (brown) to ground at key positions II and III. With the battery connected, turning the key past I puts a dead short under the dash (fire risk). Keep the battery negative disconnected for all switch work.

## Diagnostic Log

### Round 1 — before 2026-09-16 (earlier chat session)

Source: [ignition handoff](../docs/projectContext/ignition-context.md), which holds the full readings, the measurement gotchas, and the Lucas wire colours. Tools on hand: Neoteck multimeter, Power Probe 3.

**Reported:** Parked (about 4 months per the handoff, probably counted from when she was last moved), battery went flat, no crank after charging. Nothing on the key in Park or Neutral; door chime present. Jumping 12 V onto an ignition output terminal with the Power Probe gave dash lights, fuel pump prime and brake booster. START in that state gave a drain and a faint buzz, no crank. The Power Probe (fused ~20 A) can't supply solenoid pull-in current, so that buzz says nothing about the starter.

**Results:**
- Key alone does nothing; jumping a switch output wakes the dash. Fault is at or upstream of the ignition switch.
- Brown measures 12.41 V to chassis with the key out. Battery 12.46 V resting.
- The key turns the barrel's output shaft, so the barrel is driving the contact block.
- Block removed and opened: contacts coated in old yellowed grease, detent ball fell out. Contacts cleaned.
- Bench, brown → whites: I = white 2 beeps, white 3 reads 88 Ω. II = whites 2 and 3 beep. START = all whites beep.
- Bench, brown → alloy housing: 0 = open. I = 80 Ω. II = short. III = short.
- In vehicle, brown → chassis: 12.3 V at 0 and I, 0 V at II and III.
- Same short after three reassemblies.

**Ruled out:**
- Main feed and fusible links as the primary fault: brown holds 12.4 V until the switch closes. The links still need checking (see Round 2).
- Ignition barrel shear coupling.

**Confirmed:** the Lucas contact block shorts to its grounded alloy housing at positions II and III.

**Found along the way (not the cause, still a fault):** the battery positive lead is corroded through at the crimp, strands black and splayed. Likely why the Power Probe read 10.4 V on brown under load when the meter read 12.41 V.

**Not yet proven:** battery under load. 12.46 V resting is below a full charge (12.6 V+), and months flat is hard on a battery.

### Round 2 — 2026-09-16

**Results reported:** Case opened in the WorkBook from the Round 1 handoff and owner intake. No new tests since Round 1. The head gaskets were already due before she was parked. Fresh petrol added.

**Updated hypotheses (ranked), where the short is inside the block:**
1. **White 3's contact or terminal grounded to the housing** (cracked insulator, or a rivet touching the alloy near it). Best fit: brown → housing tracks brown → white 3 exactly. It reads 80–88 Ω at I, where white 3 is part-closed, and shorts at II and III, where white 3 is fully closed. If so, brown only reaches ground through white 3, and the 80 Ω at I isn't early rotor leakage. The insulator is yellowed and had a visible split at its rim.
2. **Detent ball or spring trapped** against a white contact and the housing. The ball fell out during disassembly, so its seating is suspect.
3. **Copper bridge riding proud or rotor cocked**, touching the housing wall only at II and III. Doesn't explain the 80 Ω at I as neatly.
4. **Copper swarf from cleaning** bridging a contact to the housing. Unlikely to survive three reassemblies unchanged, but free to rule out while it's apart.

**Recommended tests** (battery negative disconnected for all of them; confirm isolation by measuring battery + post → bare chassis ≈ 0 V, clamp wedged clear of the post):
1. **Block on the bench, key at 0: continuity from each white terminal to the alloy housing.** At 0 brown isolated, so this tests the whites alone. A beep on white 3 points to hypothesis 1 or 2; no beep on any white points to 3 or 4. No disassembly needed.
2. **Remove the rotor and copper bridge, then retest brown and each white → housing.** Still beeps: the short is in the fixed plate (cracked insulator or rivet), unrepairable, replace the block. Stops beeping: rotor, bridge, ball or swarf, possibly recoverable. Check the detent ball's pocket and look for swarf while it's apart.
3. **Read the Lucas type number moulded into the white insulator** (likely the 128SA / 157SA / 162SA family) so a replacement block can be ordered. Three reassemblies with the same short already make replacement the sensible call.
4. **Fusible links: continuity on each, battery disconnected.** They absorbed several short events during Round 1.
5. **Turn the engine by hand** with a socket on the crank pulley bolt, two full turns, ideally with the plugs out while watching the plug holes for fluid. With failing head gaskets and about a year standing, coolant in a cylinder could hydrolock the engine on the first crank.

**While waiting on parts:** cut the corroded battery positive lead back to bright copper and fit a new terminal sealed with adhesive-lined heat shrink, or replace the lead if it's the main battery-to-fusebox feed. Don't tape over it.

**Before the first START with a good block fitted, in order:**
1. Bench: brown → housing open at all four positions, then the brown → whites sequence correct.
2. Refit, matching wires to terminals by trace colour. Repeat brown → chassis continuity installed, battery still disconnected: open at all positions.
3. Reconnect the battery and watch for sparking at the terminal. Brown ≈ 12.4 V with the key out.
4. Key to II with the meter on brown: must hold ≈ 12.2 V. If it collapses, key off and disconnect the battery.
5. Whites live at the right positions, dash lights on, fuel pump primes.
6. Only then START: Park, parking brake on, wheels chocked.

**If it then clicks or buzzes but won't turn over:** battery resting voltage after 4 h off the charger (want 12.6 V+) and during crank (below 9.6 V = battery or cables). Voltage drops: negative post → engine block < 0.2 V, positive post → starter B+ stud < 0.5 V, each post → its own clamp < 0.1 V. Fusible link drop under load < 0.1 V. Try locking and unlocking the driver's door with the key, since the relay that clicks on key-in may be an armed alarm cutting the starter.

## Resolution

<!-- not yet resolved -->
