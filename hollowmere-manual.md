# HOLLOWMERE
### A grimdark top-down pixel-art RPG

---

## THE PITCH

You are **Nestor**, a sworn mercenary of the **Order of the Waning Light** — an
order that hunts and contains the supernatural, the occult, and whatever is left
behind when dark magic finishes with a place.

You have been sent to **Hollowmere**: a walled village in permanent rain, where
the dead do not stay down and the living have stopped asking why. Something under
the village has been handing out coins.

The whole game is one long night. There is no daylight, no safe hub, no fast
travel. You carry a lamp that is also your weapon of last resort, and every
minute you spend seeing clearly is a minute of charge you are not saving for the
thing waiting in the dark ahead.

**Tone:** Diablo's density and dread, filtered through the intimacy of an RPG
Maker village where you know all eight residents by name. Bleak, quiet, and
personal. Nobody is coming to help.

---

## THE CORE LOOP — DARKNESS AS THE MECHANIC

This is the thing that makes Hollowmere itself, and everything else is built
around it.

**The world is genuinely dark.** Not stylistically dim — a lighting layer is
composited over the scene, and outside your own light radius you cannot see
enemies, items, terrain, or the notes lying in the mud.

You carry a **dark iron lamp** (100 charge, drains 2/sec while lit, regenerates
1/sec while stowed) and a **pitch torch**. Light reveals the world — and holds the
wraiths off. Stay **exposed in darkness for more than 3.6 seconds** and they come
for you.

So every stretch of corridor is the same wager:

> Burn charge to see what is coming — or move blind, save the lamp, and find out
> the hard way.

Retreating into a lit room to let the lamp refill is a real tactic. Running dry
in the lower sewer is a real death. **Soul Powder** is the only heal, and it is
deliberately the one thing the shop will not buy from you — it is the currency of
that risk loop, and cashing it out would defuse the entire game.

Everything drawn *before* the lighting composite obeys the darkness. A tiny
handful of things are drawn *after* and are therefore self-luminous — the shop's
hanging sign, a Crystalized Tear's shimmer. That list is short on purpose: a
light in the dark should mean something.

---

## HOW IT PLAYS

**Controls:** WASD to move · right-click to swing · E to interact · I inventory ·
J journal · K skills · T hints · M mute. Mouse and keyboard only.

**Combat** is deliberate, not frantic. You have a wind-up, a strike, and a
recovery, and so does everything else — every enemy telegraphs before it commits,
and learning those tells *is* the skill. The Fallen Hunter's eyes flare green
right before he swings. The zombie hauls both arms back. The skeleton lifts its
blade overhead.

You are not fast enough to out-click a mistake. You are fast enough to read one
coming and step out of it.

**Progression** runs on two separate tracks, on purpose:

- **XP → Levels.** 50 XP for the first level. Each level grants +12 max health,
  and some grant +1 Edge (weapon damage).
- **Soul Embers → Skills.** A wholly separate currency for permanent unlocks.

---

## THE WORLD

Six hand-built spaces, all connected, no loading screens:

| Place | What it is |
|---|---|
| **The village** | Hollowmere itself. Stone walls, a single wooden gate on the north road, fifteen houses, braziers, rain. Eight residents. |
| **Ondrick's shop** | The only commerce in the game. A purse-and-`$` icon marks it on the minimap. |
| **Herbert's house** | Interior space. |
| **The undercroft** | Beneath the village. Reached by rope down a shaft. Where the skeletons are. |
| **The Drowned Run** | The sewers. Behind a locked iron gate with a key slot. Rain does not reach here — instead there is a low sub-bass hum, felt more than heard, that never resolves. |
| **The Drowned Run, lower level** | A long approach, a pillared antechamber, and the hall where the Fallen Hunter is waiting. |

Nothing in the world is placed by eye. Houses, braziers, decor, NPCs and spawn
points are procedurally generated, then **validated for overlap and for walkable
connectivity by flood fill** before they ship. If you can see it, you can reach
it.

---

## THE LORE

The story is told almost entirely through things you find, not things you are
told. Four scattered notes, a seven-page letter from a dead man, and one
conversation with a villager who has been carrying this for ten years.

### What the village knows

Herbert, the closest thing Hollowmere has to an elder:

> *"It came here. Ten years ago, near enough. Walked in off the north road in the
> middle of the afternoon, and it was polite, and it gave away coins. By the
> spring we had buried a third of the village and could not say what of."*

