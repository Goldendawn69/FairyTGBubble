# FairyTGBubble — TG game plan

**Document filename:** TGFiaryPlan.md (retained as requested)  
**Repository:** https://github.com/Goldendawn69/FairyTGBubble  
**Status:** Concept and gameplay design, **not an implementation request**  
**Last consolidated:** 2 November 2026  
**Planned mode:** Fairy Survival (separate from existing Arcade and Puzzles)

> This is the **master context and design plan for the transformation-game concept**. It records what has been agreed, possible future designs, what remains undecided, and the experiments needed to find out whether the game is enjoyable. It does **not** claim that Fairy Survival, characters, transformation tracking, or Enemy AI already work.
>
> Related source-of-truth documents: [VISION.md](VISION.md), [TRANSFORMATION_GAMEPLAY.md](TRANSFORMATION_GAMEPLAY.md), [PROJECT_STATUS.md](PROJECT_STATUS.md), [GRAVITY_DESIGN.md](GRAVITY_DESIGN.md), and [effects and tools.md](effects%20and%20tools.md). Where detailed behaviour is still being discussed, do not quietly treat a proposal here as an approved implementation specification.

## 1. The game we want to make

**FairyTGBubble** is a fantasy TG (gender transformation) bubble-shooter survival game for an **adult protagonist**. The player is fighting to escape a magical transformation machine while an Evil Fairy tries to change both their body and their mind.

The core attraction is **slow, visible, consequential transformation with active resistance**. Transformation is not merely a cutscene after a normal game-over. The same shots and bubble formations the player deals with are the mechanism that gradually change the character.

### Fictional premise

An adult traveller enters an enchanted forest and is captured by an **Evil Fairy** who enjoys transforming unwelcome visitors. She connects her captive to a magical bubble machine in the canopy/ceiling. Each enchanted bubble has an influence determined by its colour or type. The machine's magic can reach the captive only through bubbles still **connected to the machine**.

A **Good Fairy** tries to help. She cannot simply release the captive, but can lend powerful, limited bubble-clearing abilities when the player earns enough energy through play.

The Evil Fairy is not all-powerful: she has a **finite reserve of magic** and eventually runs out of new bubbles. If the protagonist can keep resisting until then, the machine ceases to function and the fairy must let them go — possibly changed, annoyed, or amused, and flying away giggling.

If the protagonist's **mental acceptance reaches stage 6**, the protagonist stops resisting and the run ends immediately. At that point the Fairy has won even if her reserve is not exhausted.

### Design principles

- **A proper game first:** Skill must noticeably change how much transformation the player suffers.
- **Transformation is a cost of gameplay:** Popping too late can be a small defeat even if the shot clears bubbles.
- **The player can make trade-offs:** Take a small transformation now, or risk a much larger one if a hanging group reaches the bottom.
- **Defensive tactics matter:** Sever supporting bubbles high on the board and let unsafe branches fall harmlessly.
- **Physical and mental changes evolve alongside each other:** Mental change is rarer/slower, *not* absent until the very end.
- **Mental changes mean acceptance, not stupidity:** Eventually the character begins to assist the Evil Fairy, fighting the player's intentions.
- **Full mental acceptance is an intentional loss, not an impossible difficulty setting to play through.**
- **Keep the existing engine sound:** Reuse BubbleFun's proven spring motion, matching, contact physics, gravity, bombs, aiming and tests without rewriting them for another mode.

## 2. Where Fairy Survival fits

This is a **new gameplay mode** with distinct progression, failure, victory and pacing rules. It must **not replace or silently change** the existing Arcade and Puzzle modes.

The inherited game currently has colour-matching shooter gameplay, elastic/sticky hanging formations, swinging contacts, physics-based detachments, gravity cascades, bombs and depth-based scoring. These make it unusually well suited to this concept: a player can strategically remove supporting bubbles and safely release a dangerous mass.

**What does not exist yet:** Fairy Survival rules; a transformation boundary; continuous enchanted descent; the upward bottom explosion; colour-to-transformation tracking; an Evil Fairy row-placement opponent; the Good Fairy's charging UI; character graphics; or mental acceptance mechanics.

The primary task is to find out whether this **new ruleset** produces a challenging, satisfying game, before building narrative production systems around it.

## 3. How the moment-to-moment game works

The Evil Fairy feeds a constantly advancing formation of enchanted bubbles from the machine at the **top**. The formation advances more aggressively than the original Arcade mode and places the shooter under mounting pressure.

