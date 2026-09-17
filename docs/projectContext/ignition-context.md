# Handoff — Betty (1993 Range Rover Classic 4.2 LWB) no-crank diagnosis

**Status as of end of session:** Root cause identified, not yet resolved. Awaiting a decision on repairing vs. replacing the ignition contact block.

---

## The original complaint

Vehicle parked ~4 months. Battery went flat. After charging, no crank.

Initial symptoms:
- Door chime present with key in barrel (door open)
- Nothing happens on turning the key
- Tried both Park and Neutral
- Jumping 12V onto an exposed ignition terminal with a Power Probe 3 produced dash lights, fuel pump prime, and brake booster engagement
- Turning key to START in that state produced a power drain and a faint electrical buzz, no cranking

Owner's tools: basic digital multimeter (Neoteck, 9999-count, true RMS) and a Power Probe 3.

---

## What has been established (in order)

1. **Key alone does nothing.** Dash only wakes when the terminal is jumped externally. Fault is at or upstream of the ignition switch.

2. **The terminal being jumped is a switch *output*, not the input.** Photo showed the Lucas contact block on the back of the steering column. Brown wire = permanent battery feed (Lucas convention). Whites = switched outputs.

3. **Feed is good.** Brown terminal measures **12.41V** with the multimeter, key out. Fusible links and main supply are therefore not the primary fault. (But see "Open items" — there is a separate corroded lead.)

4. **Shear coupling ruled out.** The key does rotate the barrel's output shaft, so the barrel is driving the block. Fault is inside the block.

5. **Block removed and opened.** Contacts were coated in old yellowed grease (classic insulating varnish failure mode). Detent ball fell out during disassembly. Contacts cleaned.

6. **Bench continuity test passed** (brown → whites):
   - Position I: white 2 beeps, white 3 reads 88Ω
   - Position II: whites 2 and 3 beep
   - START: white 1 beeps, and all whites beep (ignition feed correctly holds through cranking)

7. **BUT — the block shorts brown to the grounded alloy housing.** Continuity test, brown terminal → housing:
   - Position 0: no continuity (correct)
   - Position I: no continuity, 80Ω leakage
   - Position II: **continuity — SHORT**
   - Position III: **continuity — SHORT**

8. **This short is confirmed in-vehicle by voltage collapse.** Black lead on chassis, red on brown terminal:
   - Position 0 and I: 12.3V
   - Position II and III: **0V**
   
   Brown holds at rest and collapses the instant the switch closes. Battery itself measures 12.46V, so the battery is not the problem.

9. **Reassembled three times. Short persists at II and III every time.** Most recent confirmation: still beeps at II and III.

---

## Root cause

The Lucas ignition contact block has an internal short from the brown (permanent battery feed) terminal to the grounded alloy housing, present at key positions II and III.

Likely candidates, in rough order of probability:
- Cracked white insulator (visible split at the rim in earlier photos; plastic is yellowed and 33 years old) — **unrepairable**
- Detent ball or spring trapped between rotor and housing rather than seated in its pocket
- Copper bridge riding proud or rotor cocked, touching the housing wall at certain rotations
- Copper swarf from contact cleaning bridging to the housing

The 88Ω / 80Ω readings seen at position I were initially read as contact overlap. In hindsight they were almost certainly early leakage to ground as the rotor approached the shorted zone.

---

## SAFETY — read before doing anything

- **The brown wire is unfused permanent battery feed.** Turning the key to II with the battery connected currently puts a dead short across it. This is a fire risk under the dash.
- **Keep the battery negative disconnected** for all further switch work.
- Verify isolation by measuring battery positive post → bare chassis. Should read ~0V, not 12V. Clamps spring back onto posts easily — pull the clamp clear and wedge or wrap it.
- The fusible links have absorbed several short events during this diagnosis and should be re-verified before the vehicle is considered good.

---

## Immediate next step (the one test not yet run)

