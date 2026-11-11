# Bitblock — notes for Claude

Bitblock is Zak's 3D first-person block-building survival game. It is a single self-contained file, `index.html` (HTML + CSS + one big script), using three.js r128 from cdnjs and the "Press Start 2P" Google font. It is played by kids, so keep the tone friendly and the difficulty gentle.

Zak wants features to feel like Bitblock's own ideas, not straight copies of another game.

## How the code is organized (inside the single `<script>` in index.html)
- **Config & blocks**: `NAMES`, `TILE` (block id -> atlas tiles), `HARD`/`GROUP`/`NEED` (mining). Textures are painted into a byte array (`paint(tile, fn)`) with a 10-bit color palette — no canvas reads (anti-fingerprinting browsers blank them).
- **World storage**: 16x16 chunks, `WY=128` tall, `data` Map keyed by `K(cx,cz)` which includes the dimension (`DIM`: 0 first world, 1 Crystal Realm, 2 Ember Depths). World is 100,000,000 blocks wide; chunk meshes use local coordinates for precision.
- **Generation**: `column(x,z)` (height + biome), `genChunk` (terrain, smoothed 4x4 biomes, caves, ravines, springs, flood-fill island cleanup, flora, mines, structures, towns), `genRealm`, `genDepths`.
- **Structures**: `cellStruct` (80-block cells: pyramids w/ 3 levels, temples, ancient city ruins, shipwrecks, ruined portals; big neighbors can't overlap), towns (`townAt`, houses, wizard towers, libraries, desert variant), realm structures (spires, sky temples, observatories), mineshafts (`mineAt`).
- **Meshing**: `buildChunk` with typed buffers; separate passes for water, glass/portals (cutout), glowing magma, fire.
- **Gameplay**: player physics `step`, inventory/crafting (`RECIPES`), mining, mobs (zombies, mummies, ember zombies, villagers, wizards, crystal people), traps, fire, flowing water (`tickWater`; flows UP in the realm), magic (8 spells, 3 tiers via combining), cameras/album, portals linked in pairs, 2D side-view map (N).
- **Sound**: all procedural Web Audio (`sfx`), no files.

## How changes have been tested
Headless Chromium (Playwright, `--use-gl=swiftshader`) with three.js served locally, injecting test hooks into a copy of the page. Useful checks that caught real bugs: random-play fuzzing for errors, flood-fill scan for floating blocks, counting tiny biome patches, structure-frequency sampling in Node, and timing `genChunk`/`buildChunk`.

## Publishing
The repo is github.com/ZakCraft/bitblock. With GitHub Pages on (main branch, root), the game is playable at https://zakcraft.github.io/bitblock/.