The player shoots bubbles into the formation, matches groups and uses gravity intelligently. A popped bubble's **own colour and own position** determine whether it triggers transformation.

The basic struggle:

1. The Evil Fairy has a finite number of magical bubbles / incoming rows in reserve.
2. The current formation moves steadily towards the protagonist. Incoming rows add more pressure.
3. The player fires and creates matches, bombs or support breaks.
4. Bubbles destroyed while still powered by the machine **below the transformation line** cause their colour's transformation effect.
5. Bubbles removed **above the line** are harmless.
6. Bubbles **disconnected from the machine** fall and burst harmlessly, even when they are below the line.
7. If an **attached** bubble reaches the bottom, it causes a stronger change and triggers a destructive **straight-up** reaction.
8. The player keeps going after minor and major transformation events. There is **no ordinary Arcade-style instant game-over** merely because something reaches the bottom.
9. The player wins if the Evil Fairy's magic runs out before full mental surrender; the player loses at **mental acceptance stage 6**.

**Central tension:** An apparently successful large match can still be harmful if several machine-connected bubbles are popped too low. Conversely, a difficult shot at a high support can release a large dangerous cluster with **zero transformation**.

## 4. The transformation line and difficulty

There is a horizontal transformation line. Its location is measured as a percentage downwards from the **top of the playable board**. Every **machine-connected bubble that is popped below this line** causes **one normal unit** of its colour's transformation effect.

Suggested initial difficulty values (for prototype tuning, not final balance):

| Difficulty | Line location | Area that causes transformation |
| --- | ---: | --- |
| Easy | 75% of playable height | Bottom 25% |
| Normal | 55% | Bottom 45% |
| Hard | 35% | Bottom 65% |

A higher transformation line means the player must clear bubbles much earlier, making play harder. Descent rate, Enemy AI placement budget, total available magic and special-bubble distribution may also eventually vary by difficulty, but **none of those extra difficulty settings is approved yet**.

**Important accounting rule:** Calculate each bubble's effect separately, using the actual live position **when it is destroyed**. A match that spans both sides of the line can produce both harmless and harmful pops. Do **not** decide the whole match's effect based on the fired bubble's collision position.

Open geometry detail: choose a consistent centre/edge interpretation for crossing the line on both desktop and mobile; do not invent it in the implementation.

## 5. Bottom contact: the dangerous upward reaction

An attached bubble reaching the bottom is a **major mistake**, but it is *not* automatically the end of a run.

### Agreed intended behaviour

1. The **bubble(s) making bottom contact** receive **2×** their normal transformation effect, **not** 1× plus another 2×.
2. An explosion/reaction travels **straight upwards**, not sideways through every physically connected bubble.
3. Each additional **machine-connected bubble directly destroyed in the path** contributes **1×** of its colour's effect if it was below the transformation line, otherwise **0×**.
4. The directly destroyed bubbles disappear.
5. Existing connectivity/support logic determines whether any side branches have lost all paths to the machine.
6. Those **detached** bubbles fall under the game's ordinary gravity, hit the floor and burst with **0× transformation**. They have lost their magical link.
7. Branches still independently connected to the machine remain in play.

The vertical blast should be a **visible upward cascade**, not an instantaneous unexplained reset.

### Crucial distinction: connected versus falling

| Bubble event | Transformation contribution |
| --- | --- |
| Machine-connected bubble directly popped **above** the line | 0× |
| Machine-connected bubble directly popped **below** the line | 1×, based on its colour |
| Machine-connected bubble **touches bottom** | 2×, based on its colour |
| Extra bubble **directly hit** by the upward blast below the line | 1× |
| Extra bubble **directly hit** by the upward blast above the line | 0× |
| Bubble **loses machine connection and falls** from a match, blast or support cut | **0×, even below the line** |

This principle also applies to otherwise successful gravity cascades: falling debris should never generate transformation points merely because it crosses the danger line or bursts at the bottom.

A bubble can award **at most one transformation event**. Count a bottom initiator at 2× and never again as an ordinary popped bubble.

### The still-open "straight up" problem

The inherited bubble playfield is a **staggered hex grid**, and individual formations **swing**. A literal fixed grid column might fail to match what the player sees. Conversely, following all physical connections could destroy nearly the entire board, which is **not** the intended rule.

We must prototype and visually compare a few narrowly vertical propagation options:

- A slim vertical corridor following bubbles' **live rendered centres**.
- A succession of physically connected neighbours that each continue **generally upwards**, with a strict sideways tolerance.
- A path that stops at an actual gap, rather than acting as a laser through empty space.