**Isolate which half of the block is shorting.** Disassemble and remove the rotor and copper bridge entirely, leaving only the fixed contact plate with terminals. Then continuity test brown terminal → alloy housing:

- **Beeps with rotor removed** → short is in the fixed plate. Cracked insulator or a loose brown terminal rivet touching the alloy. Unrepairable — order a replacement block.
- **No beep with rotor removed** → short only exists with the rotor installed. Bridge/rotor/detent-ball seating issue. Potentially recoverable.

Also test **clamped into the column**, not just loose on the bench — assembly pressure can push a marginal component into contact in a way it isn't when held in the hand. This may explain why bench tests have passed while in-vehicle tests fail.

---

## Recommendation given to the owner

Stop trying to save the block. Three reassemblies, same short, same two positions. Each test cycle risks the fusible links. A replacement Lucas contact block is cheap, common, and leaves the barrel and keys untouched.

**Before ordering:** find the Lucas type number moulded into the white insulator. Likely in the 128SA / 157SA / 162SA family. The owner has been asked for this and has not yet reported it.

---

## Open items not yet addressed

### 1. Corroded positive lead (confirmed visually, independent fault)

Photo shows a main feed leaving the battery positive terminal with strands that are dark brown/black and splayed apart — oxidised through the bundle at the crimp. This is a real fault regardless of the switch.

- Cleaning will not fix it. Corrosion has wicked between strands under the insulation and cross-section is lost.
- Correct repair: cut back until strands are bright copper (may need to go further than expected), fit a new terminal or properly soldered/crimped joint, seal with adhesive-lined heat shrink. If it's the main battery-to-fusebox feed, replacing the whole lead is cleaner than splicing.
- **Do not simply tape over it** — that hides a corroded high-current joint and traps moisture.
- This is likely the cause of the 2V sag seen when the Power Probe (which draws a small load) read 10.4V on brown where the multimeter read 12.41V.

This is productive work the owner can do while a replacement block is in the post.

### 2. Fusible links — never fully verified

Owner exposed them but the tests were never completed. Two tests needed, both:
- **Continuity, battery disconnected** — catches a fully open link
- **Voltage drop across each link under load** (key at II, once safe to do so): <0.1V healthy, 0.1–0.3V marginal, >0.3V failing

Wiggle while watching. A link can read continuity fine and collapse under current.

### 3. Battery cranking capacity — never tested

The original diagnostic plan's Phase 1 was never completed because the switch fault blocked it. Battery is at 12.46V resting, which is acceptable but not proven under load. **It is entirely possible there are two faults here** — a 4-month flat battery is hard on a battery.

Once the switch is fixed, if it clicks or buzzes but won't turn over:
- Resting voltage after 4+ hours off the charger (want 12.6V+)
- Voltage at the battery posts during crank (below 9.6V = battery or cables)
- Voltage drop tests: battery negative post → engine block (<0.2V); battery positive post → starter B+ stud (<0.5V); post → its own clamp (<0.1V)

### 4. Relay click on key insertion

Owner reports a relay clicking as soon as the key goes into the barrel, before turning. Probably the alarm/immobilizer module waking on the key-in switch. Not investigated. If the vehicle has the factory Lucas alarm and it's armed, it will cut the starter regardless of a perfect ignition switch. Worth trying lock/unlock at the driver's door with the key.

### 5. Engine free rotation — never checked

Never confirmed the engine turns by hand. Worth five minutes with a socket on the crank pulley bolt before buying any starter. A seized engine after 4 months standing would present very differently, but it's cheap to rule out.

---

## Verification checklist once a working block is fitted

Run in this order. Do not skip to turning the key.

