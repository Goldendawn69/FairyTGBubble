# FairyTGBubble — vision and design direction 🧚🫧

> **Living design document:** records ideas and decisions for the new game, not proof of implementation. Project identity: **Goldendawn69**.

## What we are making

An adult protagonist is captured by an Evil Fairy in an enchanted forest. She connects them to a magical bubble machine that slowly changes their appearance and body and, more subtly, their thoughts. A Good Fairy assists from the sidelines, but her powers must be earned by skillful play.

The protagonist is trying to **survive until the Evil Fairy runs out of magical bubbles**. The player can escape partly or substantially transformed. The Evil Fairy wins when mental acceptance reaches its final stage and the protagonist no longer wants to fight.

The core game should be **challenging and strategic**, not simply an animation that plays when a standard bubble shooter ends.

## The key relationship

- **Physical transformation:** Different bubble colours will eventually change different physical features. Exact colours, categories and progression pacing have not been agreed. Physical changes tend to occur more frequently than mental ones.
- **Mental transformation:** Starts early but accumulates more slowly. It represents acceptance: the character gradually rationalises their situation and eventually subconsciously cooperates with the Evil Fairy.
- **Stage 6 — full acceptance:** Definitive game over. Once the protagonist freely stops resisting, there is no further gameplay for that run.
- **Physical completion alone:** Not yet agreed as a defeat condition. The player may potentially escape physically transformed while still resisting mentally.

Later, different bubble effects might shape the protagonist towards a bubbly, nerdy, goth or other appearance/personality outcome. **Do not build those themes in the first prototype.**

## Proposed survival rules

### Separate mode

Build **Fairy Survival** as a distinct ruleset using the existing bubble shooter engine. **Preserve the current Arcade and Puzzles gameplay** and its proven physics.

### A real transformation boundary

A horizontal transformation line sits at a difficulty-based percentage of the playfield height, measured from the top. Suggested starting values:

| Difficulty | Line at | Dangerous lower region |
| --- | ---: | ---: |
| Easy | 75% | Lower 25% |
| Normal | 55% | Lower 45% |
| Hard | 35% | Lower 65% |

These are proposed tuning values, not final balance.

Whenever a bubble **still connected to the ceiling machine** is directly popped below the line, it contributes **1×** its colour's transformation effect. Each bubble is judged using its own **live position when destroyed**, not the location of the fired shot. Pops above the line cause no transformation.

### Reaching the bottom

An attached bubble touching the bottom triggers **2×** its transformation effect. A destructive reaction then moves **straight upwards**, removing bubbles along a narrow vertical connected path. Other attached bubbles directly destroyed below the transformation line contribute their normal **1×** effect; attached bubbles above it contribute **0×**.

If this reaction breaks a supporting path, its unsupported side branches fall according to the existing gravity rules. **Detached bubbles are disconnected from the machine and therefore explode harmlessly, contributing zero transformation at any height.** Successful support-cutting is a defensive strategy.

The exact geometric interpretation of “straight upwards” on the staggered, swinging bubble lattice is **not settled**. We need a playable proof of this mechanic before finalising path width, gaps, junctions, overlapping bottom contacts or timing.

### Continuous pressure and limited magic

The enchanted board should advance continuously and more urgently than the current Arcade game. The Evil Fairy can only produce **a finite total supply of new rows/bubbles**. When her magic runs out and the protagonist still resists, the machine loses its power; the remaining bubble structure becomes harmless and the protagonist is released as they are.

Row timing, scroll speed, total magic, difficulty scaling and exact end-of-run sequence remain open for playtesting.

## The opponent: the Evil Fairy

The fairy should actively try to make the board difficult, but she cannot simply choose every bubble. For a new row she may strategically choose **up to X positions/colours**, with all remaining positions random. Candidate tactics include reinforcing weak supports, complicating an easy match and making low-hanging formations difficult to sever.

The value of X, placement rules and AI difficulty are open. A deterministic evaluator or lightweight game AI should be sufficient; no external language model is required.

**Enemy strategy must remain a contest the player can respond to, not an invisible rigged outcome.**

## The ally: the Good Fairy

Successful bubble clearing charges limited **single-use** support tools. Existing design concepts include Rocket, Match Colour, a player-fired Bomb and brief Increase Gravity. The Good Fairy's assistance should reward skilful shooting, not be available without limit.

Important twist: in Fairy Survival a powerful explosion below the line may directly pop machine-connected bubbles and cause transformation; cutting a support above the line and allowing bubbles to fall is safer. The best tool depends on **where** and **how** it is used.

These tools are not yet implemented as selectable powers in the copied game.

## Mental acceptance and the difficulty curve

Mental change should be about gradually **wanting to accept** the physical transformation, rather than generic disorientation. The player's character might initially dismiss an unfamiliar thought, later rationalise a change, and finally begin assisting the antagonist.

At intermediate stages, planned effects might include an additional unwanted bubble on a shot, new bubbles appearing, a brief faster descent, a changed next-shot colour, or reinforcement of a weak bubble connection. These should begin rarely and become more likely as acceptance increases. Their frequency, triggers and severity require design and balancing.

The player is fighting both the Evil Fairy's machine **and their own character's increasing cooperation**. Full mental acceptance is intentionally unwinnable: it ends the run. Earlier stages should remain recoverable through skilled play and support powers.

Mental bubble effects should be harder for the Evil Fairy to obtain/use than ordinary physical transformation effects. The exact cost and colour distribution are open.

## Art, narrative and accessibility

Aim for readable, expressive fantasy bubbles and clear visual feedback for safe versus dangerous pops, bottom reactions and harmless detachment. Show the protagonist's changing thoughts alongside physical changes without requiring art for the first engineering prototype.

Keep desktop and mobile fair, provide shape/pattern cues beyond colour alone, and retain reduced-motion support. No advertisements or paid tools.

## Proof of gameplay, in order

1. Demonstrate **bottom-contact straight-up destruction** on the existing real physics grid, including side-branch disconnection.
2. Prove transformation accounting: every attached bubble below the line ×1, bottom contact ×2, above line ×0, detached falls ×0.
3. Introduce a continuously descending board and establish balanced rates.
4. Add a finite magic reserve and end-of-run victory/defeat logic.
5. Prototype constrained Enemy AI selection for some incoming row slots.
6. Introduce Good Fairy support powers and incremental mental acceptance/sabotage.
7. Only then build character artwork, scenes, specific transformations and endings.

**Engineering guardrails:** Do not disturb established Arcade/Puzzle movement, sticky new-contact matching, hit detection, valid support physics or their scoring. Do not reintroduce the rejected random Gravity Shift/Orb implementation. Any new effects belong explicitly to Fairy Survival. Keep configuration separate and regression-test each step.