These are **alternatives to test**, not decisions. Simultaneous bottom contacts, crossing blast paths, animation sequencing, bombs caught in the path and rechecking support mid-reaction also remain open.

**First gameplay proof:** The right narrow upward bubbles explode; branches losing their support fall harmlessly; independently supported branches remain.

## 6. Winning, losing and the fairy's magic budget

### Winning is survival, not clearing the entire board

The Evil Fairy has a **finite number of magical bubbles/rows** she can supply. The player tries to outlast this supply while preserving enough mental resistance to keep fighting.

Once her magic really runs out, she must let the protagonist go **as they are**. She may fly away giggling even when she failed to achieve complete surrender.

Possible outcomes include:

- **Strong victory:** Very few physical and mental changes.
- **Costly victory:** Significant physical changes and some mental acceptance, but the character still chooses escape.
- **Near-miss victory:** The protagonist has become very different physically, yet has not surrendered mentally.
- **Defeat:** Mental acceptance reaches **stage 6**, and the protagonist no longer fights the machine.

These are potential **narrative variations**, not separate implemented end screens or fully defined score thresholds.

### A timer or a finite number of rows?

**Agreed direction:** Finite magical supply, rather than a free-running timer alone. A finite number of incoming enchanted rows is a promising concrete representation of that supply. The remaining magic can give the player a sense of how long they must survive.

**Still to decide:** Whether the machine stops the moment the last row is emitted or after that row has finished advancing; whether the remaining existing formation drops harmlessly on expiry; and how shot pacing relates to elapsed time. The story outcome is clear, but the precise final gameplay event still needs specification.

### Full physical transformation

**Not decided:** Whether complete physical transformation on its own is also a loss. The current design deliberately separates physical progress from mental acceptance; surviving in a physically transformed form while still resisting is a strong possible ending. Do not impose a physical game-over without discussion.

## 7. Transformation progression, colours and visual data

### An unresolved design choice: whole-body versus individual body parts

We originally considered giving each bubble colour its own body-part progression (hair, face, chest, hips, legs, voice, etc.), plus much slower mental acceptance. **This is no longer assumed to be the final progression model.** It allows varied outcomes, but it also risks an incoherent appearance (such as several fully changed features beside otherwise unchanged features), creates more balancing variables, and makes artwork/paper-doll assembly substantially harder.

There are **three models worth assessing**:

| Model | How it works | Main trade-off |
| --- | --- | --- |
| **A. Whole-body progression** | Physical transformation advances on one shared track. Authored milestones gradually alter different features in a coherent sequence. Mental acceptance advances separately. Clothing/presentation can follow milestones. | Simple and predictable, but gives less variation in exactly which feature changes first. |
| **B. Individual body-part progression** | Each body part advances independently according to bubble category. Mental acceptance remains separate. | Flexible, but may produce awkward combinations and demands many visual combinations. |
| **C. Hybrid progression** | One primary physical track keeps the overall transformation coherent; optional cosmetic/feature traits can be added later if they genuinely improve gameplay or art. Mental acceptance is separate; clothing/presentation follows the route or has a small distinct state. | Preserves extensibility without committing to many independent body-part meters. |

**Updated preferred direction:** Keep the three options above as design history, but proceed conceptually with a coherent **whole-body** track plus separately progressing **face, hair, clothing/appearance and mind**. Stage thresholds, bubble categories and artwork compatibility are still open. No renderer is being implemented yet.

A unified physical track does **not** mean every aspect changes simultaneously. Different milestones can show early hair/face changes, then body proportions and voice, and eventually a more complete transformation. Those are **authored changes within an overall progression**, rather than isolated body parts independently reaching 100%.

### Physical, mental, clothing and route are different concepts

- **Body:** One coherent full-body transformation progression, not separate limbs or individual anatomical areas. Milestones are **open**.
- **Face:** Facial changes can progress independently, but must fit the current body model.
- **Hair:** Hair length, style and colour can progress independently.
- **Mental acceptance:** Its own slower progression, represented by the working 0–6 stages. Stage 6 is definitive game over; it must not become a mere visual substage of physical progress.
- **Clothing/presentation:** Clothing, makeup and accessories can progress at their own rate, using images compatible with the current body model.
- **Style route:** The chosen kind of woman (for example bubbly, nerdy, goth, or many other possibilities). **The first bubble that actually transforms the protagonist locks the run's route.** Later physical, mental and clothing changes take on that route's appearance and flavour.

