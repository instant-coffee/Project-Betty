# Betty — Cold idle stalling

**Status:** Open
**Opened:** 2026-08-18
**Vehicle:** 1993 Range Rover Classic 4.2 LWB

## Symptom
Intermittently stalls at idle when the engine is still cold (first 5-10 minutes after a cold start). Once warmed up, idles fine and hasn't stalled while driving. Started about two weeks ago, no other changes noticed.

## Diagnostic Log

### Round 1 — 2026-08-18
**Reported:** Stalls at idle only when cold, 2-3 times this week, always within the first few minutes after starting. No warning lights. Hasn't had any recent work done.

**Hypotheses (ranked):**
1. Coolant temperature sensor drift — feeds incorrect cold-start enrichment to the 14CUX ECU, a well-known cause of exactly this symptom pattern. Cheap and fast to check.
2. Idle air control / bypass stepper motor fault — controls cold idle air bypass; a fault here commonly presents as cold-only stalling.
3. Vacuum leak — perished hose specifically affecting cold idle behavior before things seal up thermally.

**Recommended tests:**
1. Check coolant temperature sensor resistance against spec at a known temperature (compare to manual/RAVE spec table).
2. Pull and inspect the IAC/bypass stepper motor connector for corrosion; check it moves freely.
3. Do a visual/smoke check of vacuum hoses near the throttle body and intake.
