# Recipe Standard Format

The contract for every recipe in this repo. The Kindle (Memento) reads the
root-level `.md` files from GitHub and displays them as **uniform plain text**.

## How Memento renders (the constraint that drives everything)

Tested on-device: Memento strips markdown markup but applies **no styling**.
- `#` headings, `**bold**`, `*italic*` -> the symbols are removed but the text
  stays the same size and weight as everything else. They do nothing.
- `<!-- HTML comments -->` -> shown as literal text. Never use them.

So the only formatting that actually creates hierarchy on the device is:
- **CAPITAL LETTERS** for the title and section labels.
- **Blank lines** for separation (these do render).

Write recipes in plain text using those two tools. No `#`, no `**`, no comments.

## Why the rest of the structure is the way it is

- The pre-commit hook builds `manifest.txt` from **root-level** `.md` files only
  (`ls -1` is non-recursive). Anything in `_system/` or `_raw/` never reaches the
  Kindle. Tooling and raw drops live in subfolders on purpose.
- `_raw/` is gitignored. Raw recipes from cookbooks/sites can be copyrighted and
  this repo is **public**, so they never get pushed.
- The ingredient list is the source of truth for grocery lists - written so you
  can shop straight off it. `_system/ingredients.md` stays as the canonical-name
  + gram-conversion reference; the device never sees it.

## The template

````text
RECIPE TITLE

Serves 4 · 45 min


INGREDIENTS

- 8 chicken thighs
- 1 can crushed tomatoes
- 450 g elbow pasta
- 1 onion, diced
- salt, to taste


STEPS

1. First step.

2. Second step.
````

Title in CAPS on the first line. `Serves N · time` under it. Two blank lines
before each CAPS section label. One blank line between every ingredient group and
between every step.

## Rules

### Title and section labels

- Recipe title: ALL CAPS, first line of the file.
- `Serves N · time` line directly under the title (integer serves; total
  active+passive time, e.g. `35 min`, `3h 30m`).
- Section labels (`INGREDIENTS`, `STEPS`): ALL CAPS, with two blank lines above.
- Ingredient sub-groups (only when the recipe truly has components, e.g.
  `Roast chicken`, `Tzatziki`): Title Case, so they read as subordinate to the
  CAPS section label. For multi-component step lists, lead the step with a CAPS
  component tag (`1. ROAST CHICKEN. ...`).

### Ingredient lines

Grammar: `<quantity> <unit> <item>[, <prep>]`. Write them so the list doubles as
a shopping list - a reader should know roughly how much to buy.

- **Whole items bought and used as pieces stay counts**: `8 chicken thighs`,
  `1 onion, diced`, `3 cloves garlic`, `2 lemons`, `4 scallions`, `6 shallots`,
  `2 eggs`.
- **Canned goods are counted in cans**: `1 can crushed tomatoes`,
  `2 cans chickpeas, drained`.
- **Things you measure into the dish are in grams**: ground meat, rice, pasta,
  couscous, flour, sugar, cheese, oils, sauces, stock, yogurt, spices. Convert
  cups/tbsp/tsp/oz/lb to grams (liquids too - water/stock/wine ≈ 1 g per ml,
  oil ≈ 0.92, milk ≈ 1.03).
- **Seasonings to taste**: `salt, to taste`, `black pepper, to taste`.
- `item` is the canonical name from `_system/ingredients.md` (lowercase,
  singular); `prep` is optional, after a comma.
- One ingredient per line.

### Steps

`STEPS` label, numbered list, plain imperative prose, **a blank line between each
step**. Step-level timing ("simmer 30 min") is fine.

## Intake workflow

1. Drop a raw recipe (paste, `.txt`, `.md`, screenshot text) into `_raw/`.
2. Run `/push-recipes`. It converts each raw file to this plain-text template at
   the repo root - CAPS title/sections, counts for whole/canned items, grams for
   measured items, using `_system/ingredients.md` for canonical names and
   conversions, adding new registry entries when needed - then commits and pushes
   to the Kindle. The run reports any estimated servings/times/conversions and
   new ingredients added.

Raw files stay in `_raw/` (gitignored) as the archive of originals.