Importantly, the **style lock is a separate axis from physical progression**. A route answers *what kind of woman is the character becoming?*; progression answers *how far has the transformation gone?*

### Bubble meanings if we simplify physical progression

The original idea was one colour per body part, with rarer mental bubbles. Simplifying to a whole-body track raises an important question: **what does each colour now do?** Possible future choices include colours that contribute to a common physical meter, separate rare mental-influence bubbles, and/or style information carried by bubbles.

**Do not assume that every physical colour still corresponds to an individual body part.** Nor should we invent a new colour taxonomy before playtesting readability and strategy.

The first gameplay prototype can still record **per-colour transformation events and counts for diagnostics**. This does not oblige the finished character model to retain a separate visible meter for each colour/body part.

### Capture character state independently of the image renderer

The game should capture enough **structured state** for portraits or paper-doll graphics to be added later without changing bubble physics or the transformation engine.

A *proposed* minimal shape (illustrative, **not an approved data schema**) would record:

- `styleRoute`: unset until the first genuine transformation event locks it; then fixed for that run.
- `physicalProgress`: an overall body transformation value or stage.
- `faceProgress`: a separate face transformation value/stage.
- `hairProgress`: a separate hair transformation value/stage.
- `appearanceProgress`: clothing, makeup and accessory progression.
- `mentalAcceptance`: a separate acceptance value/stage, reaching game over at 6.
- `transformationEvents`: optional event information (cause, colour/type, amount, time/sequence, resulting state) useful for replay, debugging, analytics and future presentation.

Avoid double sources of truth: if clothing stages can be calculated reliably from progress and route, do not duplicate them in persistent game state unnecessarily. If extra physical traits become meaningful, add them through an **extensible schema**, not a premature fixed list of every body part.

An eventual image system should **read this character state**, not dictate which physics or scoring events happen. Correct separation makes switching rendering approaches easier, although producing and aligning the artwork remains a real content challenge.

### Milestones and pacing

The first proof-of-gameplay should count actual changes and measure how often they occur before inventing final visual thresholds. Later, progress can map to small early changes, coherent physical milestones, clothing/presentation changes, thoughts, interference and route-specific art.

Keep physical and mental changes concurrent in time, with mental transformation **significantly rarer and slower**. Physical progression alone is **not currently confirmed** to be a loss condition.

## 8. Mental acceptance: gradual self-sabotage

### What mental change means

This is **not primarily confusion, reduced intelligence or arbitrary loss of concentration**. The Evil Fairy is changing what the protagonist **wants**.

The protagonist may:

1. Notice an unfamiliar, favourable thought about a change and reject it.
2. Begin rationalising or appreciating some physical changes.
3. Find their resistance emotionally harder to maintain.
4. Subconsciously create openings for more magical bubbles.
5. Start acting in ways that help the fairy.
6. Finally accept the new self and **stop fighting back**.

The player remains determined to win while the **character gradually disagrees with them**. That player/character conflict is a central dramatic feature.

**Pacing requirement:** Physical and mental transformation happen in parallel; mental events are **less common** and progress more slowly, but should occur from early stages. Do not reserve all thoughts until the body is almost complete.

### Working mental stages — provisional scale, fixed loss endpoint

| Stage | Character's direction | Intended gameplay direction |
| --- | --- | --- |
| 0 — Resisting | Wants to escape unchanged | Normal shooting |
| 1 — Doubt | Brief positive thoughts, then alarm | Mostly narrative; little or no sabotage |
| 2 — Rationalising | Starts explaining away changes | Rare, minor subconscious interference |
| 3 — Partial acceptance | Likes aspects of the change | More kinds of unwanted bubble effects |
| 4 — Cooperation | Subconscious sometimes helps the fairy | More frequent or stronger interference |
| 5 — Nearly surrendered | Struggles to keep resisting | Serious pressure, but **victory still possible** |
| **6 — Full acceptance** | **No longer wants to escape** | **Immediate game over; no further shooting** |

These names and intermediate boundaries are **proposals**. The final condition at stage 6 is agreed: full acceptance ends the run, and a mathematically impossible victory **after** that point is intentional.

### The mental effects should follow the psychology

As acceptance increases, effects should increasingly appear to originate from the protagonist's changed instincts rather than an omniscient game simply cheating.

Candidates to experiment with, **not yet approved as final abilities**:

