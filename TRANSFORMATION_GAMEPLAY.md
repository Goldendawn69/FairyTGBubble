# FairyTGBubble — transformation gameplay

**Status:** Design specification and proof-of-concept questions, **not implemented gameplay**. Applies to the planned **Fairy Survival** mode. Existing Arcade/Puzzle rules are preserved.

## Survival objective

The Evil Fairy is using a ceiling-connected magical bubble machine to transform a captive adult. The protagonist must hold out until the Evil Fairy's **finite supply of magic** is exhausted. The player wins while the character is still resisting, even if some changes remain. Reaching **mental acceptance stage 6** means the character stops fighting and **the run ends in defeat**.

## Bubble removal outcomes

The distinction between **machine-connected** and **detached** bubbles is essential.

| Event | Transformation effect |
| --- | --- |
| Machine-connected bubble popped above transformation line | 0× |
| Machine-connected bubble popped below transformation line | 1× of that bubble's colour |
| Machine-connected bubble directly touches bottom | 2× of its colour |
| Attached bubble destroyed by upward reaction below the line | 1× of its colour |
| Attached bubble destroyed by upward reaction above the line | 0× |
| Bubble disconnected from the machine, then falling/exploding | 0× regardless of position |

Classify each bubble individually at the moment of destruction. **Do not** assess an entire match using the shot impact point. One bubble may contribute at most once; the bottom bubble's ×2 replaces, rather than adds to, the normal ×1.

Bubbles detached by removal of a support are no longer powered by the machine. Their eventual floor impact is harmless and cannot count as a transformation event.

## Transformation line and difficulty

Percentage is measured from the **top of the playable vertical board**, not from the launcher.

- Easy: suggested 75%.
- Normal: suggested 55%.
- Hard: suggested 35%.

These are prototype defaults only. Balance must account for both desktop and mobile geometry. The live physical bubble centre/edge convention needs to be selected and used consistently.

## Bottom reaction: straight up, not an entire connected cluster

When one or more **attached** bubbles contact the bottom:

1. Credit the bottom-contact initiating bubble(s) at **2×**.
2. Send a destructive reaction **vertically upwards** through the bubble formation, not laterally across all its physical links.
3. Directly destroyed machine-connected bubbles below the transformation line contribute **1×**; above the line **0×**.
4. Re-evaluate support connectivity after the upward removal.
5. Disconnected side branches fall according to the existing gravity engine and contribute **0×**.
6. Still-connected structures remain in place.

**Open geometry question:** The grid is staggered and attached bubbles swing. We have not agreed if the reaction follows live centres with a width tolerance, steps through a connected sequence with roughly upward links, or stops at a physical gap. These must be visually tested before finalising.

Multiple simultaneous bottom contacts, overlapping reaction paths, interaction with board Bomb Bubbles and resolution ordering are also open.

## Continuous descent and magic supply

Fairy Survival should make the attached formation **descend continuously**, rather than waiting for the inherited Arcade's every-five-shots row drop. Exact scroll cadence, insertion rule, total row supply and end-of-magic cleanup are undecided.

The Evil Fairy may strategically select **X bubbles per incoming row** while the rest are randomly filled. X, allowed placements, visibility and tactics will be configured and playtested. The enemy AI should not get unrestricted access to a perfect board or infinite hostile bubbles.

## Physical and mental progression

Eventually colours will map to transformation categories. Exact associations, number of colours, proportional rarity and thresholds are open. The first prototype only needs **per-colour numerical counters**.

Physical changes accumulate more often. Mental effects occur less frequently but should be present early. They represent increasing **acceptance and subconscious cooperation** rather than a loss of intelligence.

Working mental stages:

| Stage | Narrative direction | Gameplay intent |
| --- | --- | --- |
| 0 | Determined resistance | Normal |
| 1 | Unfamiliar thoughts/doubt | Primarily narrative |
| 2 | Rationalisation | Rare interference |
| 3 | Partial acceptance | Some additional bubble-related interference |
| 4 | Growing cooperation | Stronger/more frequent interference |
| 5 | Almost surrendered | Severe pressure; resisting remains possible |
| 6 | Full acceptance | **Immediate game over, no further resistance** |

Potential (not approved) effects: extra bubble attached to the shot, new bubbles spawned on the board, a brief descent acceleration, altered next-shot colour, magical reinforcement. Frequency, safeguards and story explanations are unresolved. Mental progress should **not** assume each body part has its own accepted/rejected preference; individual acceptance and specialist themes are future possibilities.

## Good Fairy support

Potential earned, one-use tools: Rocket, Match Colour, player Bomb and Increase Gravity. Their charging, targeting and transformation impact need reassessment for this mode. A bomb fired into attached bubbles **below the line may cause transformation**; removing a ceiling support to let branches fall is safer.

## Open design decisions

- What exactly is a vertical connected path in a swinging staggered hex-grid?
- Do bottom reactions stop at the first empty vertical gap, or find a narrow upward route?
- When is a bubble's machine connection tested across multi-stage explosions?
- How many colours? Which physical and mental categories? How rare is mental influence?
- How does transformation accumulation convert into visual/story stages?
- How much magic does the Evil Fairy have? What does “run out” mean if old bubbles remain?
- What row frequency and descent speed produce a fair but threatening game?
- What choices can the Enemy AI make with X slots, and how are unfair placements limited?
- When and how do Good Fairy tools recharge and mitigate interference?
- Is complete physical transformation alone a separate loss? **Not decided.**

## First proof-of-concept test cases

- A match crossing the line charges only the bubbles below it, each once.
- A bottom initiator is credited ×2, not ×3.
- A straight-up blast removes its narrow intended path without swallowing lateral branches.
- Detached side branches fall and score **no transformation**.
- A side branch still independently connected to the machine stays on the board.
- Actual rendered positions and blast geometry remain consistent while swinging.
- Arcade and Puzzle modes retain their original win/loss and scoring behaviour.
