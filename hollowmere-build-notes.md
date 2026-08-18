# Hollowmere — build notes (v18)

Top-down grimdark RPG prototype. Single self-contained HTML file, canvas 2D, no
dependencies, no external assets, no build step. Open `hollowmere.html` in a browser.

**Companion file:** `hollowmere.html` — keep both in project knowledge together.

**Setting.** Nestor is a hired sword of the **Order of the Waning Light**, an
order that hunts and contains the supernatural. The village of Hollowmere is one
job. v18 is where that stops being flavour: Brother Aldric is the first other
member of the order the player meets, and he is dead.

## Non-negotiables

1. Everything drawn procedurally; every sound synthesized (Web Audio). One portable file.
2. Internal resolution **576x360**, `image-rendering: pixelated`. Never raise it.
   (v13 moved it up from 384x240 at a 1.5x art scale; this line said 384x240 until
   v18 and was wrong for five versions.)
3. Darkness is the core mechanic. New features must respect the dark.
4. UI drawn in-canvas. Small pixel text uses hand-plotted 3x5 glyphs (drawDigit),
   never fillText below ~8px. Menu panels may use fillText at 8-11px.
5. Elements drawn BEFORE the lighting composite obey darkness; AFTER are self-luminous.
   That placement is a per-element design decision. **Every new element must say
   which side it is on and why** — see the v18 table below.
6. **PC only.** Mouse + keyboard. Touch was removed in v13; do not add it back.

## Controls

WASD/arrows move · RIGHT-click swings sword · LEFT-click interacts (talk/pickup)
or activates hotbar slots · E also interacts · I inventory · J journal ·
**K skills** · T hints · Esc closes · 1-9 activate hotbar slots · M mutes.

## Player: Nestor

150/150 HP (red orb, left). Soulpoints = lamp charge 100/100 (green orb, right).
LV 1 start; ghost kill = 5 XP; LV2 at 50 XP, each next level needs 25% more
(50 → 63 → 79 …, Math.ceil). Death: screen fades, "THE DARK TAKES YOU",
respawn at plaza with ghosts pacified.

## Equipment (fixed, on the character sheet)

- Weapon: Short sword — always in hand (blade drawn at his side at rest)
- Head: Dark linen hood (+1 armor) · Torso: Black tunic (+2) ·
  Legs: Leather trousers (+1) · Boots: Leather boots (+1) → armor total 5
- Armor subtracts flat from ghost damage: 12-20 becomes 7-15 (min 1).
- Lower-right slot: Leather backpack → click to open the pack panel.

## Backpack / hotbar model (important)

BACKPACK is a 12-slot array; the bottom hotbar IS slots 0-8 of that same array.
Moving items in the pack rearranges the hotbar automatically. Click an item to
pick it onto the cursor, click a slot to place/swap; the X (or closing) drops a
held item into the first free slot. Slots 10-12 are pack-only (dimmer border).
Initial: lamp, torch, soul powder. Number keys and hotbar clicks call
slotAction(i): lamp/torch toggle the held light (lightKind, by kind not slot);
soul consumes.

## Sword

Simple 2px-wide rectangular blade, guard 3px dull brass; hand (dark fist) at
radius ~7-8; NOTHING metallic inside radius 9 (everything inward is arm/fist).
Rest: hangs at his side, tilt ±24 degrees away from body (REST_TILT=0.42 —
sign matters, flipped once already; pivot side must match tilt direction).
Swing phases: wind 0.13s (ease-out, pull back past start), strike 0.10s
(ease-in, 164 deg), recover 0.17s. Arm extension animates 0→1 through the
swing (tucked at rest, extended mid-strike). Cleave: per-pixel tapered
crescent drawn INSIDE the depth sort (occludable), carrying its own light
holes + cold blue additive. Geometry verified: 0 torso/hood intersections
across all facings and phases (leg overlap = correct occlusion).

## Lights

Torch r78 (warm, fast flicker) / Dark iron lamp r62 (green, slow pulse) share
one hand; sword has the other permanently. Lamp drains 2/s lit, regenerates 1/s
stowed, auto-stows at 0 and can't relight until >0. 14 braziers ring the
village (r34 warm pools, placed programmatically clear of all obstacles).
Ghost drops glow on their own.

## Ghosts (8)

Visible ONLY under lamp light (fade in over last ~22px of r~55). Exposure
accumulates while lit (weighted by brightness); at 3.6s cumulative → hostile:
shriek + positional growl, tint shifts red, chases at ~3x drift speed, passes
through walls. Contact hit 12-20 raw on 1.5-2s cooldown (positional growl each
attack). Hostiles bleed through as faint red shimmer within 30px regardless of
light; red screen-edge pulse when one is near. 3 sword hits kill (hit flash,
knockback, camera kick); death = positional whimper + soul powder drop.
Player death resets hostility.

## Soul powder

E or left-click to take (two-note chime). Stacks with count in its slot.
Activate slot to consume: +40 HP, green flash; refused at full HP (red flash,
not wasted). It is the economy loop: lamp reveals → provoked ghosts cost HP →
kills yield powder → powder restores HP.

## Inventory screen (I)

Centered parchment panel: name, HP bar + SP bar, LV + XP bar, 4x-scaled
portrait with subtle idle (1px breath bob, pulsing red eyes, blade at side).
Equipment slots flank: left weapon/torso/legs, right head/boots/backpack.
Hover shows item names + armor. Bottom: Armor total, Souls count. World
pauses while open (movement, attacks, lamp drain, hostile chase all held).

## Audio (all synthesized)

Rain loop (smoothed noise, HP480/LP1750, 2 slow LFOs, M mutes) · whispers
(formant bandpass 780/2000Hz, HRTF panner circling) · swing / hit / hurt /
shriek / pickup / heal one-shots · ghost growl (86%-correlated noise LP520→300
+ 58Hz saw with 17Hz vibrato) and death whimper (sine 430→112Hz, 11Hz tremolo,
breath layer), both HRTF-positioned at the ghost's true bearing.

## Whisper events

Every 20s of WALKING (timer frozen while idle/menus): shuffled once, each of
the 4 lines plays once, never repeats. Torch contracts ~13% + extra flicker
while active. FIFTH LINE STILL UNSUPPLIED — one array entry when it arrives.

## Village

17 houses (3 ruined, 4 lit), plaza pyre, well, cart, graves, fences, 8 trees,
14 braziers, 7 wandering villagers (dialogue: "..." only, on purpose). Rain:
depth-scattered landing points, splash particles, drawn twice (masked + 16%
over-dark pass).

## Known gaps

- Rain falls through roofs; torch lights through walls (no shadow casting).
- Villagers walk through each other; straight-line pathing gives up behind houses.
- Whispers have no world source. Ghost hostility resets free on player death.
- Touch cannot attack (right-click only) — needs an on-screen button eventually.
- No save/persistence. No editor yet (object-placer plan discussed; ground is
  procedural, so terrain painting would require introducing a real tile array).
- Art ceiling: procedural rects; houses weakest. Tilesets would pay off if this
  goes past prototype.

## Scope warning

Build a vertical slice, not "the game": one area, a few enemy types, one boss,
~15 min loop. If the loop isn't fun small, content won't save it.


## v13 changes (art scale, minimap, owls)

**Resolution / art scale.** Internal canvas is now **576x320-family: 576x360**
(was 384x240) — a 1.5x art scale. Every sprite was redrawn larger with more
detail, NOT just upscaled: character is 25px tall (was 17), houses have plank
rows + timber beams + mullioned windows + chimney smoke, trees have 9 branches,
props are multi-tone. Tile size 24 (was 16). All speeds, light radii, and
sword radii scaled 1.5x. Cost: ~7.5k fillRect/frame, ~1.2ms of JS draw-call
work per frame — comfortable.