| Effect | Gameplay consequence | Possible story reading |
| --- | --- | --- |
| Enchanted / extra shot | An unwanted bubble joins the fired shot or is added to the board | The character subconsciously preserves the fairy's work |
| Bubble growth | New machine-connected bubbles materialise in an awkward spot | The character assists the enchantment |
| Faster descent | Machine advances faster for a short, visible period | Resistance slips and more magic passes through |
| Colour betrayal | The next shot's colour changes before firing | Their instincts work against the original plan |
| Reinforced support | A weak support acquires another bubble/connection | They protect a vulnerable group |
| Visual distraction (optional) | Brief, readable magical effects obscure a small board area | The changing character is distracted by the enchantment |

Earlier ideas such as simply slowing down the controls, making the aim physically inaccurate or treating "severe" as meaningless hesitation **are not the preferred direction**. The strongest ideas add **new bubble challenges and consequences**, rather than silently breaking input.

The Evil Fairy's mental transformations should be **rarer / more expensive to arrange** than ordinary physical transformations because they ultimately cause a full surrender and can make future resistance harder. What "expensive" means in terms of the fairy's row-placement budget or bubble types has not yet been decided.

### Fair, understandable sabotage

Some uncertainty is intentional; players should feel challenged. Nevertheless, unexplained events that destroy an already fired perfect shot may feel unfair rather than difficult.

Potential safeguards to investigate: telegraphed activation; a short delay; selecting from a bounded pool of effects; limits on simultaneous sabotage; and counterplay via Good Fairy powers. **No probability tables or cooldowns have yet been agreed.**

There is a purposeful **downward spiral**: changes increase interference, making more changes likely. This is part of the game's identity. Do not accidentally eliminate the risk of eventual defeat simply to ensure every player can always recover. The only absolute line is mental stage 6, where the character stops resisting.

### Individual acceptance of body parts: future, not core

A later concept is the character becoming more accepting of **particular features**, possibly generating bubbles related to changes they now like. That would require tracking acceptance for hair, face, body and so on **separately** from general mental acceptance.

This is interesting for deeper story and personality variations, but **not required** for the first prototype. The initial mental system should track overall acceptance only.

## 9. The Evil Fairy as an active enemy

The Evil Fairy wants to make defence progressively more difficult, particularly by creating connected structures that are hard to sever before they reach the transformation zone.

The planned Enemy AI should have a **limited number X of deliberate colour/placement choices in each new row**. The remaining slots should be filled randomly. It must not control every bubble without constraint.

Possible strategic goals:

- Reinforce dangerous low-hanging formations.
- Block easy support cuts.
- Add awkward colours that make matching harder.
- Extend a chain towards the bottom and increase the chance of a vertical blast.
- Spend some of her limited choices on rarer mental-influence bubbles.
- React to the player and remaining magic without "knowing" impossible future inputs.

**Still to decide:** X at each difficulty, how row shape/size is chosen, whether and how the fairy sees the next projectile, how much magic a selected bubble costs, when selection happens, and whether she can reserve powerful bubbles for later.

This does **not** require a large language model. A deterministic board evaluator plus bounded randomisation is a plausible starting approach, but the AI algorithm has not been selected.

## 10. The Good Fairy and player tools

The Good Fairy is the narrative reason the captive receives limited defensive powers. These should be **earned through successful play**, especially larger pops and clever gravity cascades, not freely available every shot.

Existing design concepts in [effects and tools.md](effects%20and%20tools.md):

- **Rocket:** Straight-line destruction; only bubbles it passes through.
- **Match Colour:** A shot that adopts the target colour, helping create a match.
- **Player Bomb:** A fired, medium-sized radial explosion.
- **Increase Gravity:** A brief, chosen increase that can break sufficiently weak supports.

Inherited design rule: **Each tool has its own charge; one use consumes its full charge; it must recharge before another use.** The qualifying charging events in the inherited document are a starting point, not yet ratified for Fairy Survival.

**Crucial new-mode twist:** A bomb may cause **1× transformation for machine-connected bubbles below the line** that it destroys. A carefully aimed rocket/shot used to cut a high support may free a much larger dangerous branch without any transformation. Tools are therefore **strategic choices**, not universal get-out-of-jail cards.

The Good Fairy may later gain mental-interference countermeasures, but no such power has yet been selected.

## 11. Narrative beats and player feedback

This game needs gradual characterisation, not a generic "transformation +1" alert.

### Early run

