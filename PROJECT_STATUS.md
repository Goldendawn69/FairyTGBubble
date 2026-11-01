# FairyTGBubble — project status

**Repository:** https://github.com/Goldendawn69/FairyTGBubble  
**Owner:** Goldendawn69  
**Baseline version:** 0.10.0  
**Scope of this documentation update:** Rebrand the independent copy and record the new fairy-survival design. **No Fairy Survival code has been built yet.**

## Inherited working baseline

The current copied game has browser-based Arcade and Puzzle play, a spring-gravity/sticky-contact simulation, normal matching, bombs and cascades, falling bubble effects, and configurable depth-based scoring. It includes the Node test suite and browser smoke test code.

The original physics, grid, collision/aiming, support graph and scoring modules should be reused with regression protection. The original Arcade/Puzzle gameplay is **not** to be silently replaced.

## Planned but not implemented

- Fairy Survival ruleset, menu, pacing and transformation indicators.
- Continuous enchanted descent, finite Enemy magic and survival victory.
- Transformation line and per-colour physical/mental accounting.
- Straight-up bottom-contact destruction and zero-transformation detached falls.
- Constrained Enemy AI choice of X new-row positions.
- Good Fairy earned single-use powers as a Fairy Survival mechanic.
- Mental acceptance stages, interference and stage-6 surrender/game-over.
- Story visuals, character changes, endings and appearance/personality themes.

See [VISION.md](VISION.md) for the overall game and [TRANSFORMATION_GAMEPLAY.md](TRANSFORMATION_GAMEPLAY.md) for event rules and unresolved decisions.

## Next experiments

1. Implement and visualise the bottom-contact vertical blast on live swinging positions.
2. Verify support recomputation and harmless detachment, including still-supported branches.
3. Add simple per-bubble transformation accounting with a configurable line.
4. Test continuous descent and a limited magic budget.
5. Assess actual game fairness before enemy placement and mental sabotage.

## Engineering constraints

Keep features in separate, testable modules; preserve the inherited physics; validate mobile and desktop behaviour. An undocumented idea is **not** automatically an implementation requirement. Re-evaluate the inherited [effects and tools.md](effects%20and%20tools.md) specification for Good Fairy abilities before implementing it.

**Identity note:** This repository is owned by Goldendawn69. The first imported commit may still contain older project text in **Git history**. Rebranding the current files is not the same as rewriting existing history.