1. Bench: brown → housing must be **OL at all four positions**. This is a separate test from brown → whites and is the one that has been failing.
2. Bench: brown → whites sequence correct (0 = all open; II = ignition whites closed; III = ignition + start closed, sprung).
3. Battery negative disconnected. Verify isolation at the post.
4. Check fusible links.
5. Refit block, matching brown and whites to original terminals by trace colour.
6. Repeat brown → chassis ground continuity test **installed**, battery still disconnected. OL at all positions.
7. Reconnect battery. Watch for abnormal spark at the terminal.
8. Meter on brown, key out: ~12.4V expected.
9. Key to II, meter still on brown: **must hold ~12.2V**. If it collapses, key straight back off and disconnect battery.
10. Confirm whites go live at correct positions; look for dash lights and fuel pump prime.
11. Only then attempt START. Park, parking brake on, wheels chocked.

---

## Reference notes

**Lucas wire colour convention (confirmed useful this session):**
- Brown — permanent unswitched battery feed
- White — ignition-fed circuits
- White/red — starter solenoid
- White/orange — accessory

**Measurement gotchas encountered this session, worth not repeating:**
- Measuring brown → white gives the *difference across* the contacts, not what either wire actually is. 0V there is ambiguous: it means either a healthy closed contact or a collapsed feed. **Always reference to chassis ground.**
- Ohm readings from a disconnected block's terminal to the steering column measure the *vehicle harness* (through bulbs, relay coils, ECU), not the switch. Meaningless for switch health.
- The high-impedance meter shows 1–3V of phantom voltage on floating terminals. Load with a test light to distinguish phantom from real.
- The Power Probe 3 is fused ~20A and cannot supply a Lucas solenoid's pull-in current (~30–40A). The buzz/chatter heard at the start of this diagnosis was almost certainly this, not a starter fault. Do not use it on the solenoid trigger; use a fused jumper or a remote starter switch.
- The key-in door chime runs off its own permanent supply and will chime happily with the main ignition feed completely severed. It proves nothing about the ignition circuit.
- The buzz/whir at key-on on an RRC is often the ABS hydraulic pump priming the accumulator — normal and unrelated to cranking.

**Documentation:** The RAVE workshop CD (widely available as an ISO) has full 1993 RRC wiring diagrams including market-variant wire colours and relay positions. Recommended to the owner; not confirmed obtained.

**Searched and not found:** No factory exploded diagram of this Lucas contact block exists online. Forum threads on Lucas switch rebuilds consistently stop at "clean it, don't take it apart." A schematic of the assembly stack-up was produced from photos this session and given to the owner, clearly labelled as inferred rather than authoritative.

---

## Working relationship notes

The owner is technically capable, methodical, and follows a diagnostic plan properly. He reports readings accurately and asks good clarifying questions. He has explicitly said he wants honest pushback and learns well from challenge — so tell him plainly when a reading doesn't mean what he thinks it means, and when a repair isn't worth continuing.

He has caught his own errors (meter leads in the wrong jacks) when prompted with a sanity check. Sanity-check the instrument before trusting a surprising null result.

---

## Suggested skills

**Be aware: none of the available skills cover automotive electrical diagnosis.** There is no skill in the catalog for vehicle wiring, Lucas components, or fault isolation. Do not go looking for one — this work is done from first principles plus the owner's meter readings. The genuinely relevant ones are narrow:

- **`file-reading`** — call this if the owner uploads a file whose content is not already visible in your context (only a path under `/mnt/user-data/uploads/` appears). It routes you to the right tool per file type. Likely relevant here: RAVE manual extracts, wiring diagram PDFs, parts invoices.
- **`pdf-reading`** — call this specifically for reading or extracting from PDFs, which is the most likely format for RAVE workshop manual pages or wiring diagrams. Covers page rasterization for visual inspection, which matters for diagrams.
- **`docx`** or **`pdf`** — only if the owner asks for a written deliverable in those formats (a repair log, a parts list to hand to a shop). Not needed for the diagnosis itself.

The owner communicates almost entirely via photos of the vehicle and meter displays. Those arrive as images directly in context and need no skill — just read them carefully, and say so plainly when an image is ambiguous or doesn't show what's needed rather than guessing.