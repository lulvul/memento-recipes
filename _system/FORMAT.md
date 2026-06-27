# Recipe Standard Format

The contract for every recipe in this repo. The Kindle (Memento) reads the
root-level `.md` files from GitHub and renders them as markdown. **Everything in
the file shows on the device** - Memento renders HTML comments as visible text,
so there is no "hidden" channel. Keep every recipe clean and readable; nothing
rides along invisibly.

## Why the structure is the way it is

- The pre-commit hook builds `manifest.txt` from **root-level** `.md` files only
  (`ls -1` is non-recursive). So anything in `_system/` or `_raw/` never reaches
  the Kindle. Tooling and raw drops live in subfolders on purpose.
- `_raw/` is gitignored. Raw recipes copied from cookbooks/sites can be
  copyrighted, and this repo is **public**. They never get pushed.
- The visible ingredient list is the source of truth for grocery lists - it's
  written so you can shop straight off it (counts for whole things, cans for
  canned, grams for what you measure in). `_system/ingredients.md` stays as the
  canonical-name + gram-conversion reference, not something the device sees.

## The template

````markdown
# **Recipe Title**

**Serves 4 · 45 min**

## **Ingredients**

- 8 chicken thighs
- 1 can crushed tomatoes
- 450 g elbow pasta
- 1 onion, diced
- salt, to taste

## **Steps**

1. First step.

2. Second step.
````

That's the whole thing. A bold title, a serves/time line, a readable ingredient
list, and numbered steps with a blank line between each.

## Rules

### Title and headers

- Recipe title is a bold H1: `# **Recipe Title**`.
- Section headers are bold H2: `## **Ingredients**`, `## **Steps**`. Component
  sub-groups inside ingredients are plain H3: `### Roast chicken`.
- The bold markers are deliberate - Memento renders headings without much weight,
  so the `**` is what makes titles read as titles on the device.

### Serves / time line

Right under the title: `**Serves N · time**` (e.g. `**Serves 8 · 35 min**`,
`**Serves 6 · 3h 30m**`). `serves` is an integer; `time` is total active+passive.

### Visible ingredient lines

Grammar: `<quantity> <unit> <item>[, <prep>]`. Write them so the list doubles as
a shopping list - someone reading it should know roughly how much to buy.

- **Whole items you buy and use as pieces stay counts**: `8 chicken thighs`,
  `1 onion, diced`, `3 cloves garlic`, `2 lemons`, `4 scallions`, `2 eggs`,
  `6 shallots`.
- **Canned goods are counted in cans**: `1 can crushed tomatoes`,
  `2 cans chickpeas, drained`. The can is the buy-and-use unit.
- **Things you measure into the dish are in grams**: ground meat, rice, pasta,
  couscous, flour, sugar, cheese, oils, sauces, stock, yogurt, spices. Convert
  cups/tbsp/tsp/oz/lb to grams (liquids too - water/stock/wine ≈ 1 g per ml,
  oil ≈ 0.92, milk ≈ 1.03; close enough to weigh straight).
- **Seasonings to taste**: `salt, to taste`, `black pepper, to taste`.
- `item` is the canonical name from `_system/ingredients.md` (lowercase,
  singular). `prep` is optional, after a comma (diced, grated, drained).
- One ingredient per line. Use `### Sub-group` headers only when the recipe truly
  has components (e.g. `### Sauce` / `### Filling`).

### Steps

Bold `## **Steps**` header, numbered list, plain imperative prose, **a blank line
between each step** so they don't run together on the device. Step-level timing
("simmer 30 min") is fine and expected.

## Intake workflow

1. Drop a raw recipe (paste, `.txt`, `.md`, screenshot text) into `_raw/`.
2. Run `/push-recipes`. It converts each raw file to this template at the repo
   root - counts for whole/canned items, grams for measured items, using
   `_system/ingredients.md` for canonical names and conversions - reconciling
   every ingredient against the registry (adding new canonical entries when
   needed), then commits and pushes to the Kindle. The run reports any estimated
   servings/times/conversions and new ingredients added.

Raw files stay in `_raw/` (gitignored) as the archive of originals.