The captive is frightened and determined. A few small physical changes occur. Mental changes begin as intrusive, surprising thoughts — the character notices them, rejects them and returns to the shooting challenge.

Illustrative thought: *"Why did I think my hair looked pretty like that? I need to get out of here."*

### Middle run

Physical changes are becoming obvious. The captive increasingly rationalises them: perhaps one particular change "isn't so bad". At the same time, occasional extra bubbles or a brief descent acceleration make it obvious the character is beginning to cooperate unconsciously.

### Late run

The character is now caught between escaping and wanting the transformation to continue. Subconscious assistance is stronger and more frequent. The player's skills and earned Good Fairy powers can still secure a difficult victory **before** stage 6.

### Endings

- **Victory:** Fairy magic depleted; the machine stops. The captive is released with accumulated physical and mental changes intact (exact narrative depends on eventual progression rules).
- **Defeat:** Mental acceptance reaches stage 6. There is no meaningful desire to continue fighting, so the shooting ends. The Evil Fairy has achieved her goal.

**Tone:** Magical, cheeky and increasingly unsettling from the protagonist's perspective. The Evil Fairy is playful and manipulative; the Good Fairy is genuinely helpful but limited. Future tone/visual-narrative detail needs deliberate design. Keep characters explicitly adult.

### Presentation needs (later)

- A readable status for remaining Evil Fairy magic, current mental acceptance and physical categories.
- Clear indication of the transformation line and the danger of directly popping attached bubbles below it.
- Distinct visual feedback for **harmless falling/detachment**, **minor transformation**, and **2× bottom impact**.
- A character portrait/sequence that changes gradually rather than jumping between unrelated images.
- Optional thought/dialogue snippets that communicate *why* mental interference happened.
- Desktop/mobile layouts and reduced-motion support; avoid relying on colour alone.

Do **not** spend the first implementation phase producing extensive character art or branching scenes.

## 12. Style lock, different kinds of women and future character pictures

A key creative goal is that runs can lead to **many different kinds of women**, not a single generic end state.

### First-transforming-bubble style lock

**Agreed direction:** The **first bubble that actually causes a transformation** selects and **locks** the run's style route. It is not simply the first bubble fired, or the first bubble that is popped safely.

- Popping a machine-connected bubble **below** the transformation line can establish the route.
- A bubble making **bottom contact** and causing transformation can establish the route.
- Harmless pops above the line and detached falling bubbles do **not** establish a route.
- Once locked, subsequent transformations develop **within the chosen route** rather than repeatedly switching between incompatible styles.
- Open: how route information is encoded in bubble colour/type; what happens if a mental bubble is the first effective one; and which event wins if several bubbles cause the first transformation simultaneously.

The style lock should shape **both pictures and narrative/mental transformation**. Acceptance, thoughts, mannerisms and eventual appearance can be expressed differently for different routes without requiring completely different core physics.

### A growing range of styles

**Bubbly/girly, nerdy and goth** are only three illustrative examples, **not the limit**. Other possibilities might include elegant, sporty, fashionable, bookish, fairy-like, posh, shy or numerous additional styles. No list is final, and the design should avoid hard-coding a fixed three-route system or reducing people to caricatures.

### Pictures are an intended feature, not an optional afterthought

The finished game is intended to include **illustrated character pictures** so the player can see the protagonist gradually change within their locked route. We expect route-specific character appearances and a progression of visual stages, potentially including poses, facial expressions, hair, clothing and presentation changes.

**Stage-aware paper dolls are now the preferred future direction**, but final art technology and implementation are still postponed. Keep these three approaches for comparison:

1. **Pre-drawn images:** Finished illustrations for each route and transformation milestone.
2. **Stage-aware paper-doll/layered art — preferred:** Each style route has multiple compatible body-stage bases, with independent face, hair, clothing, makeup and expression layers selected using character state.
3. **Hybrid art:** Pre-drawn base/stage illustrations with a smaller set of interchangeable layers or overlays.

### Stage-aware paper-doll direction (future work)

The **first bubble that genuinely transforms the character** still locks the style route. Each route has its **own model**, with several **body-stage base illustrations** showing a gradual, coherent whole-body male-to-female change. There is no single universal paper doll that must fit every body shape or every feminine route.

Within that route, **body, face, hair, clothing/appearance and mind can progress at different speeds**:

