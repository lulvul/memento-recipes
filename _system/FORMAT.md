# Recipe Standard Format

The contract for every recipe in this repo. The Kindle (Memento) reads the
root-level `.md` files from GitHub, so the **visible** part stays clean and
readable. Machine data for grocery lists rides along in hidden HTML comments the
device doesn't render.

## Why the structure is the way it is

- The pre-commit hook builds `manifest.txt` from **root-level** `.md` files only
  (`ls -1` is non-recursive). So anything in `_system/` or `_raw/` never reaches
  the Kindle. Tooling and raw drops live in subfolders on purpose.
- `_raw/` is gitignored. Raw recipes copied from cookbooks/sites can be
  copyrighted, and this repo is **public**. They never get pushed.

## The template

````markdown
# Recipe Title

<!-- meta: serves=4 | time=45m | tags=pasta,comfort -->

## Ingredients
- 450 g elbow pasta
- 42 g butter
- 1 onion, diced
- salt, to taste

<!-- ingredients
elbow pasta | 450 | g |
butter | 42 | g |
onion | 1 | ea | diced
salt | 0 | to-taste |
-->

## Steps

1. First step.
2. Second step.
````

That's the whole thing. Title, a hidden meta line, a readable ingredient list, a
hidden structured ingredient block, and numbered steps.

## Rules

### Visible ingredient lines

Grammar: `<quantity> <unit> <item>[, <prep>]`

- **Everything weighable is in grams.** Convert cups, tbsp, tsp, oz, lb to grams.
  Liquids too - a kitchen scale weighs them fine (water/stock/wine ≈ 1 g per ml,
  oil ≈ 0.92, milk ≈ 1.03; close enough to weigh straight).
- **Whole/discrete items stay as counts**: `1 onion, diced`, `3 cloves garlic`,
  `2 eggs`. Their gram weight lives in the structured block, not the visible line.
- **Seasonings to taste**: `salt, to taste`, `black pepper, to taste`.
- `item` is the canonical name from `_system/ingredients.md` (lowercase,
  singular). `prep` is optional, after a comma (diced, grated, drained).
- One ingredient per line. No sub-headers inside the list unless the recipe truly
  has components (e.g. `### Sauce` / `### Filling`); if so, repeat `## Ingredients`
  grouping is fine but keep the structured block flat.

### Hidden meta line

`<!-- meta: serves=N | time=Xm | tags=a,b -->`
- `serves` = integer. `time` = total active+passive, e.g. `45m`, `3h`.
- `tags` = free-form, comma-separated, lowercase. Used for finding recipes later.

### Hidden structured block

Pipe-delimited, one ingredient per line, between `<!-- ingredients` and `-->`:

`canonical_item | qty | unit | prep`

- `canonical_item` must exist in `_system/ingredients.md` (add it there if new).
- `unit` is one of: `g`, `ml`, `ea`, `clove`, `can`, `pinch`, `to-taste`.
  Prefer `g`. Use `ea` for whole counts, `clove` for garlic, `can` only when the
  can size is the meaningful unit.
- `qty` for `to-taste` items is `0`.
- This block is the source of truth for grocery lists. Keep it in sync with the
  visible list when editing by hand (intake generates both together).

### Steps

Numbered list, plain prose, imperative. No times baked into prose if they're in
the meta line, but step-level timing ("simmer 30 min") is fine and expected.

## Intake workflow

1. Drop a raw recipe (paste, `.txt`, `.md`, screenshot text) into `_raw/`.
2. Run `/push-recipes`. It converts each raw file to this template at the repo
   root, converts measures to grams using `_system/ingredients.md` densities,
   reconciles every ingredient against the registry (adding new canonical entries
   when needed), then commits and pushes to the Kindle - one command, format and
   publish together. The run reports any estimated servings/times/conversions and
   new ingredients added.

Raw files stay in `_raw/` (gitignored) as the archive of originals.