### What the notes say

A page torn from a dream-book, found in the village:

> *"When I dream, I can see them... Dark figures with golden eyes... They whisper
> of a world which came before. They say... This world is ours, we came first..."*

### What Brother Aldric found

Aldric went into the sewers before you and did not come out. You find his body
lying flat in the dark, and his letter on him — seven pages:

> *"There is a thing beneath this city that gives away tokens. It finds the
> desperate — the starving, the grieving, the ruined — and it offers them a small
> dark coin, and it asks for nothing. Nothing that it names, at least."*

> *"The shades we have been putting down above are not strangers. They took the
> coin. Every one of them."*

> *"It came to me last night. I could not look at it long. Skin like polished
> marble, white and without a seam. Eyes burning gold, and behind them nothing
> that I would call a mind."*

**The reveal:** every wraith you have been banishing for the villagers was one of
those villagers. You have not been protecting Hollowmere from an invader. You
have been cleaning up after a transaction its residents agreed to.

### The Fallen Hunter

The boss of the lower sewer is **Elphric** — a knight of your own Order, sent
before you and never heard from again. You recognise the coat before you
recognise him. Same hooded silhouette as Nestor, deliberately: he wore this
uniform, took the same oath, and the only visible difference is the colour
burning in the hood.

> *"The coat is ours. Waning Light, hemmed in the old way, the way they stopped
> doing before I took my oath."*

The Order sends people here. The Order does not get them back.

---

## QUESTS

Four, chained. The game currently ends after the fourth with an honest
**"GAME IN PROGRESS — THANK YOU FOR PLAYING"** card.

**1. Shades, be gone!** *(Herbert)* — Banish 10 wraiths. **50 XP**
The tutorial quest, disguised as a chore. Teaches the light loop by making you
fight in the dark ten times.

**2. What Stirs Beneath** *(Herbert)* — Clear the undercroft: 7 kills.
**50 XP + 25 gold**
Down the rope. Your first real dungeon, and the first hint that the village's
problem did not come from outside it.

**3. What Became of Elphric** *(The Order)* — Report to Herbert what you found
below. **80 XP**
Unlocked by finding Aldric's body and reading his letter. Ends in the lower sewer
against the Fallen Hunter.

**4. The Road to Yotan** *(Herbert)* — Travel north to the city of Yotan.
**100 XP**
Herbert finally tells you where the answers are kept. The north gate unlocks. You
open it, and the game — for now — ends there.

> *"Go north. Yotan. There are people there who write these things down instead of
> burying them. Ask for the archive, and say the Waning Light sent you."*

---

## ENEMIES

Every enemy is drawn from the **same underlying figure routine** as the player and
the villagers — same proportions, same walk cycle, same 29-pixel frame. They are
differentiated by palette, by overlaid detail, by stride rate, and by attack
grammar. The result is that everything in Hollowmere reads as the *same kind of
creature*: people, in various stages of not being people any more.

**Eye colour is the faction tell.** In a game this dark, you often see the eyes
before the silhouette — so each faction owns a colour and a pulse rate.

### Wraiths / Shades — `15 HP`, `24–40 dmg`
The village dead. Held off by light; they come for you after **3.6 seconds**
exposed in darkness. The core-loop enemy — they are less an obstacle than they
are the pressure that makes the lamp matter. Drop **Crystalized Tears**.

### Skeletons — `20 HP` (undercroft) / `30 HP` (sewers), `28–40 dmg`, 2.2s cooldown
Crisp bone, a dark green helm, a straight sword. The fastest stride in the game.
Wind the blade fully overhead — that is the tell — then drive it down. **Red
eyes, which go dark the instant they are struck**: the light in the thing gutters
and comes back. Drop gold and iron shackles.

### Zombies — `20 HP`, `70 dmg`, 3.1s cooldown
Swollen rot. Bare-handed and the hardest single hitter among the regulars — a
zombie that connects takes nearly half your starting health. Half the skeleton's
stride: he has mass and no hurry. A split flank you can see two ribs through, a
hanging jaw, one leg that drags and leaves a smear. **Attacks by throwing BOTH
arms forward** to grab — where the skeleton commits one arm to a blade, he
commits everything. Sick yellow-green eyes.