| Track | Model/visual consequence |
| --- | --- |
| **Body** | Selects a compatible full-body base for that point in the transformation |
| **Face** | Selects a face variant aligned to the current body base |
| **Hair** | Selects a hairstyle/length/colour variant, fitted to the body/face base |
| **Clothing/appearance** | Selects clothes, makeup and accessories suited to the body base and locked route |
| **Mind** | Controls slower acceptance, dialogue, possible expression changes and game interference; **stage 6 is game over** |

For example, a character might have an early body stage while hair and face are further along and clothing is barely changed. The game should **allow that difference without producing broken or mismatched art**. We do *not* want to track independently transformed legs, chest and hips.

**Data before images:** The game should record the locked style route and each broad transformation track separately. An eventual renderer reads these values and chooses the correct body base and compatible layers. Bubble physics and transformation progress must not depend on paper-doll image filenames. The existing \`transformationEvents\` concept remains useful for tracing progress.

**Artwork caveat:** Multiple body bases reduce the mismatch problem but do not eliminate the effort of drawing/fitting outfits, face layers and hair across stages. Later, test **two body bases, two face states, two hair states and two clothing states for one route** before adopting a renderer. Investigate anchors, layer ordering and allowed combinations.

**Still open:** Exact stage counts and progress thresholds, illustration/animation format, whether clothes and makeup need separate layer types, asset compatibility fallbacks, and how bubble colours map to these broader tracks. These choices should wait until after the first bubble-physics proof of concept.

Trade-offs to investigate later include asset volume, consistency of proportions across stages, animation, transparent layers, memory/performance on desktop and mobile, how many routes are practical, and whether character art is created manually or with image-generation assistance.

**Architectural commitment now:** Data must describe the character's **route and progression** without assuming any of these three rendering methods. **No art pipeline or paper-doll engine is needed for the first physics/gameplay prototype.**

**Still open:** How many visual stages, how to handle layer compatibility, whether makeup and clothing need separate artwork layers, and the relationship between stage milestones and a physical/mental transformation event.

## 13. Risks to confront before implementation

### Risk A — no agency

If continuous descent makes all transformations unavoidable, this is not a satisfying survival game. Test whether high skill, targeting supports and tool use genuinely improve outcomes.

### Risk B — near-automatic wins

If even casual play can safely destroy everything before it reaches the line, the premise lacks pressure. Tune line height, descent pace, row composition, magic supply and later Enemy AI **with measured playtesting**.

### Risk C — enormous transformations from one event

A bottom explosion might remove many bubbles at once. Because each colour contributes separately, a single cascade could produce a disproportionate amount of change. Do **not** silently cap changes; evaluate actual event distributions and discuss any cap/overflow mechanic.

### Risk D — sabotaging the player's precision

Do not create false aiming geometry: the rendered bubble, guide and actual collision must remain consistent. Mental interference should be an explicit gameplay event, not secretly incorrect physics.

### Risk E — breaking the original engine

The inherited code has hard-won gravity, sticky contacts, support connectivity, bomb chains and depth scoring. Fairy Survival should own its own progression and loss rules. Maintain strong regression tests for Arcade and Puzzles.

### Risk F — collapsing all progress into "mental = last"

Mental change must start early, but should not advance as quickly as physical change. Its rare event frequency is important to pacing and to the meaningful stage-6 defeat.

## 14. Practical proof-of-concept plan

The following is a **proposal for incremental experiments**, not permission to start coding everything immediately.

| Phase | Prove | What to inspect |
| --- | --- | --- |
| P0 — Mode isolation | Fairy Survival can eventually have different rules without altering Arcade/Puzzles | No regressions or shared-state leakage |
| P1 — Bottom blast | Attached bottom contact makes an understandable narrow **upward** path explode | Path follows live motion and does not swallow lateral branches |
| P2 — Detachment | Removed supports let side branches fall and burst harmlessly | Independent supports remain; disconnected pieces count 0× |
| P3 — Transformation line | Normal 1× / bottom 2× / safe 0× effects calculated per bubble | Crossing matches and overlapping events counted once |
| P4 — Continuous descent | Pressure rises independent of the player's shot count | Aiming and physics remain fair and legible |
| P5 — Finite magic | A run genuinely ends with survival victory after a limited supply | No endless loop when the last row arrives |
| P6 — Fairy placement budget | A limited number X of placements noticeably creates tougher formations | Remaining bubbles random; matches still realistically playable |
| P7 — Good Fairy | One-use charged tools reward skill | Can be strategically harmful if fired below the line |
| P8 — Mental system | Slower acceptance and bounded interference increase difficulty | Stage 6 ends the run; earlier stages still allow skilled resistance |
| P9 — Presentation | Physical and mental changes visibly and narratively accumulate | Story follows actual state, not random unrelated cutscenes |

### Essential prototype tests

- A 3+ match spanning the line transforms only its still-attached lower bubbles.
- A matching/physics contact that pops below the line follows the same rules as a shot-created match.
- A bottom initiator gives **2× once**, never both 2× and 1×.
- A multi-colour upward reaction credits each **directly destroyed** machine-connected lower bubble independently.
- Side branches detached by the reaction fall **without transformation**, however low they are.
- A branch with another independent machine support does **not** fall.
- Bottom reactions cannot count the same bubble twice through multiple paths, explosions or delayed fall.
- Live rendered bubble positions, collision geometry and upward-effect path remain consistent.
- Increased mental acceptance can genuinely make survival more difficult, without secretly falsifying shot hitboxes.
- Finite magic eventually terminates play by victory if stage 6 has not been reached.
- Arcade and Puzzles retain the inherited outcomes and gravity/scoring behaviour.

## 15. Open decisions register

These need discussion or playtesting; they should **not** be decided by an AI coding agent without approval.

1. **Exact upward propagation:** fixed vertical corridor vs upward-connected path; how to handle gaps and swaying.
2. **Bottom impact batching:** multiple bubbles touching simultaneously, overlapping blast paths and animation ordering.
3. **Bomb/special interactions:** when directly destroying an attached board special triggers its own effect.
4. **Transformation counting:** precise line-crossing convention, minimum units for visible steps, whether any cap exists.
5. **Broad transformation progression:** preferred body, face, hair, clothing/appearance and mind tracks; specific thresholds, bubble categories and layer fit are still undecided.
6. **Bubble colours/types:** how colours contribute to physical versus rare mental progress if separate body-part meters are removed.
7. **Physical completion:** whether it can coexist with successful survival; any non-mental failure conditions.
8. **Magic reserve:** rows versus individual bubbles, last-row resolution and victory timing.
9. **Descent:** scrolling rate, acceleration, input behaviour during continuous physics motion and difficulty scaling.
10. **Enemy AI:** strategically selected slots per row, costs, placement legality and fairness.
11. **Mental interference:** which events are best, how often they occur, warning/counterplay and player agency.
12. **Good Fairy tools:** earning rates, cooldown/charge conditions and safe interactions with the line.
13. **First-bubble style lock:** what metadata determines the route; mental-first and simultaneous-event tie-break behaviour.
14. **Visual presentation:** stage-aware multiple-body-base paper doll currently preferred; confirm fit, stages, art workload and desktop/mobile performance before implementation.
15. **Clothing and identity:** clothing/appearance progresses independently; decide compatible artwork, variants and whether makeup needs its own layers.
16. **Narrative progression:** protagonist identity, how progression shapes dialogue, and eventual endings.
17. **Specialised feminine routes:** which archetypes exist, route-specific acceptance, and how to scale beyond three examples.
18. **Score relation:** Should depth-based points and high-risk clearing charge abilities, improve final score or alter the magical assault? Not decided.

## 16. Hand-over instructions for future AI work

When another AI or coding session opens this project:

1. Work only in **Goldendawn69/FairyTGBubble**, unless explicitly instructed otherwise.
2. Read this document, VISION.md, TRANSFORMATION_GAMEPLAY.md, PROJECT_STATUS.md and relevant original physics/special documents.
3. Treat **Agreed** as gameplay intent, **Proposed** as a possible implementation to test, and **Open** as requiring discussion.
4. Do not assume that documented features are implemented. Inspect the actual current source, version and tests.
5. Keep the original Arcade and Puzzles available and working.
6. Make small, testable branches/PRs; separate gameplay, physics, scoring, transformation state, Enemy AI and presentation.
7. Do not reintroduce the original rejected random Gravity Shift / Orb system.
8. Verify real gameplay visuals in addition to automated tests, particularly moving supports and the upward blast.
9. Prioritise **proving the central loop** before adding specialised transformations, AI-generated character art or a large dialogue system.
10. Do not hard-code final difficulty, colours, mental frequencies or progression thresholds until they have been discussed and tested.

---

**One-sentence goal:** Build a strategically demanding bubble-shooter in which an adult captive resists an Evil Fairy's finite magical assault, carefully avoiding machine-connected pops below a transformation line and using gravity to escape — while physical transformation and slower, insidious mental acceptance increasingly turn the captive against their own efforts to survive.