**Village bounded.** World is now 1200x1200 (was 1100 at the old scale, i.e.
much larger relative to the character — this is a real shrink). Layout was
generated programmatically and validated: 15 houses, 42px minimum street gap,
plaza kept clear to r205, **100% connectivity with zero unreachable pockets**.
16 braziers on an even 16-sector ring, 16 trees, all clear of houses/props.

**Villager pathing fixed.** `pickTarget()` now rejects targets inside solids
(14 attempts before falling back home). On a blocked step the villager slides
perpendicular, and a `stuck` timer forces a re-pick after 0.7s. Verified: 90s
run, zero villagers inside solids, all still moving.

**Minimap.** Top-right, 112x112, `mmPt()` maps world->panel. Shows houses
(ruins darker), trees (owl trees warmer), pulsing braziers, the pyre,
villagers, soul-powder drops, a viewport rectangle, and the player with a
facing tick. Hostile ghosts blink red — passive ones are NOT shown, so the
lamp stays the only way to find them.

**Owls.** 3 of the 16 trees carry an owl (spread >660px apart). Blink, head
turns, ear tufts. Hoot every 16-42s if the player is within 520px: two-note
sine (392->370, then 330->300Hz) with 5.5Hz vibrato, lowpassed to 900Hz, plus
a bandpassed breath layer — HRTF-panned from the tree's real bearing.

**Combat numbers.** Sword 5 dmg (EQUIP.weapon.dmg), ghost HP 15 -> 3 hits.
Armor 5 still subtracts flat, so ghost hits land 7-15.

**Inventory legibility.** Panel 260x316 with ornate corner studs and a title
bar. Fonts 9-16px (were 7-9). Every label drawn twice with a 1px black offset
for contrast against the parchment. Bars are 96px with highlight + shadow rows
and centred readouts. Equipment slots 36px, pack slots 44px, both with hover
highlight and tooltips showing armor/damage values. Portrait is 3x with a
breath bob and a slow sway.

**PC-only.** Touch handlers were dropped. Right-click swings, left-click
interacts, WASD moves, I/Esc inventory, 1-9 hotbar, M mute.


## v14 — quests, Herbert, journal

**Herbert** (`HERBERT`) is a static NPC at 638,488 — 180px from spawn, placed by
searching for a spot with 26px clearance on all sides. Elderly sprite: hunched,
grey beard, hooded robe, staff with a faint glowing head. Not in `NPCS`; he has
his own draw call and his own branch in the proximity scan.

**Dialogue engine** replaced the old one-line box. `startDialogue(npc, name,
pages, dots)` where each page is `{text, choice?}`. Word-wrapped via
`wrapText()`, typewritten at 44 chars/sec. E skips the typewriter, then E again
advances. Choices render as numbered rows — pick with 1/2 or by clicking.
Villagers still say "..." through the same engine (`dots:true`).

**Quest data** lives in `QUESTS`. One quest: `shades` / "Shades, be gone!" /
giver Herbert / banish 10 wraiths / 100 XP. States:
`available -> active -> ready -> done`.
- available: yellow `!` over Herbert and on the minimap
- accept: quest scroll opens (Esc or X closes)
- ready (10 killed): reddish "VOW FULFILLED" banner, marker becomes `?`
- done: gold "QUEST COMPLETE / 100 XP EARNED" banner, marker clears
- after: "Be gone, leave me to my thoughts"
- declining leaves it available; asking mid-quest reports the remaining count

**Journal (J)** — parchment scroll with rolled ends. Lists taken quests with
title, giver, objective + progress bar, and status: "In progress." /
"Herbert is waiting for your report" / "Vow fulfilled." plus a green check
beside completed titles. Esc or X closes.

**Ghost respawn (required by the quest).** Only 8 ghosts exist, so 10 kills was
impossible. Dead ghosts now return after 15-27s at a validated free spot
240-490px from the plaza and at least 260px from the player, fully reset.

**Banners** — `banner(main, sub, tone)`; 'red' and 'gold' tones, fade in with a
slight rise, horizontal gradient plate with hairlines.

**New sounds** — `quest` (rising triangle triad) and `vow` (low descending
lowpassed sine pair).

**Any open panel pauses the world**: `uiPaused` covers inventory, journal and
the quest scroll — movement, ghosts, villagers, lamp drain, owls, whisper timer.

**Verified end to end**: offer -> decline -> re-offer -> accept -> scroll ->
respawn cycle -> 10 kills -> VOW FULFILLED -> journal shows "waiting for your
report" -> turn in -> 100 XP -> gold banner -> post-quest line. Panel layout
checked for collisions against hotbar, orbs and minimap.


## v15 — levels, quest 2, the rope, the undercroft

**Level rewards** (previously levels counted for nothing). Each level: +12 max
HP, +4 max lamp charge, and +1 sword damage every 2nd level. Gold banner shows
"LEVEL n / +12 VIGOUR (+1 EDGE)". Note this makes the sword scale: at LV3 it
deals 7, so skeletons still die in 3 hits.

**Quest 2 "What Stirs Beneath"** chains directly off quest 1's turn-in — the
"We can rest tonight...For now" page is followed by the undercroft offer with a
Yes/No choice. Declining sets `available_soft`, which keeps a `!` on Herbert so
you can accept later. Objective: clear the undercroft (2 skeletons), 150 XP +
25 gold. Asking Herbert mid-quest points you southwest for the rope.

**Currency**: `gold` and `shackles`, shown on the character sheet alongside
armour and weapon damage.

**Toasts** — `toast(txt)` renders a small framed line above the hotbar.

**The hanged man** hangs from the tree at 298,853 (southwest). The rope is
anchored to a ground stake at +26px. It is cut by a **sword swing** (the arc
test now also checks the anchor), not by E. Cutting drops the body over 0.8s
into a slumped pose; then E on the anchor takes the rope. Rope goes into the
first free backpack slot, so it can be dragged onto the action bar like
anything else.

**The well** (STONES[1]) is the entrance. E without a rope in the backpack →
Nestor says "I cant go down there, I need something to lower me in". With rope
→ descend. You can climb back up at the shaft at any time.

**AREA switching.** `AREA.name` is 'village' or 'undercroft'. `blocked()`,
camera clamp (`areaW/areaH`), entity updates, the layer list, weather, lighting
and the minimap all branch on it. Undercroft is 1500x460 built from three
overlapping rectangles (entry chamber, corridor, end chamber) — overlap matters,
since collision requires the player circle to fit entirely inside one room.
Verified sealed: 3870 sample points, zero walkable cells outside the rooms;
3000 random steps never escaped.

**Undercroft look**: limestone block courses on all four wall faces, wet stone
floor with cracks/puddles/moss speckle, 14 blue wall torches (r34, very dim),
and a moonlight shaft at the well with dust motes and a hanging rope.
Ceiling drips fall and splash with positional ping sounds. No rain, no fog.

