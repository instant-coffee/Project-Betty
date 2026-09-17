---
name: betty-diagnostics
description: Diagnoses mechanical, electrical, and other issues on Betty, a 1993 Range Rover Classic 4.2 LWB, through an iterative, evidence-based troubleshooting process. Keeps a persistent case file per issue that accumulates symptoms, ranked hypotheses, recommended tests, and results across multiple rounds until the problem is resolved. Use this skill any time Betty, the Range Rover, or "the Rover" is mentioned as having a problem, acting up, making a noise, throwing a warning light, not starting, or behaving strangely — even if the user just describes a symptom without asking for a "diagnosis" outright (e.g. "Betty won't start this morning," "the Rover's idle is rough," "there's a burning smell from the Range Rover," "I checked the relay like you said and..."). Also use it to record test results or fix confirmations for an issue already in progress.
---

# Betty Diagnostics

Betty is a 1993 Range Rover Classic 4.2 LWB. She's 30+ years old, and problems on cars like this are rarely solved in one shot — you form a hypothesis, test it cheaply, learn something, and narrow down from there. This skill's job is to keep that process honest and cumulative: never re-suggest something already ruled out, always leave a trail of what was tried and why, and ground hypotheses in what's actually known to go wrong on this specific vehicle rather than generic car advice.

## Where things live

All of Betty's records live in the Project Betty repo (the WorkBook). Paths below are relative to the repo root; they are provisional until the WorkBook's folder structure is decided:

- `vehicle-profile.md` — static facts about Betty herself (VIN, transmission type, suspension type, known mods or deviations from stock, service history highlights). Read this first, every time. If it doesn't exist yet, this is the very first case — create it with whatever the user tells you, note anything unknown as "unknown — verify from VIN plate / RAVE manual," and don't block the diagnosis waiting on it.
- `cases/<slug>.md` — one evolving file per distinct issue, named for the symptom (e.g. `cases/2026-08-cold-start-stall.md`). A 30-year-old Land Rover often has more than one thing wrong at once — don't merge unrelated symptoms into one file, and don't let a new report silently reopen an old resolved case unless the symptom genuinely matches.

If `cases/` doesn't exist yet, create it.

## Step 1: Work out which case this is

Read `vehicle-profile.md` if present, then look through `cases/` for an open case (`Status: Open`) whose symptom matches what's being reported. Signs this is a continuation, not a new issue: the user references a test you previously recommended, reports results, or is clearly picking up a thread ("I checked the relay like you said"). If so, that's the same case file — append to it, don't start over.

Otherwise, treat it as new — even if another case is still open for something else.

## Step 2: Get the essentials before guessing

Don't jump to hypotheses off a one-line report like "Betty won't start." Before diagnosing, you need (pull from what's already been said, and ask conversationally for the rest — this isn't a rigid intake form):

- the exact symptom and how it presents (sound, smell, warning light, handling change, won't start, stalls, etc.)
- when it started, and whether it's consistent or intermittent
- what it correlates with (cold start vs. hot, idle vs. driving, weather, freshly driven vs. sat for a while, a speed/RPM range)
- anything that changed recently (a repair, a part swap, long period unused)
- mileage, if known and relevant

If the first message already answers most of this, don't re-ask — reason with what you have and only chase the gaps.

## Step 3: Form ranked hypotheses

Read `references/rrc-4.2-lwb-common-issues.md` and pull the failure points relevant to this symptom's category (starting, running/misfire, cooling, electrical, fuel, suspension, transmission, brakes, chassis). Combine that with `vehicle-profile.md` and this case's own log — anything already tested and confirmed fine stays ruled out unless new evidence reopens it.

Rank hypotheses on fit-to-symptom first, then commonality on this vehicle, then cost/ease of testing. When two causes fit equally well, put the cheap, common one first — don't lead with a rare or expensive failure just because it's more dramatic.

## Step 4: Recommend tests, not fixes

For each hypothesis, give one concrete, doable test that would confirm or rule it out — specific enough to actually go do ("pull the main relay under the dash by the kick panel and check it clicks/has continuity" beats "check the electrical system"). Order tests cheapest/easiest first. Flag safety issues where they apply: jack stands (never just a jack) under the car, disconnect the battery before working on wiring, relieve fuel pressure before opening the fuel system, let a hot engine/cooling system cool before opening it.

Don't recommend a fix before the hypothesis behind it has actually been tested — the loop is diagnose → test → learn → refine, not guess → replace parts.

## Step 5: Write the case file

Create or update the case file using this structure — append a new dated round rather than overwriting previous ones, so the reasoning trail stays visible:

```markdown
# Betty — [short symptom title]

**Status:** Open | Resolved
**Opened:** [date]
**Vehicle:** 1993 Range Rover Classic 4.2 LWB

## Symptom
[Description as currently understood, updated if it evolves]

## Diagnostic Log

### Round 1 — [date]
**Reported:** [what the user said]

**Hypotheses (ranked):**
1. [Cause] — [why it fits, why it's ranked here]
2. ...

**Recommended tests:**
1. [Specific test for hypothesis 1, safety notes if relevant]
2. ...

### Round 2 — [date]
**Results reported:** [what testing round 1 found]

**Ruled out:** [anything eliminated, and how]

**Updated hypotheses:** [re-ranked, given new evidence]

**Recommended tests:** [next round]

<!-- repeat rounds until resolved -->

## Resolution
**Root cause:** ...
**Fix applied:** ...
**Verified by:** [how the fix was confirmed — problem gone under the same conditions it used to occur]
```

## Step 6: Closing out

When the user confirms a fix worked, close the case: fill in Resolution, set `Status: Resolved`. If the failure is one known to recur (several of Betty's likely gremlins are), say so plainly rather than implying it's gone for good.

If this round surfaced a durable fact about the car itself (confirmed transmission type, confirmed suspension type, a mod discovered under the hood), fold it into `vehicle-profile.md` so it doesn't need rediscovering next time.

## After delivering the report

Tell the user, in plain terms, what you think is most likely and what to go check first — the case file is the record, but the conversation is where they actually find out what to do next. Keep it grounded: you're narrowing down probabilities from known failure patterns, not pronouncing a diagnosis from a single symptom.