### The Fallen Hunter (Elphric) — `150 HP`, `30 dmg` — **BOSS**
The only enemy still *wearing* the Order: deep hood, belt, the waning crescent on
his chest. Heavy pauldrons, bare bone shins under the hem, one long blade — the
longest reach in the game. The slowest stride of anything that walks. His eyes
**flare green as he commits to a swing**, and reading that flare is the fight. He
has a named health bar. He is not a monster you found; he is a colleague.

---

## INVENTORY & EQUIPMENT

A deliberate, non-automatic system: **picking something up never replaces
something you are using.** Everything goes to the pack, and equipping is an
explicit act.

- **12 pack slots.** The first 9 double as the hotbar.
- **Equipment slots:** weapon, head, torso, legs, boots, **armour**, pack.
- Hover a slot and press **E** to equip or use it, or drag it onto the slot.
- Stacking items (Tears, Soul Powder) track a single count and show it on the icon.
- Aldric's letter is readable from the inventory panel — an unreadable letter is a
  worse bug than a tidy rule.

**Weapons:** the short sword (a stubby arming sword, brass guard) and the long
sword (longer, narrower, swept guard, +5 reach). Damage is base weapon damage plus
your accumulated **Edge** from levelling.

**Notable items:** Dark iron lamp · Pitch torch · Soul powder · Crystalized Tear ·
Iron shackles · Frayed rope · Sewer Key · Necromancer Sigil · Aldric's Letter

---

## ECONOMY

**Ondrick** runs the only shop, out of a house marked on the minimap with a purse
and a `$`.

**He sells:**
| Item | Price | Effect |
|---|---|---|
| Rusted Mail | 30g | +2 armour |
| Warden's Plate | 90g | +5 armour |

**He buys:** Crystalized Tears (5g) and Iron Shackles (8g).

**He will not touch Soul Powder** — and says so:
> *"Soul powder I will not touch. Keep it. You will need it."*

Armour subtracts flat from every incoming hit, with a floor of 1 damage. Against a
70-damage zombie, Warden's Plate is the difference between three hits killing you
and four.

---

## SKILLS

Bought with **Soul Embers**, a currency entirely separate from gold and XP. Opened
with **K**. Three skills, one rank each, **30 embers** apiece, with the cost
scaling +20% compounding per purchase — so the second costs 36, the third 43.

**Thy Flame Shines Brighter**
*The dark gives more ground to the light you carry.*
A wider light radius. Buys you reaction time everywhere in the game.

**Perseverance**
*The lamp drinks the dark back in faster when stowed.*
Faster recharge while the lamp is put away. Rewards the discipline of moving dark
and lighting only when you must.

**Soul Steal**
*What you put down leaves something behind.*
Kills return something to you. Turns combat from pure attrition into a resource
you can farm.

The three map cleanly onto the three ways to survive Hollowmere: **see more**,
**recover faster**, or **take it out of them**.

---

## PRESENTATION

**Internal resolution: 576×360**, upscaled with hard pixel edges. Never raised —
every sprite is hand-sized to that grid.

**One self-contained HTML file. Zero dependencies.** No engine, no libraries, no
CDN, no image files, no audio files, no build step.

- **Every sprite is procedural** — drawn from filled rectangles at runtime. There
  is not a single image asset in the project.
- **Every sound is Web Audio synthesis** — no samples. Rain is filtered noise. The
  sewer hum is two detuned sub-oscillators at 34 and 34.6 Hz beating against each
  other about once every 1.7 seconds, under a 51 Hz body, with a whisper of noise
  rolled off at 120 Hz so it has air without ever becoming a texture you notice.
  Positional sounds use HRTF panning.
- **Small text is hand-plotted** from 3×5 bitmap glyphs. Below about 8 pixels,
  ordinary font rendering turns to mush under the pixelated upscale.

Ambience is strictly per-area: rain belongs to the village and the hum belongs to
the sewers, and neither is ever audible where it does not belong.

---

## CURRENT STATE

Playable start to finish: arrive in Hollowmere, take Herbert's quests, clear the
undercroft, find Aldric, read his letter, unlock the Drowned Run, kill Elphric,
report back, and open the north gate.

**The ending is a card, not a conclusion.** Yotan is real, named, and reachable in
fiction only — the road north exists so the story has somewhere to go.

### Where it would go next
- **Yotan** — the archive city. The first place in this world where somebody wrote
  things down instead of burying them.
- **The thing that hands out coins.** Skin like polished marble, white and without
  a seam, eyes burning gold. It has been described three times and seen zero.
- **The Order.** It has now lost two hunters to one village. Somebody sent them.