**Skeletons** (2): 20 HP, 14-20 damage, 2.2s attack cooldown (slower than the
ghosts' 1.5s), 0.32s claw wind-up with reaching arm bones. Full skeleton sprite
— skull with jaw and glowing sockets, 4-rib cage, spine, pelvis, limb bones.
On death they collapse into a spreading bone pile. Positional bone SFX for
step / attack / hurt / die, all built from bandpassed noise clicks plus a
triangle thud; the death sound is 14 descending clicks.

**Drops** now carry a `kind`: 'soul', 'gold' (1-5 coins, drawn as a stacked
pile) or 'shackle'. 62/38 split from skeletons.

## Still open

- Levels grant stats but no skills/choices.
- The undercroft is one corridor with 2 skeletons — deliberately a stub.
- Quest 2's turn-in line is mine, not yours; replace it if you want.
- No save. No shops, so gold has nothing to buy yet.


## v16 — art pass (references) + tuning

**Balance.** XP curve now grows **85%** per level (was 25%): 50, 93, 173, 321,
594, 1099. Both quests award **50 XP** (quest 2 still adds 25 gold). Quest 2 is
`locked` until quest 1's turn-in and the journal filter now only lists
active/ready/done, so it never appears early.

**Villagers speak.** 5 of the 8 carry a line about the haunting; the other 3
still answer "...". Silence now reads as a choice because it sits beside real
voices.

**Skeletons are swordsmen** (per the reference sheet). Warm bone palette
(#e6dcc0 / #c2b697 / #9c9076 / #6e6552) instead of grey. Full build: skull with
brow, jaw teeth, glowing sockets, 4-rib cage with spine vertebrae, pelvis,
jointed femur/tibia legs, a rusted shoulder pauldron, and an iron sword held in
the near hand. Swing is a 3-phase arc — 0.42 of the window is wind-back, then a
2.55rad sweep — with a dull grey cleave arc drawn behind the blade (deliberately
duller than Nestor's white one). Death crumbles into a spreading bone pile with
the sword falling clear.

**Wraiths rebuilt as hooded figures** (per the reference). Pale mauve robe with
lit left edge and shadowed right, a deep dark hood cavity, hanging sleeve arms,
and a tattered hem of 5 independently wavering strands that fade out downward.
Glowing cyan-green eyes with a bright inner pixel and a shadow line above; they
blink. Hostile shifts the whole robe toward red and the eyes to ember.

**Trees have canopies** (per the reference). 9 of 16 are leafy: splayed root
flare, textured trunk, then 9 overlapping canopy blobs drawn dark-first then
inset with mid-tone and a light patch on the upper-left only, plus rim
highlights and drifting loose leaves. Palette stays grimdark
(#101c0e -> #315226). 4 are dead/bare, and the 3 owl trees stay bare so the owls
read.

**Torches got sconces.** Surface braziers: iron post, ribbed bowl with 5 bars,
5-layer flame from deep red through to a white core, plus rising ember specks.
Dungeon torches: wall bracket with mounting bolts, 5-layer blue flame
(#123c56 -> #e8fbff), rising blue sparks.

**Undercroft dressed** with 46 hand-validated pieces: crates and stacked crates,
banded barrels, corner spider webs (radial spokes + arc rings), hanging chains
that sway, bone scatters, skulls, and rubble. 16 of them are solid obstacles;
the rest are flat dressing. Verified: none outside the rooms, and the corridor
is still traversable from the entry chamber to x=1351.

**Wall shadows.** Each room now gets an ambient-occlusion band — 14px gradient
down from the top wall, 9px on the sides and bottom — which is what makes the
limestone read as walls rather than a flat border.


## v17 — reference-strict art, the full undercroft, Herbert's house

**Ghost rebuilt from the sheet-ghost reference.** Pale grey body (214/178/140/104
ramp) with a hard dark outline, domed top, big black oval eyes with a shadow
notch, small oval mouth, and 4 independently wavering bottom tendrils that taper
and outline as they fall. Hostile shifts red and lights the eye centres.

**Skeleton strict to the reference.** COOL grey bone (#e4e4e6 / #c6c6ca /
#9d9da2 / #6f6f76), brown leather cuirass over the left ribs (#7a4f28 / #5c3a1e
/ #402715), gold collar clasp at the neck, exposed dark-gapped ribs on the right,
and a **curved falchion** — the blade curves via a `pow(t,1.7)*3.4` perpendicular
offset, with a maroon wrapped grip. Death drops the leather and the sword clear
of the bone pile.

**Well rebuilt from reference**: 3 courses of offset stone blocks, a capped rim,
dark green water with a moving glint, two timber posts, and a 4-tier blue
shingled roof with a ridge cap. Rope and bucket hang from the crossbeam.

**Hanged man is a skeleton** and reads as hanged: rope from the branch to a noose
collar, skull tilted, jaw and teeth, leather cuirass, dangling rib/arm/leg bones,
feet clear of the ground, whole body swaying. It **only exists after quest 1** is
turned in (`hangedActive()`), and the rope can't be cut before then. After
cutting it collapses into a slumped heap on its side.

**Zombie** (new): hunched shambler in greens (#54762c / #7a9c42 / #3a5220), spiky
hair, tattered wrap, long arms hanging past the knees, torso tilt that sways. 20
HP, **35 damage**, 3.1s cooldown with a slow **0.95s** windup (vs the skeleton's
0.32s) — you can see it coming and step out. Positional growl while patrolling
and on wind-up, and a 1.9s haunting moan on death (sine + saw + breath, 4.6Hz
vibrato, falling 196→74Hz). Drops 2-6 gold, 1-2 old rags, 1-2 rotten teeth.

**Undercroft expanded to 5 rooms** (1850x470): entry, corridor, mid chamber,
second corridor, treasure chamber. 98.9% connectivity verified. **5 skeletons +
2 zombies**, and quest 2 now needs all **7**.

**The chest** in the end chamber gives 100 gold, the **Long sword (12 dmg,
replaces the Short sword)**, and the **Necromancer Sigil** — a black stone with a
pulsing red rune. Opening it banners "HERBERT MIGHT BE INTERESTED ON THIS".

**Herbert's house.** Delivering the sigil gets "Come with me" and moves you to a
third area: timber walls, plank floor, 5 book-laden shelves, a lit stone
fireplace (the only light source), a table with a candle, and **2 cats** — one
curled and sleeping, one sitting with a flicking tail; both blink. Banner reads
"GAME STILL IN PROGRESS / THANK YOU FOR PLAYING". The door leads back out.

**Quest markers**: yellow `!` over the well (and on the minimap) once quest 2 is
active and you have not yet descended; Herbert shows `?` whenever you carry the
sigil.

**Hints scroll (T)** opens on first load: all controls plus what the torch, lamp
and soul powder actually do. Esc or X closes it.

**Fix**: villagers could wander onto Herbert and steal his interaction. Herbert
now wins proximity ties within 14px, and villager wander targets avoid a 58px
radius around him. The sigil is consumed on delivery.


## v18 — the shop, the sewers, skills, and an inventory that stops eating things

### Inventory: pickups no longer replace what you are holding

The old behaviour swapped loot straight into the hand and displaced whatever was
there. Now **every pickup goes to the first free backpack slot** (`addItem`,
`packFree`), and equipping is an explicit act.

- Pack full → the pickup is refused, the item **stays on the ground**, and a
  `PACK IS FULL` toast fires (`refusePickup`). `takeDrop` returns false and does
  not splice the drop.
- Equipping from slot *n* **swaps**: the outgoing sword or armour lands back in
  slot *n*, never the void (`slotAction`). Torch ↔ lamp keeps the older model —
  both live in the pack and `lightKind` selects which is lit, by kind not slot.
- Non-equippables (tear, key, rope, sigil) set `PL.denied` and play the new soft
  `denied` blip. **The letter is the exception**: activating it reads it, because
  §4 wants it readable from the inventory and an unreadable letter is a worse bug
  than a missing blip.
- **`edgeBonus` is new.** Level-up damage used to mutate `EQUIP.weapon.dmg`, so
  swapping swords silently threw away every point of accumulated edge. Levelling
  now increments a player-side `edgeBonus` and `weaponDmg()` sums the two.
- The chest's Long sword is **loot**, not an automatic replacement. It and the
  sigil go to the pack, or to the floor in front of the chest if there is no room.

### Armour — a new body slot

`EQUIP.armor` (one worn at a time), shown as a fourth row on the left of the
character sheet and as a small shield glyph beside the HP bar. Row pitch dropped
54 → 46 to fit it without hitting the portrait or the footer. Flat reduction with
a **floor of 1** — armour can never make you immune. Base kit is still 5; Rusted
Mail takes it to 7, Warden's Plate to 10. A 20-damage wraith hit lands for 13
through mail; a 3-damage hit still lands for 1 through plate.

### Crystalized Tear

Wraiths now drop **two** things: Soul Powder on the ground as before, and a
Crystalized Tear **straight to the pack** (`grantTear` → `tearToPack`). Stackable
into one slot with a 3x5 glyph count. Pure vendor trash — no use, no consume, no
equip — sells to Ondrick for 5g. If the pack is full when a Tear would be granted
it falls to the ground at the wraith's death position instead. Note the stacking
rule: if a Tear slot already exists the pack is never "full" for Tears.

### Ondrick's shop

**House 8** `[736,318,153,105]` became the shop — its door stands **184px from
Herbert**, the closest of the fifteen, so the sign is in view within the first
minute. No sixteenth house was added.

- **Exterior tell:** a hanging lantern sign over the door. This is now the visual
  grammar for "this building is enterable" — reuse it for any future interior.
- **Minimap:** the shop house is tinted warmer than its neighbours and carries a
  hand-plotted purse-with-a-`$` mark floating above it, drawn above the props and
  below the player dot.
- **Interior** (`shop` area, 400x280): plank floor, shelves of goods, a hanging
  lantern that is the only light source, and a counter that **spans the full room
  width on purpose** — the 4px gaps at either end are narrower than the player's
  12px circle, so there is no walking round behind the shopkeeper. The band behind
  the counter is a deliberately sealed staff area, not an orphaned pocket.
- **Ondrick**, hunched, aproned, arms on the counter. First conversation is two
  pages of dialogue and then the panel; after that E opens the panel directly.
- **Panel**: same frame, glyphs and palette as the inventory. BUY left, SELL
  right, gold always in the header. Esc or the X closes it.

| Buy | Price | Effect |
|---|---|---|
| Rusted Mail | 30g | +2 armour |
| Warden's Plate | 90g | +5 armour |
| Sewer Key | 25g | unlocks the grate, one-time stock |

| Sell | Price |
|---|---|
| Crystalized Tear | 5g |
| Iron Shackles | 8g |

**Soul Powder is deliberately not sellable.** It is the heal that pays for the
lamp/wraith risk loop; letting it be cashed out would defuse that loop.

The Sewer Key leaves the shelf permanently once bought (`SEWER_KEY.bought`) and
is **not consumed on use** — the grate stays open so the level can be re-run.

### The Drowned Run

A second level below the undercroft, reached through an iron grate in the
undercroft's **mid-chamber lower-right** (1010,375) — 50px off the corridor band,
so it is only found by walking the room's edges. Locked without the key
(`THE GRATE IS LOCKED`); with the key it opens permanently on a heavy metal-scrape
cue (filtered noise sweep 340→1150→430Hz over a 78→41Hz iron thud).

1500x400, **nine overlapping 64px channels** in a zig-zag instead of the
undercroft's rooms — you never see more than one turn ahead. Wet brick: a
specular row on the lower edge of every floor tile plus random highlight dashes,
and standing water down the centre-line of each channel with drifting glints.

- **7 skeletons at 30 HP** (undercroft skeletons are 20). Difficulty came from HP,
  not damage, exactly as specified — the player may arrive armoured, and raising
  damage would have punished the unarmoured run instead.
- **Three torches in the whole level**, dimmer and smaller than the undercroft's
  (r26 @ 0.30 vs r34 @ 0.42): two at the mouth, one guttering beside Aldric so the
  body reads when you reach it. This is where the dark bites hardest and where the
  25g has to feel spent.
- **Ambience is walk-time gated**, following the whisper-timer pattern: `sewerWalk`
  only advances while `P.moving`, so standing still never advances drips or chains.
  Chain rattle is 5-9 bandpassed noise clicks over a falling triangle, HRTF-panned
  from a real chain in the decor.

**Brother Aldric** slumps against the wall at the far end, in the Order's surcoat
with a waning-crescent mark. Interacting takes **Aldric's Letter** to the pack and
reads it. The letter is re-readable from the pack (activate the slot) and mirrored
into the journal as a standing block, because the reframing is the point of the
whole level: **the wraiths you have been banishing all game took the coin. They
are victims, not monsters.**

### Herbert and the road to Yotan

Gated strictly on carrying the letter — nothing about Yotan exists in the game
before Aldric is found. Herbert opens on a held beat (`...`), carries a 1px idle
tremor for the length of the conversation, already knows about the tokens from
ten years ago, refuses to say what he took, and sends you north. **100 XP**, no
mechanical unlock. Fires once and cannot be re-awarded.

**The north road is physically closed** by a collapsed gatehouse at (535,112) —
verified clear of every house, tree and brazier, and the village still floods to
100% connectivity. Interacting gives `THE NORTH ROAD IS NOT YET WALKED`, so the
hook reads as deferred rather than broken.

### Soul Embers and skills (K)

**Naming call:** the currency is **Soul Embers**, not Soul Points. "Soul Points"
and "Soul Powder" sat three pixels apart in the same panel and were unreadable as
distinct things. Powder heals; Embers buy skills. One name shipped, not both.

Wraith 3, skeleton 5. **Zombies are not in the spec's table — they were priced at
5 with skeletons**, being the same tier of undercroft threat; flag if that is wrong.

`SKILLS` is indexed by rank with a `costs[]` array, so rank 2/3 is a data edit and
not a refactor. Only rank 1 ships.

| Skill | 15 embers | Effect |
|---|---|---|
| Thy Flame Shines Brighter | rank 1 | +25% light radius: torch 117→**146**, lamp 93→**116** |
| Perseverance | rank 1 | lamp regeneration 1/s → **3/s** |
| Soul Steal | rank 1 | 5 HP/s for 3s after a kill, 15 total, refreshes not stacks |

**Open decision from the spec, resolved:** "Perseverance increases soul point
regeneration by 2" was ambiguous. Implemented as **(a) lamp fuel regen 1→3/s**.
The lamp is the only meter in the game that regenerates, it directly buffs the
core wraith-hunting loop, and reading (b) — passive SP regen — would have made
every skill eventually free and killed the currency as a choice.

Thy Flame scales at the **lighting-composite step** (`lightScale()` applied once
to `BASE`), so every carve, tint and reveal downstream agrees. The wraith reveal
radius rides the same scale — a bigger lamp that revealed no more wraiths would be
a lie about what the skill does. Soul Steal shows as a warm pulse and rim ticks
around the HP orb.

### Lighting layer decisions for every new element

| Element | Side | Why |
|---|---|---|
| Shop interior, shelves, counter, Ondrick | **before** | Lit by the hanging lantern; the room obeys the dark like Herbert's house |
| Shop lantern **sign** | **after** | It is the "you can enter here" signal and must carry across a pitch-black village |
| Crystalized Tear on the ground | **after** (plus a small pre-composite carve) | Deliberate exception to the darkness rule — a crystallised soul should be findable in the dark. The carve lights the floor under it; the post-composite pass is only the self-luminous core and motes |
| Sewer floor, water, brick, decor | **before** | The level is about not being able to see |
| Sewer torches | **before** | Real light sources, dimmer than the undercroft's |
| Brother Aldric | **before** | Found by lamplight or by the one torch beside him — that is the beat |
| The grate | **before** | It is a thing in a room |
| North gatehouse rubble | **before** | Terrain |
| Skill panel, shop panel, journal letter block | **after** (HUD) | Not in the world |
| Soul Steal orb pulse, purse minimap mark | **after** (HUD) | Not in the world |

### Fixes that came out of verification

- **`drawScrollFrameJ` drew its close button at `journalX()` for every caller.**
  The hints panel drew its X at the journal's coordinates while `hintsClick`
  tested somewhere else — the drawn button and the live button disagreed. It now
  takes the rect as a parameter.
- **`pickTarget` clamped wander targets to `WORLD` (1200) in every area**, quietly
  pinning anything east of x=1150 in the 1850-wide undercroft. Clamps per area now.
- **Sewer kills could tick the undercroft vow** — that quest can still be active
  when you go down. `countUndercroftKill` is area-guarded.
- **The lamp regenerated while a panel was open.** The old shape was
  `if(lamp && !uiPaused) drain; else if(...) regen;` so an open panel skipped the
  drain and fell straight into the regen branch — you could top the lamp up by
  sitting in the inventory, and Perseverance would have tripled that rate. Panels
  now freeze the meter in both directions.
- **`I` did not close the skill panel**, leaving two panels stacked.
- **The shop doorway strip was orphaned** from the room by a 3px gap between the
  two walkable tests; the door band now overlaps the room band by 12px.
- **The shop door anchor sat inside the house's collision box.** Houses carry a
  solid over their lower 58%, so an anchor at the painted door is unreachable. It
  is now the standing spot in front of the house.
- **Hints panel** (reported): poor contrast and text leaking past the frame. Ink
  darkened (`#5e1408` keys, `#171004` body against the `#4e4025` page), frame grown
  to 436x340, rows tightened, and the item list is now **measured against the
  footer** so it can never overrun. Journal grown to 380x322 with 64px rows for the
  same reason — a third quest plus the letter block overran the old frame.

### Verification performed for v18

- **No Node on this machine** (`node`, `bun`, `deno` all absent), so the headless
  stub run was done in a real browser instead — real canvas, real Web Audio, which
  is a stricter check than a stub. Frames driven manually by calling `loop()`.
- **3,250 frames**, 650 per area across village / undercroft / sewer / shop /
  house, with movement and attacks every 29 frames and light toggles: **0 errors**.
- **Identifier diff v17 (`520f46c`) → v18: 0 lost**, 247 added, all accounted for.
- **Flood fill**: sewer 100% (8,007 cells, 0 orphans), undercroft 100%, village
  100%, shop 100% after the doorway fix. Every sewer skeleton and Aldric reachable
  from the entry shaft; all 23 sewer decor pieces inside rooms.
- **Panel overflow**: all five panels measured by instrumenting `fillRect` and
  `fillText` — **0 draws outside the frame**, everything inside 576x360.
- **~110 acceptance assertions** across the spec's checklist, all passing.

### Sword geometry — read this before touching it

`GUARD_BASE`, `TIP_BASE`, `SIDE`, `REST_TILT`, the pivot and the arm length were
**not touched in v18**. The sweep was re-run anyway, modelled faithfully on
`drawArmBlade` (guard = 5 1x1 pixels at integer k ∈ [-2,2]; blade = 1x1 pixels at
integer radii from `ceil(gr)+1`, offsets 0/+1/+2 one-sided) against the real
`drawFigure` body rects (side torso `x-4,by-18,9x12`, front `x-6,by-18,12x12`;
side hood `x-5,by-27,11x10`, front `x-6,by-27,12x10`).

**Result: v17 and v18 are identical — 11 hits out of 37,384 sampled positions, the
same single pixel (282,153), guard only, facing 1, at the deepest part of the
wind-back.** Geometry is bit-for-bit unchanged, so v18 introduced no regression.

But that is **not zero**, and the v17 notes claim zero. The pre-existing graze is
one guard pixel clipping the corner of the hood block on the away-facing sprite,
and at 60fps it can land on roughly frame 4 of the wind. It is a real 1px artefact
that predates v18. Fixing it means moving `REST_TILT` or the pivot, which requires
the full re-verify — deliberately not done here without a call on it.

## v18.1 — NPCs no longer get stuck

**Reported:** villagers freezing against terrain and objects.

**Cause.** `updateNPC` decided "did I move?" by whether an assignment happened,
not by whether the entity actually went anywhere:

```js
if(!blocked(nx, n.y, 7)){ n.x = nx; moved = true; }   // ux === 0 -> nx === n.x
```

For axis-aligned travel the cross-axis candidate **equals the current
coordinate**, which is trivially walkable, so `moved` was set true while the
villager stood perfectly still. The stall handling lived entirely inside
`if(!moved)`, so it never ran. Measured over 120 simulated seconds, four of eight
villagers were affected; the worst stood still for **109 of 120 seconds** and
covered 187 units while a healthy neighbour covered 2,146. Every stuck villager
had *the same x as its target* — the signature of the bug.

`updateSkeleton` and `updateZombie` carried the identical flaw.

**Fix**, four parts:

- **`moveEntity(o, ux, uy, step, r)`** — steps and returns the distance *actually*
  covered. A stall is now `gained < step*0.35`, which no amount of axis-alignment
  can fake. It also tries sliding **both** ways along a wall; the old code only
  tried one perpendicular and gave up if that side was also solid.
- **`trackStall`** — two independent watchdogs. A short one for "not moving"
  (0.6s), and a long one for **"moving but never getting closer"** (3s of no
  improvement in distance-to-target). The second catches the sliding-forever case
  the old timer structurally could not see, because sliding counts as movement.
- **`losClear` + a rebuilt `pickTarget`** — targets are now preferred only if the
  straight line to them is walkable, sampled every ~10px. Not pathfinding, just
  enough to stop a wanderer choosing a spot behind a house. The last-resort
  fallback was `home`, which can itself be unreachable from where the entity is
  standing and reproduced the exact grind being fixed; it now aims at a
  reachable point near the entity's current position.
- **`unstick`** — failsafe that spirals outward to find air if anything ever ends
  up inside geometry.

Chase paths for skeletons and zombies now route through `moveEntity` too, so
pursuers slide round corners instead of grinding into them.

**Result**, same measurement window:

| | before | after |
|---|---|---|
| Slowest villager | 1.6 units/s | 14.1 units/s |
| Longest frozen run | 109 s | 7 s |
| Spread fastest:slowest | 11x | 1.3x |
| Inside solids | 0 | 0 |

The residual 5-7s stationary runs are the **intended** idle pause
(`wait = 1 + random*4`), not stalls. Verified over 180s for villagers and 150s
for undercroft skeletons, undercroft zombies and sewer skeletons: zero stalls
past 10s, zero inside solids, path lengths within 1.3x of each other in every
group. Identifier diff against v18: **1 lost, `let moved`** — the buggy flag
itself, intentionally removed.

## v18.2 — the sewer gate, and a class of spawn bug

**Reported:** getting stuck when trying to leave the sewer, plus a request to put
the entrance on the wall as an iron gate with a key slot.

**The freeze.** `ascendSewer()` placed the player at `SEWER_GRATE.y + 26` = y401.
The undercroft mid-chamber spans y140-400 and `blocked()` requires `y+4 <= y+h`,
so the highest walkable y is **396**. The player materialised five pixels inside
the wall with zero free directions and could not move in any direction. The old
grate sat on the floor at y375, so "26px below it" landed in the masonry.

**Audited every other transition for the same class of bug** and found a second,
pre-existing one: `leaveHouse()` spawned at (600,300), which is squarely inside
**house 6's** collision box — also zero free directions. That one has been in the
file since v17 and would have frozen the player on the way out of Herbert's house,
which is currently the last beat of the game.

Both now land on a named, pre-validated standing spot (`GRATE_STAND`,
`SEWER_STAND`, and a checked spot beside Herbert), with `unstick()` as a guard.
All nine transitions are asserted to leave the player walkable with at least two
open directions.

**The gate.** `drawIronGate()` replaces the floor grate and the rope shaft with a
single shared piece of art used on both sides of the passage: a dressed stone
surround set into the wall, five vertical iron bars with two riveted cross-braces,
and a **lock plate with a keyhole** — round bore over a tapered slot, with a dull
brass glint so it is findable in lamplight. `flip` mirrors it for the sewer side,
where the gate is in the north wall and the player stands below it. Open state
swings the bars back against the jamb, leaving hinge pins in the stone and a dark
passage with a depth cue.

- Undercroft side: `SEWER_GRATE` now at (1010, **400**) — the south wall line of
  the mid-chamber, not the floor. `GRATE_STAND` (1010, 386) is 14px in front.
- Sewer side: `SEWER_SHAFT` at (150, **40**), the north wall of the entry sump.
  `SEWER_STAND` (150, 62). The rope-and-shaft art is gone — you walk through a
  gate and come out of a gate.
- The gate reads 54x38px; 49 draws shut, 31 open.
- **LIGHTING LAYER: before the composite.** It is masonry and iron; it obeys the
  dark like every other object. Only the keyhole's brass glint animates, and it is
  still lit rather than self-luminous — you have to bring light to find the lock.

**Verified:** three full round trips in both directions, never stuck, able to walk
away from the gate on every arrival; the key still opens it permanently and is
never consumed; sewer flood fill still **100%** (8,007 cells) seeded from the new
standing spot with Aldric and all seven skeletons reachable; 3,250 frames across
five areas with zero errors; identifier diff **0 lost**.

## v18.3 — scattered notes and a journal that has two tabs

Four lore scraps hidden in the village, two against houses and two in tree roots.
Pick one up and it is yours permanently, readable any time from the journal.

| # | Where | Anchor |
|---|---|---|
| 1 | (254,700) | 33px from house 3 |
| 2 | (176,632) | 30px from tree 4 |
| 3 | (938,766) | 24px from house 11 |
| 4 | (402,462) | 27px from tree 8 |

Placements validated, not eyeballed: none sits on a solid, all four are walkable
and reachable from spawn by flood fill, spread 283-425px out so none is in the
plaza and none is in a corner.

**The journal now has tabs**: `VOWS` and `NOTES n/4`. `JOURNAL.tab` selects,
`JOURNAL.note` is the open scrap. Unfound notes show as `— not yet found —`
slots, so the player knows how many are still out there without being told where.
Picking one up flips the journal to the Notes tab and pre-selects it, and the
toast says so. Selecting a note renders its full text wrapped inline — reading
never leaves the journal, so you can browse them freely.

Quest rows tightened 64 -> 62 and the list start moved down to make room for the
tab bar. Measured across six panel states (three quests + letter, each note open,
and none found): **0 draws outside the frame**, and no note is clipped — the
longest has 92px of vertical headroom, so notes about 3x longer would still fit.

**LIGHTING LAYER: BEFORE the composite.** A scrap of paper in the grass obeys the
dark like everything else — you find these by carrying light over them, which is
the point of the mechanic. They do get a small pre-composite carve (r13 @ 0.30,
about half a drop's reach) so a torch passing nearby lifts them out of the ground
rather than swallowing them. The Crystalized Tear remains the only element in the
game that is deliberately self-luminous.

Notes win proximity ties against villagers, Herbert and doors, so standing on one
in a crowd still picks it up.

## v18.4 — the gate bug, the key changes hands, inventory and skill rework

**The gate "disappeared".** It was set into the **south** wall, and
`drawIronGate` paints its frame and passage *away* from the wall line — so on a
south wall it painted down over the walkable floor, on top of the player, and
read as the gate vanishing into the room. Moved to the **north wall** of the
mid-chamber (1010,140), which is where `drawUnderGround` actually lays limestone
courses. Now it paints into masonry and the player, standing below it with a
larger y, sorts in front of it. Both gates use `flip:false` for the same reason.

The open state was also rewritten: it used to be a near-black rectangle, which
reads as absence rather than as a doorway. It now has lit inner jambs, a
threshold stone, a brighter depth gradient and a slow cool draught. A small r20
carve makes the ironwork legible once you are on it without giving the door away
from across the chamber.

**The Sewer Key is Herbert's now.** Delivering the sigil gets four lines about
his brother and the key in your hand — no more "come with me" and no more house
transition. Ondrick sells armour only. `SEWER_KEY.given` guards the handover, and
a full pack defers it rather than eating the key.
*The house interior (plank floor, fireplace, two cats) is still in the file but
nothing routes to it any more.*

**Inventory.** Character sheet left, backpack immediately right, both always
visible — 260 + 10 + 240, centred with a 33px margin. Previously the pack
overlaid the sheet, so you could never see what you were equipping and what you
were equipping it from at the same time. Three ways to equip now:

- hover a slot and press **E** (`equipHovered`)
- **drag** onto an equipment slot (`dropOnEquip`) — swaps onto the cursor, and
  refuses the wrong item type
- **1-9** from the belt, as before

Equippable slots highlight gold on hover. The weapon can be unequipped too, which
leaves you on `Bare hands` at 1 damage.

**Skills repriced.** Base 15 -> 30, and each rank owned makes the next 20%
dearer, compounding (`SKILL_INFLATION`). The three rank-1 skills now cost
**30 / 36 / 44**, so the full set is 110 embers against 45 before. You commit to
an order of purchase instead of sweeping all three.

**Encoding damage, self-inflicted.** A PowerShell regex rewrite of the file
double-encoded 11 middots and 6 section signs and added a BOM. Repaired to UTF-8
without BOM, 0 mojibake. **Do not rewrite this file with PowerShell text
operators** — use the editor, which round-trips encoding correctly.

## v18.5 — the lower run and the Fallen Hunter

**A second sewer level.** `sewer2`, 900x420, reached through a gate past Aldric
at the end of the Drowned Run. Three spaces: a long approach, a pillared
antechamber, and the hall. Darker again than the run above — four torches at r24
against the upper level's three at r26 — and the water is deeper, drawn as a
broad standing sheet rather than a channel line. Verified **100% connected**
(5,000+ cells, 0 orphans) with the Hunter and the note both reachable from the
entry.

**The Fallen Hunter** — 75 HP, 20 damage, 1.9s cooldown, 0.46s wind-up.

He wears **the player's silhouette**: the same hooded robe, the same belt, drawn
a size larger. The tell is the eyes — Nestor's burn red, his burn **green**
(`#4ce07a`), and they are the only green light in the game. He carries a long
straight order blade rather than a falchion, and the Order's waning-crescent
emblem sits on his chest, pulsing.

Note on damage: 20 is **raw**, like every other enemy in the game (skeletons
14-20, zombie 35). Through the base kit's 5 armour that lands as 15; through
Warden's Plate it lands as 10. He takes about 7 clean Long sword hits.

**Boss bar** — 280px, centred at the top, four quarter notches so it reads as a
length rather than a blob, a pulsing leading edge, the name `FALLEN HUNTER`, and
the remaining HP in 3x5 glyphs. It appears only once he has noticed you and
disappears the moment he is down.
**LIGHTING LAYER: after the composite.** It is HUD, not world.

**The recognition.** He does not simply die. Nestor walks over, reads the hem,
recognises the emblem *across a table*, and names him: **Elphric**, of the Order
of the Waning Light, missing six months and quietly written off as having walked
north. He did not walk north. That opens **What Became of Elphric** — report to
Herbert — which Herbert closes with an instruction to send it to the Order and a
push toward Yotan.

**Notes redistributed.** Two in the village, one in the Drowned Run (620,250),
one in the lower hall (420,230). `NOTES[].area` gates both rendering and pickup,
so the lore deepens as the player descends rather than all of it sitting in the
grass. `notesHere()` is the single accessor; the layer push, the light carve and
the proximity scan all read from it, so a note can never be collectable in a room
it does not render in.

### Verification for v18.4 / v18.5

- **65 assertions** across the gate, the key handover, the three equip paths, the
  skill curve, sewer2 geometry, the boss fight and the Elphric chain.
- **3,900 frames** across all six areas with attacks and movement: **0 errors**.
- **All ten** area transitions land the player walkable with 2+ open directions.
- Flood fill: sewer2 **100%**, Hunter and note 4 reachable, all decor inside rooms.
- Panel overflow measured on all six panels **plus the boss bar**: 0 outside.
- Identifier diff v18.3 -> v18.5: **0 lost**, 50 added.

## v18.6 — the art pass, the wall, and the hum

### Enemies read as what they are now

**Skeleton.** The old sprite failed for one structural reason: a leather cuirass
covered the whole torso, so the ribcage — the single thing that says "skeleton" —
was never visible, and the ribs behind it were four flat bars with nothing behind
them. Rebuilt:

- The cuirass is now **one shoulder pauldron**, leaving the chest open.
- The ribcage is a **dark cavity** with five tapered ribs drawn across it, so the
  gaps between the bones read as holes rather than as shading. Sternum over the
  top, clavicle above, pelvis with its own hollow below.
- The skull has a domed cranium, a **brow ridge casting a hard line** over the
  sockets, a nasal cavity, cheekbones, and a **hinged jaw with teeth**.
- Limbs are humerus / elbow / forearm / hand and femur / knee / tibia / foot,
  with the joints drawn a shade lighter so they read as separate bones.

**Animation**, all three enemies, three states each:

| | walk | attack | hit |
|---|---|---|---|
| Skeleton | 4-beat: leg swing, 1px bob, pelvis twist, arms counter-swinging, skull lagging a frame | `skelLean` pulls the body back 3px then drives 8px through | recoils along the facing, flashes, **and the jaw drops open** |
| Zombie | drag-step: 2px drop onto the carrying leg, trailing foot splayed with a drag mark, shoulders rolling, head lolling a beat late | winds back 4px slowly then falls 11px forward | shoved back 11px, head snaps over |
| Fallen Hunter | slow two-beat stride, 2px drop, dust off the stone, hem swinging a frame behind the legs | hauls back 5px then comes through 13px, sword arm lifting, **eyes flare as he commits** | rocks 5px without staggering — he has mass |

The zombie also got an open wound with two ribs visible in it, and the boss now
shows ribs through the open front of his robe.

### The village is enclosed

A stone curtain wall on the existing 36px boundary — dressed blocks in offset
courses, a capstone lip with an inward shadow, and crenellated corner towers so
the corners do not read as a mitre joint. One opening: a **wooden gate** centred
on the north side, two banded and studded timber leaves under a stone arch, with
a bar and lock across them. That gate is the intended way in and out.

The old rubble barricade at (535,112) is gone — the wall does that job now, and
doing it twice read as clutter. The wall needs no collision of its own because
`blocked()` already refuses anything outside the boundary; it is drawn in the
band just outside that line. Village connectivity re-verified at **100%**.

**LIGHTING LAYER: before the composite.** The wall obeys the dark, which is what
makes the village feel enclosed rather than merely bounded — you meet it as it
comes out of the black.

### Below ground the rain stops

Rain used to play everywhere, including three storeys underground. `updateAmbience`
now crossfades: rain out over 1.1s on the way down, and a **deep hum** in over
2.4s. The hum is two detuned sub-oscillators at 34 and 34.6Hz beating against
each other roughly once every 1.7 seconds, a 51Hz triangle body, and a whisper of
noise rolled off at 90Hz, all under a 120Hz lowpass with a 0.055 swell. Volume is
**0.055** — it should be felt as pressure, not heard as a sound. It never
resolves. `duckRain` is inert below ground and mute silences both beds.

### Numbers

| | was | now |
|---|---|---|
| Wraith | 12-20 | **24-40** |
| Skeleton | 14-20 | **28-40** |
| Zombie | 35 | **70** |
| Fallen Hunter | 75 HP, 20 dmg, 1.9s / 0.46s | **150 HP, 30 dmg, 1.52s / 0.368s** |

All raw, before armour. The boss is now ~13 Long sword hits against 7.

### Aldric lies down

He was propped against a wall with a pulsing letter, which read as a man having a
rest. He is now **flat on the floor**, laid along the x axis, head turned, hood
slipped back off the skull, one arm flung past his head and the letter fallen
face-up beside it. **Every animated term in the sprite was deleted** — verified
byte-identical across 4.6 simulated seconds. An idling corpse reads as a sleeper.

### Long sword vs short sword

They were pixel-identical: one `drawSwordIcon` and one fixed blade length. The
long sword now has its own icon (17 draws against 6) — longer narrower blade with
a fuller, a swept guard with down-turned quillons, wire-wrapped grip, disc pommel
— and `WEAPON_REACH` adds **+5** to the tip and the swing arc so it outreaches
the short sword in the hand.

**That is guarded geometry**, so the sweep was re-run for both blades:
short 37,384 samples / 11 hits, long 59,224 samples / **11 hits**, same single
pixel. `GUARD_BASE` is untouched and the blade only ever grows away from the
hand, so no new body intersections are possible. No regression.

### Encoding, again

The earlier PowerShell incident left damage I did not catch: my repair fixed the
**2-byte** mojibake but missed the **3-byte** sequences, so 10 em dashes and 2
bullets were still double-encoded — in Aldric's letter, the notes UI, quest
objectives and several toasts. All 12 repaired. The file is UTF-8 without BOM and
every content string is verified clean.

## v18.7 — the Road to Yotan closes, and the gate opens

The Yotan vow used to be a permanent `active` with no completion path. It now has
one, and it is the end of the current build.

**The chain:** kill the Fallen Hunter → *What Became of Elphric* opens → report to
Herbert → **both** quests close in that one conversation. Herbert sends you north
and says he will have the gate unbarred; a toast confirms it can be opened.
Walking to the north gate and pressing E swings it open on a heavy timber cue and
banners **GAME IN PROGRESS / THANK YOU FOR PLAYING**.

**Three gate states**, each drawn differently:

| state | look | interaction |
|---|---|---|
| barred | iron bar across both leaves, lock housing | `THE NORTH ROAD IS NOT YET WALKED` |
| unbarred | bar gone, empty brackets, a warm glint at the seam | opens it |
| open | leaves swung against the jambs, the road running off into black, the bar leaning where it was set down | a closing line |

**XP is not double-paid.** Yotan's 100 XP is normally granted on *acceptance*
(when Herbert first reads the letter). On completion it is only paid if the
player never accepted it — i.e. never found Aldric's letter and so never heard of
Yotan. Verified both ways: normal path pays 80 on the report, skip-the-letter
path pays 180.

**`gateopen` sound**: a 46Hz sawtooth groan under a sweeping lowpass, a
bandpassed creak wobbling at 6.5Hz as the hinge turns, and the bar knocking down
on stone at 1.45s.

### Verified
26 assertions across the full chain from a barred gate through the boss to the
banner, plus 6 for the never-took-the-letter path; the gate refuses at every
stage before the report; 1,700 frames soaked with the gate both open and shut;
identifier diff **0 lost**, 3 added; 0 mojibake.

## v18.8 — skeletons off the reference sheet, dialogue scrolling, hum scoped

### Skeletons redrawn from the reference

Three things were wrong against the sheet, and the third mattered most:

1. **Cool grey bone.** Palette is now warm cream — `#f2e9cf / #ded0ab / #bda882 /
   #8a765a` — instead of the old blue-grey.
2. **No outline.** Every bone element now sits on a 1px `#2b2318` bed. The hard
   dark silhouette is most of what makes the reference read at this size; without
   it the sprite was pale blocks on a dark floor.
3. **No shield.** The reference carries a round shield in every single frame.
   `drawSkelShield` draws it: dark boards, a broad lighter rim, plank seams and a
   central boss, carried on the arm opposite the falchion and depth-ordered so it
   sits behind him when he faces away.

Also: the leather pauldron is gone (the sheet has no armour), the skull is taller
with a higher dome and **big black deep-set sockets** as the whole face, the
cheekbones flare wide, the pelvis is a hipbone with two wings and a gap rather
than a slab, the spine is visible, and the limbs are thinner — 2px shafts with
knob joints.

**The boss keeps his hood**, as asked, but gets the same bone language
underneath: an outlined skull with a toothed jaw inside the hood cavity, a proper
ribcage of tapered ribs over a dark cavity through the open robe, bare shoulder
knobs, and outlined leg bones.

### Dialogue scrolls

The box was `bh=104` and cut its text at `by+bh-40`, leaving room for **exactly
two lines** — which is why most of Herbert's pages were truncated mid-word.

- Box is now `DLG_H=140` with `DLG_VIS=5` lines at `DLG_LINE=14`.
- Overflow **scrolls**: mouse wheel, or up/down arrows (movement is frozen while
  a dialogue is open, so the keys are free).
- It **auto-follows the typewriter** until the player scrolls manually, then
  hands over.
- A scrollbar appears only when there is something to scroll, with a blinking
  nudge while there is more below.
- **E scrolls to the end before advancing**, so pressing on can never skip past
  unread lines.

`choiceRects` and the whisper box now derive from `DLG_H` instead of a hardcoded
104, so they follow the box.

### The hum is sewers-only

`undergroundArea()` was undercroft + sewer + sewer2. It is now **sewer + sewer2**
only, and rain is gated to `rainyArea()` — the village alone. Three states:

| where | bed |
|---|---|
| village | rain |
| undercroft, house, shop | neither (the undercroft keeps its drips) |
| sewer, sewer2 | the hum |

So the hum can never be heard on the surface, indoors, or in the undercroft.

### Verified
7 ambience assertions across all six areas; 13 dialogue assertions including
*every wrapped line reachable by scrolling* and the longest real string in the
game; skeleton and boss rendered to an ASCII grid and read back; 2,600 frames
across four areas plus 200 with a dialogue open, 0 errors; identifier diff 1 lost
(`cx0`, a local in the rewritten ribcage) and 17 added; 0 mojibake.

## v19 — the game can be looked at, and the first sprite that is not fillRect

Two changes, and the first is the reason the second could be judged at all.

### Screenshots

`tools/shot.ps1` renders a frame of the running game to a PNG through headless
Chrome. Until now there was no way to see a change without asking the user to
play it, which is why every art regression in v18.6–v18.8 was caught by them: the
light being too bright three separate times, the missing ember trail, the white
fog balls. The browser preview pane is not composited and cannot be
screenshotted; Chrome headless can.

`hollowmere.html` is never touched. It is copied with `shot-bootstrap.js`
appended, which reaches `P`, `cam`, `lightKind`, `AREA` and `loop` **by bare
name** — top-level `let`/`const` share the global lexical scope across classic
scripts, so nothing has to be exported.

Four things it must do, each learned by getting a useless PNG:

* **Stub `requestAnimationFrame` before stepping.** Driving `loop()` while the
  game re-arms its own spawns thousands of chains and wedges the renderer.
* **Keep the REAL rAF as well.** The game's first `requestAnimationFrame(loop)`
  ran before the bootstrap existed. That callback holds a direct reference to
  `loop`, cannot be cancelled, and fires one genuine frame afterwards. So the
  debug overlay is painted through the saved real rAF, nested twice, to land on
  top; and `last` is reset to `performance.now()` first, so that trailing frame
  computes a small delta instead of jumping by the whole synthetic warm-up.
* **Seed `Math.random`.** Rain, grain and flicker all read it.
* **Close the panels.** The game opens with `HINTS.open` covering the screen. The
  first several attempts were photographs of "A GUIDE FOR THE HIRED SWORD".

And the rule that came out of it: **set state by its real name.** An early
version sprayed an assignment across guessed names — `held`, `heldLight`,
`holding`. One stuck: `heldLight` is the game's *getter function*, and
overwriting it with a string blacked out the canvas entirely. The state is
`lightKind`. The `set=` parameter is now guarded: it writes only a property that
already exists on an object that already exists, and never over a function.

### The chest

`CHEST` now draws `chest_1` (closed) and `chest_5` (open) from the castle pack —
37x22 against the old hand-plotted 35x31, shorter, which sits better beside a
29px player. Packed by `tools/spritepack.py` into a base64 data URI and spliced
in by `tools/inject.py` between markers, so **the one file survives**: 1.9 KB, no
assets folder, no build step, still opens by double-click.

Three decisions worth keeping:

* **The original chest is still there as a fallback.** If the atlas has not
  decoded, the hand-plotted one draws. A missing image should be a cosmetic blip,
  never a hole in the world.
* **The lock keeps its heartbeat.** The pulsing glow is drawn over the sprite,
  because in a game this dark the chest must announce itself from beyond the
  lamp's reach — the sprite alone is a brown lump in the black.
* **It stays inside the `layers` pass**, so it goes under the lighting composite
  and obeys the darkness like every `fillRect` around it. That is also why its
  palette treatment (sat 58%, val 78%) is *lighter* than the one used for belt
  icons: those are drawn after the composite and never darkened again.

A near-miss worth recording: under the green lamp the chest looked grey and
lifeless, and I was about to weaken the treatment. Shot again under torchlight it
reads as warm brown wood with iron bands — the green was the lamp tinting
everything, not the palette. Judge a world sprite under more than one light.

Verified: 900 frames in the village and 900 in the undercroft, `err:null` both;
identifier diff 0 lost, 3 added (`SPR`, `SPR_IMG`, `drawSprite`); closed, open
and torch-lit frames inspected.

## Still open

- Villagers still walk through each other; there is no crowd avoidance.
- Aldric's letter still lives on the Vows tab rather than in Notes. Arguably it
  belongs with the other readables now that the tab exists.
- Herbert's house is orphaned content: it renders, but nothing leads there.
- The Fallen Hunter drops nothing. He is a story beat, not a loot piñata, but a
  keepsake (his emblem?) would probably feel right.
- The 1px guard/hood graze from v18 is still there.
- Levels grant stats; skills are now a real choice, but only rank 1 exists.
- The Drowned Run has no quest attached — kills are their own reward.
- Yotan is a stub: an active journal entry and a closed road, nothing more.
- No save. Rags and teeth still buy nothing.
- The 1px guard/hood graze above.
- Rain still falls through roofs; torch still lights through walls.
