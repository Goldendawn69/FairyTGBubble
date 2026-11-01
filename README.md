# FairyTGBubble 🫧🧚

**FairyTGBubble** is an experimental transformation-themed bubble-shooter survival game by **Goldendawn69**.

**Repository:** https://github.com/Goldendawn69/FairyTGBubble  
**Platform:** Desktop and mobile web browsers  
**Technology:** HTML, CSS, JavaScript ES modules and Canvas  
**Current state:** Playable inherited Arcade/Puzzle engine at version 0.10.0. **Fairy Survival has not yet been built.**

## The premise

An adult traveller enters an enchanted forest and is captured by an Evil Fairy. Her magical bubble machine slowly changes the captive's appearance, body and — less frequently, but from early on — thoughts. As acceptance grows, the character begins subconsciously helping the fairy instead of resisting.

A Good Fairy offers support through powers earned by successful bubble clearing. The Evil Fairy has a **finite supply of magical bubbles**: outlast her magic and escape with whatever changes have already occurred. Reach **stage 6, full mental acceptance**, and the protagonist stops resisting: game over.

These are **game-design decisions and proposals**, not current game features.

## Currently playable

The independent starting copy includes:

- Arcade mode and three handcrafted Puzzle levels.
- Matching three or more connected bubbles of the same colour.
- Physical gravity, sagging and swinging formations, sticky contacts, real collision positions and contact-triggered matching.
- Bomb Bubble explosions, chain reactions, unsupported falling bubbles and visual effects.
- Configurable, depth-sensitive scoring in `src/scoring-config.js` and `src/scoring.js`.
- Desktop/mobile layouts, keyboard/mouse/touch controls and automated Node/browser tests.

**The original Arcade and Puzzle modes are preserved as the technical baseline.** Nothing in this repository's new story documents should be mistaken for implemented Fairy Survival behaviour.

## Design documents

- [VISION.md](VISION.md) — premise, agreed design direction, success/failure and priorities.
- [TRANSFORMATION_GAMEPLAY.md](TRANSFORMATION_GAMEPLAY.md) — proposed Fairy Survival mechanics, transformation accounting and open decisions.
- [PROJECT_STATUS.md](PROJECT_STATUS.md) — implemented versus planned.
- [GRAVITY_DESIGN.md](GRAVITY_DESIGN.md) — inherited spring physics and collision requirements.
- [effects and tools.md](effects%20and%20tools.md) — inherited specifications for board specials and rechargeable tools; re-evaluate for the Good Fairy.
- [REFACTORING.md](REFACTORING.md) — inherited architecture and regression practices.

## Run the existing prototype

From the repository root:

```sh
python3 -m http.server 8000
```

Visit `http://localhost:8000/`.

```sh
npm test
```

runs the Node tests. Separate browser smoke checks exist under `tests/` and GitHub Actions.

## Development priorities

1. Prove bottom-contact straight-up destruction and harmless detachment.
2. Introduce a configurable transformation line and per-colour counters (without character artwork).
3. Prototype continuously advancing formations and the Evil Fairy's finite magic.
4. Test enemy placement budgets, assistance and increasingly frequent mental interference.
5. Add characters, narrative scenes and eventual specialised appearance/personality transformations.

Keep physics, rendering and gameplay rules modular. **Do not change or overwrite the inherited Arcade/Puzzle modes just to add Fairy Survival.** Test each small change before moving on.

No advertising, paid currencies or real-money purchases are planned.
