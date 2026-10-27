# FitFindr

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first** — setup, every
> command, and what to do when something breaks.
>
> Once `python test.py` passes:
>
> ```bash
> python app.py listings --full -n 6      # read the data (Milestone 1)
> python app.py fields                    # what you can filter on
> python app.py ask 'vintage graphic tee under $30'
> ```
>
> All three tools are stubs, so that last command will do nothing useful yet.
> That's the starting position.
>
> **The rest of this file is your submission.** Fill it in as you go.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     HOW TO USE THIS FILE

     This is your submission. Fill each section in as you finish the milestone
     it belongs to — don't leave it all to the end.

     Unit 3 asks for the first five sections. Unit 4 adds the five below them.
     Leave the unit 4 sections alone until then; they're here so you know
     what's coming.

     Everything is pasted as TEXT. No screenshots, no images, no video links.
     A typed block of output gets full credit; a picture of the same output
     gets none.
     ───────────────────────────────────────────────────────────────────────── -->

<!-- ═══════════════════════ UNIT 3 — THE BUILD ═══════════════════════ -->

## What This Does

FitFindr takes a plain-language shopping request like "vintage graphic tee under $30, size M" and turns it into a structured search. It finds matching listings, suggests outfit combinations from the user’s wardrobe, and writes a short social caption for the selected item. The agent uses session state to carry the item from search into styling and caption generation, so the user only types the original request once.

---

## Tool Inventory

<!-- Four lines per tool. This is worth 2 points and it's the single most
     common place students lose them.

     "Returns a list" earns NOTHING. The description has to say what is IN
     the list.

     The empty case isn't optional either — it's the thing your loop branches
     on, and if you don't decide it here you'll discover it as a crash in
     Milestone 5. -->

### `search_listings`

- **What it does:**
  returns a list of dicts of relevant listings to the query after searching for the listings data for items matching a description, and optionally a size and a price ceiling.

- **Inputs:** description: str, size: str | None = None, max_price: float | None = None
- **Returns:** list[dict]
- **When it has nothing:** Returns an empty list when nothing matches — an empty list, not None and not an exception. Your loop branches on this.

### `suggest_outfit`

- **What it does:** takes in a dictionary and wardrobe dictionary and suggests one two to outfits
  based off the user's requirements. Calls the model through function generate() to generate suggestions after being fed the two dicts.
- **Inputs:** new_item: dict, wardrobe: dict
- **Returns:**A non-empty string with outfit suggestions. With an empty wardrobe, return general styling advice rather than raising or returning "". Unit 4 has you trigger the empty wardrobe on purpose, so decide now what it should do.
- **When it has nothing:** A non-empty string with outfit suggestions. With an empty wardrobe, return general styling advice rather than raising or returning ""

### `create_fit_card`

- **What it does:** writes a short caption about the fit that an influencer would post about a find. the goal is to make the reader feel energized and interested in the fit.
- **Inputs:** outfit: str, new_item: dict
- **Returns:** A two-to-four sentence caption about the outfit.
- **When it has nothing:** If `outfit` is empty or whitespace, return a descriptive message rather than raising.

---

## Planning Loop

<!-- Your branch rule, stated as a rule — the condition AND both paths — plus
     the file and function that holds it.

     Like this:
       "If search_listings returns an empty list, put a message in the session
        and stop. Otherwise take the first result and go to suggest_outfit."
        — agent.py::run_agent

     The grader checks your code against what you claim here, so the file and
     function have to be real. -->

**Branch rule:** If `search_listings` returns an empty list, store a helpful message in `session["error"]` and stop. Otherwise, take the first result, store it in `session["selected_item"]`, and continue to `suggest_outfit` and `create_fit_card`.

**Where it lives:** `agent.py::run_agent`

**How the query is parsed:** I used a regex-based parser in `agent.py` to extract the description, the size, and the price cap from the user’s query. It strips the price phrase and size phrase out of the freeform text, then keeps the rest as the search description.

**What moves through the session:** `query` -> `parsed` -> `search_results` -> `selected_item` -> `outfit_suggestion` -> `fit_card`; if the search is empty, the loop exits early and leaves the later fields as `None` while setting `error`.

---

## Sample Run

<!-- Two things go here.

     1. One FULL query and its output, pasted as text.
     2. Your three per-tool terminal tests — the command and what it printed. -->

**One full query**

```
$ python app.py ask '...'

```

**The three tools, tested one at a time**

```
$ python -c "from tools import search_listings; print(search_listings('graphic tee', max_price=30))"

```

[{'id': 'lst_002', 'title': 'Y2K Baby Tee — Butterfly Print', 'description': 'Super cute early 2000s baby tee with butterfly graphic. Fitted crop length. Tag says medium but fits like a small.', 'category': 'tops', 'style_tags': ['y2k', 'vintage', 'graphic tee', 'cottagecore'], 'size': 'S/M', 'condition': 'excellent', 'price': 18.0, 'colors': ['white', 'pink', 'purple'], 'brand': None, 'platform': 'depop'}, {'id': 'lst_006', 'title': 'Graphic Tee — 2003 Tour Bootleg Style', 'description': 'Vintage-style bootleg tee with faded graphic. Slightly boxy fit. 100% cotton, soft and worn-in.', 'category': 'tops', 'style_tags': ['graphic tee', 'vintage', 'grunge', 'streetwear', 'band tee'], 'size': 'L', 'condition': 'good', 'price': 24.0, 'colors': ['black'], 'brand': None, 'platform': 'depop'}, {'id': 'lst_017', 'title': 'Mesh Long-Sleeve Top — Black', 'description': 'Sheer black mesh long-sleeve. Great for layering under a graphic tee orover a bralette. Stretchy material, fits true to size.', 'category': 'tops', 'style_tags': ['y2k', 'grunge', 'goth', 'layering'], 'size': 'S/M', 'condition': 'excellent', 'price': 15.0, 'colors': ['black'], 'brand': None, 'platform': 'depop'}, {'id': 'lst_033', 'title': 'Vintage Band Tee — Faded Grey', 'description': 'Faded grey band-style tee with distressed graphic. Crew neck. Fits boxy. Well-loved but no holes ormajor damage.', 'category': 'tops', 'style_tags': ['vintage', 'grunge', 'band tee', 'graphic tee', 'streetwear'], 'size': 'L', 'condition': 'fair', 'price': 19.0, 'colors': ['grey', 'charcoal'], 'brand': None,'platform': 'depop'}, {'id': 'lst_011', 'title': 'Low-Rise Cargo Pants — Khaki', 'description': 'Y2K eralow-rise cargo pants. Lots of pockets. Khaki color, slightly distressed at the hems. Great for layering with a long tee.', 'category': 'bottoms', 'style_tags': ['y2k', 'cargo', '2000s', 'streetwear'], 'size': 'W29', 'condition': 'fair', 'price': 27.0, 'colors': ['khaki', 'tan'], 'brand': None, 'platform': 'poshmark'}, {'id': 'lst_015', 'title': 'Vintage Graphic Hoodie — Faded Black', 'description': 'Faded black pullover hoodie with barely-visible vintage graphic on the chest. Cozy interior. Some pilling but adds to theworn-in look.', 'category': 'tops', 'style_tags': ['vintage', 'grunge', 'graphic', 'streetwear'], 'size': 'L', 'condition': 'fair', 'price': 26.0, 'colors': ['black', 'charcoal'], 'brand': None, 'platform': 'depop'}]

```
$ python -c "from tools import suggest_outfit; ..."
python -c "from tools import suggest_outfit; from utils.data_loader import get_example_wardrobe, load_listings; print(suggest_outfit(load_listings()[0], get_example_wardrobe()))"


```

Here are two effortless ways to style your new vintage Levi's:

**1. Casual Streetwear Look**

- **Top:** White ribbed tank top
- **Outerwear:** Vintage black denim jacket
- **Shoes:** Chunky white sneakers
- **Accessories:** Black crossbody bag
- **Why it works:** The **white ribbed tank top** provides a clean, fitted contrast to the straight-leg vintage denim, leaning into a classic 90s streetwear aesthetic. Layering the black denim jacket adds edge, while the chunky sneakers tie the casual vibe together.

**2. Relaxed Cozy Layers**

- **Top:** Oversized grey crewneck sweatshirt
- **Accessories:** Brown leather belt, black crossbody bag
- **Shoes:** Black combat boots
- **Why it works:** Tucking the hem of the **oversized grey crewneck sweatshirt** into the jeans creates an effortless high-low silhouette (cozy top, structured bottoms). Tying it together with the brown leather belt and grounding the look with black combat boots adds a touch of rugged, vintage contrast.

```

$ python -c "from tools import create_fit_card; ..."
python -c "from tools import create_fit_card; from utils.data_loader import load_listings; print(create_fit_card('jeans and white sneakers', load_listings()[0]))"
```

## Honestly heartbroken these don’t fit me right because the wash on these vintage 501s is literally perfection. Just throwing them on with some beat-up white sneakers and a t-shirt is the easiest outfit ever. Grabbed them on depop for $38 and just want them to go to a good home now!

## How I Used AI

<!-- Two specific moments. What you asked, what came back, what you changed.

     "I used Claude to help me code" is not enough.

     "I gave Claude my search_listings spec. It returned None on no match
     instead of an empty list, so I changed it" is the level we want. -->

**Moment 1**

- _What I asked for:_ I asked ai to implement search_listings spec, afterwards i tested it and read it to ensure it gave me the right outputs.
- _What came back:_ Right outputs came back on the first try so i had no changes i needed to do.
- _What I changed:_ I had to change nothing

**Moment 2**

- _What I asked for:_ I asked AI to help me design the outfit prompt so it used the user’s existing wardrobe instead of giving generic advice.
- _What came back:_ It suggested a prompt that explicitly listed wardrobe items and asked the model to reference them by name.
- _What I changed:_ I rewrote the `suggest_outfit` prompt to include `wardrobe["items"]`, name at least one piece in each suggestion, and fall back to general styling advice when the wardrobe is empty.

<!-- ═══════════════════════ UNIT 4 — THE TEST ═══════════════════════

     Don't fill these in during unit 3.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Run Log — Before

<!-- Five criteria, five tries each, in this exact format.

     Five, because your criteria are written out of five. Mark each try PASS
     or FAIL, count the passes, and read that count against your target — a
     row targeting 4 of 5 with three PASS cells is MISSED (3/5).

     `python run_eval.py --label before` runs everything and writes the table
     into results/. Paste it here and fill in the verdicts. -->

Source: `results/run_2026-10-26_2307_before.md`, produced by `run_eval.py::main` running `agent.py::run_agent` — 5 tries per scenario, caching off, temperature 0.9, 40 real model calls.

| Criterion | Target | Try 1 | Try 2 | Try 3 | Try 4 | Try 5 | Verdict |
| --------- | ------ | ----- | ----- | ----- | ----- | ----- | ------- |
| 1. Matching query completes all three tools and returns a fit card | 4 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |
| 2. Impossible query stops before `suggest_outfit` with a message naming what to change | 5 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |
| 3. First search result = `session["selected_item"]` = item `suggest_outfit` received | 5 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |
| 4. Fit card contains the item's exact price | 5 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |
| 5. Empty wardrobe still returns a non-empty outfit suggestion | 5 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |

How each try was scored:

- **1:** PASS if the try was not stopped early and a fit card came back. All five reached `[3] create_fit_card` (fit cards 189–284 chars).
- **2:** PASS if the trace ends at `[1]` with `out: [] (empty)` and the session error names what to change. All five stopped after one step.
- **3:** No dedicated scenario was added for this criterion, so it was scored on the five tries of scenario 1. PASS if the first title in step 1's `out:` matches both `selected_item` and step 2's `in:`. All five showed `Y2K Baby Tee — Butterfly Print` in all three places.
- **4:** Scored on scenario "fit card includes price" (`vintage band tee under $40`). PASS if `$24` (the selected item's price) appears in the fit card text. All five did.
- **5:** Scored on scenario "empty wardrobe" (`denim jacket under $50`, `--empty-wardrobe`). PASS if no crash and the outfit suggestion is non-empty. All five returned general styling advice. (The same scenario also ran once more as a leftover diagnostic row with `criterion: None`; those five tries also passed and are not counted.)

**Real output from one try per criterion**

Criterion 1 — `agent.py::run_agent`, try 1, `vintage graphic tee under $30`:

```
[1] search_listings (via MCP)
      in:  dict with keys: description, size, max_price
      out: 10 items: Y2K Baby Tee — Butterfly Print, Graphic Tee — 2003 Tour Bootleg Style, Vintage Band Tee — Faded Grey … +7 more
[2] suggest_outfit
      in:  Y2K Baby Tee — Butterfly Print ($18.0, depop)
      out: Here are 2 practical outfit ideas using your new butterfly baby tee and existing wardrobe:  **1. Casual Y2K St…
[3] create_fit_card
      in:  Y2K Baby Tee — Butterfly Print ($18.0, depop)
      out: obsessed with this butterfly baby tee, giving major early 2000s energy. look so cute with some baggy dark deni…
```

Criterion 2 — `agent.py::run_agent`, try 1, `designer ballgown size XXS under $5`:

```
- stopped early: yes — I couldn't find a matching listing. Try changing the item description, size, or maximum price.
- selected_item: (none)
- search_results: 0

[1] search_listings (via MCP)
      in:  dict with keys: description, size, max_price
      out: [] (empty)
```

Criterion 3 — `agent.py::run_agent` session + trace, try 1 of scenario 1:

```
- selected_item: Y2K Baby Tee — Butterfly Print ($18.0, depop)
[1] search_listings (via MCP)  out: 10 items: Y2K Baby Tee — Butterfly Print, …
[2] suggest_outfit             in:  Y2K Baby Tee — Butterfly Print ($18.0, depop)
```

Criterion 4 — `tools.py::create_fit_card`, try 1, `vintage band tee under $40`:

```
- selected_item: Graphic Tee — 2003 Tour Bootleg Style ($24.0, depop)

Fit card:
Found the sickest 2003 tour bootleg tee while digging through the racks the other day. It looks so good tucked into baggy jeans with some beat-up combat boots, or dressed down with khaki trousers and sneakers. Grab it on my depop for $24 before I change my mind and keep it!
```

Criterion 5 — `tools.py::suggest_outfit`, try 1, `denim jacket under $50`, empty wardrobe:

```
Here are two practical, easy-to-recreate outfit ideas for your cropped light-wash denim jacket:

**1. The 90s Streetwear Look**
*   **Top:** A ribbed black or white baby tee.
*   **Bottoms:** High-waisted, wide-leg utility cargo pants in olive green or beige. 
*   **Footwear:** Chunky white sneakers (like Nike Air Force 1s).
*   **Accessories:** A black nylon shoulder bag and simple silver hoop earrings.
…
```

---

## Verdicts and Diagnoses

<!-- MET or MISSED per criterion against LAST UNIT's target, plus a sentence on
     how you decided.

     Then, for every miss: which of the four places it happened — a tool, the
     loop's branch, the session, or the model's output — AND the mechanism.

     Not a diagnosis:  "The fit card was bad."
     A diagnosis:      "The fit card criterion missed on 2 of 5 items. Both had
                        an empty brand field. My prompt puts the brand in the
                        first sentence, so the card opened with a blank and read
                        like a fragment. The tool worked; the prompt assumed a
                        field that isn't always there."

     Look for a pattern. Three misses on the same tool is one problem, not
     three. -->

| #   | Criterion | Target | Verdict | How I decided |
| --- | --------- | ------ | ------- | ------------- |
| 1   |           |        |         |               |
| 2   |           |        |         |               |
| 3   |           |        |         |               |
| 4   |           |        |         |               |
| 5   |           |        |         |               |

**Diagnoses**

---

## Loop Trace

<!-- One full run, printed step by step, with the MCP call visible in it.

     `python app.py ask '...' --trace` once you've added the trace.step()
     calls in Milestone 2.

     Worth pasting BOTH the happy path and the empty-search path. The empty
     one should be visibly shorter, because it stops. If your two traces are
     the same length, your branch isn't working — and this is the fastest way
     anyone will ever find that out. -->

**Happy path**

```
python app.py ask 'vintage graphic tee under $30' --trace
[1] search_listings (via MCP)
      in:  dict with keys: description, size, max_price
      out: 10 items: Y2K Baby Tee — Butterfly Print, Graphic Tee — 2003 Tour Bootleg Style, Vintage Band Tee — Faded Grey … +7 more
[2] suggest_outfit
      in:  Y2K Baby Tee — Butterfly Print ($18.0, depop)
      out: Here are 2 practical outfit ideas using your new baby tee and existing wardrobe:  **1. Retro Streetwear** * **…
[3] create_fit_card
      in:  Y2K Baby Tee — Butterfly Print ($18.0, depop)
      out: Found this precious little butterfly baby tee and I'm honestly so obsessed. Fits like an absolute dream and gi…

  Found:    Y2K Baby Tee — Butterfly Print — $18.0 on depop

  Outfit:   Here are 2 practical outfit ideas using your new baby tee and existing wardrobe:

**1. Retro Streetwear**
* **Items to use:** Butterfly baby tee + Baggy dark-wash straight-leg jeans + Chunky white sneakers + Black crossbody bag
* **Why it works:** The tight-fitting baby tee creates a flattering 90s silhouette when balanced with the volume of the baggy dark-wash jeans. Chunky white sneakers tie in the white from the tee for an effortless, casual look.

**2. Edgy Y2K Contrast**
* **Items to use:** Butterfly baby tee + Wide-leg khaki trousers + Black combat boots + Vintage black denim jacket
* **Why it works:** The sweet, pastel butterfly print provides a great contrast against the tougher black combat boots and denim jacket. Pairing the baby tee with wide-leg khaki trousers keeps the proportions balanced and highlights the waist.

  Fit card: Found this precious little butterfly baby tee and I'm honestly so obsessed. Fits like an absolute dream and gives major 2000s pop star off-duty. Grab it on my depop for $18 before I change my mind and keep it! 🦋✨

0 model calls this session, 2 served from cache
```

**Empty search**

```
python app.py ask 'designer ballgown size XXS under $5' --trace

[1] search_listings (via MCP)
      in:  dict with keys: description, size, max_price
      out: [] (empty)

  I couldn't find a matching listing. Try changing the item description, size, or maximum price.

0 model calls this session


```

**Empty wardrobe**

```
python app.py ask 'denim jacket under $50' --empty-wardrobe --trace
(running with an empty wardrobe)
[1] search_listings (via MCP)
      in:  dict with keys: description, size, max_price
      out: 7 items: Denim Jacket — Light Wash, Cropped, Vintage Levi's 501 Jeans — Medium Wash, 90s Track Jacket — Navy/White Stripe … +4 more
[2] suggest_outfit
      in:  Denim Jacket — Light Wash, Cropped ($42.0, poshmark)
      out: Here are two practical, easy-to-recreate outfit ideas for your cropped light wash denim jacket:  **1. The 90s …
[3] create_fit_card
      in:  Denim Jacket — Light Wash, Cropped ($42.0, poshmark)
      out: Finally letting go of this cropped light wash denim jacket because I literally never reach for it, but it’s so…

  Found:    Denim Jacket — Light Wash, Cropped — $42.0 on poshmark

  Outfit:   Here are two practical, easy-to-recreate outfit ideas for your cropped light wash denim jacket:

**1. The 90s Streetwear Look**
*   **Top:** A fitted, black ribbed cotton baby tee.
*   **Bottom:** High-waisted, wide-leg cargo trousers in olive green or beige.
*   **Footwear:** Retro white leather sneakers (like Adidas Sambas or Nike Air Force 1s).
*   **Accessories:** A black nylon shoulder bag and chunky silver hoop earrings.
*   *Why it works:* The fitted baby tee balances the volume of the wide-leg cargo pants, while the cropped jacket highlights the high waist.

**2. The Off-Duty Casual Look**
*   **Top:** A striped oversized button-down shirt (blue and white), worn unbuttoned over a plain white crewneck t-shirt.
*   **Bottom:** Straight-leg medium wash blue jeans (creating a cool denim-on-denim contrast).
*   **Footwear:** Canvas low-top sneakers (like Converse All-Stars) or comfortable loafers.
*   **Accessories:** A minimalist leather crossbody bag and baseball cap.
*   *Why it works:* Layering the longer button-down underneath the cropped jacket creates a stylish, high-contrast silhouette.

  Fit card: Finally letting go of this cropped light wash denim jacket because I literally never reach for it, but it’s so good for throwing overbaby tees or layering with button-downs. Grabbed it thrifting a while ago and it's just sitting in my closet. Listed on my Poshmark for $42 if anyone wants to give it a better home!

0 model calls this session, 2 served from cache

```

**Model unavailable**

```
python app.py ask 'black leather boots under $60' --trace
[1] search_listings (via MCP)
      in:  dict with keys: description, size, max_price
      out: 9 items: Platform Mary Janes — Black Patent, Oversized Flannel Shirt — Plaid Red/Black, Leather Belt — Brown, Braided … +6 more
[2] suggest_outfit
      in:  Platform Mary Janes — Black Patent ($55.0, depop)
      out: Here are two ways to style your new platform Mary Janes using pieces from your wardrobe:  **Outfit 1: Casual Y…
[3] create_fit_card
      in:  Platform Mary Janes — Black Patent ($55.0, depop)
      out: Obsessed is an understatement for these chunky patent Mary Janes 😭 Grabbed them thrifted and they’re giving th…

  Found:    Platform Mary Janes — Black Patent — $55.0 on depop

  Outfit:   Here are two ways to style your new platform Mary Janes using pieces from your wardrobe:

**Outfit 1: Casual Y2K Streetwear**
*   **The Look:** Baggy dark-wash jeans + white ribbed tank top + black cropped zip hoodie (open) + platform Mary Janes + black crossbody bag.
*   **Why it works:** The tight white tank contrasts with the baggy denim for a classic 90s/Y2K silhouette, while the cropped black hoodie pulls in the black of the shoes to tie the whole outfit together.

**Outfit 2: High-Contrast Edgy Neutral**
*   **The Look:** Wide-leg khaki trousers + white ribbed tank top + vintage black denim jacket + platform Mary Janes.
*   **Why it works:** The structured khaki trousers tone down the intense goth vibe of the shoes, creating a balanced, cool-girl mix of tailoringand grunge. Adding the black denim jacket mirrors the footwear for a cohesive finish.

  Fit card: Obsessed is an understatement for these chunky patent Mary Janes 😭 Grabbed them thrifted and they’re giving the ultimate 90s/Y2K streetwear vibe, but I just don’t reach for them enough. Asking $55 shipped on Depop so someone can actually wear them!

0 model calls this session, 2 served from cache
```

**On the MCP move:** <!-- what changed in your code, and whether anything
behaved differently afterwards. If the rewire didn't work, say exactly where it
broke — the error text and the last thing that worked. That earns the point in
full. -->

I swapped the direct search_listings(...) call in agent.py::run_agent for mcp_client.call_tool("search_listings", {...}). I first ran python mcp_client.py to confirm that the server registered the tool. It printed “Asking mcp_server.py what it offers…” and listed search_listings with its description and inputs. After the swap, nothing behaved differently. Both example queries gave the same results as the direct call (10 listings for the tee query, [] for the ballgown query), and the empty-results branch still stopped before the fit card. The return value stayed a list of dicts, so the branch didn’t need any changes.

---

## The Improvement

<!-- What you changed, why your diagnosis pointed at it, and the after-run in
     the same table format. One change, measured properly.

     `python run_eval.py --label after` -->

**What I changed:**

**Which failure it was meant to fix:**

### Run Log — After

| Criterion | Target | Try 1 | Try 2 | Try 3 | Try 4 | Try 5 | Verdict |
| --------- | ------ | ----- | ----- | ----- | ----- | ----- | ------- |
| 1.        |        |       |       |       |       |       |         |
| 2.        |        |       |       |       |       |       |         |
| 3.        |        |       |       |       |       |       |         |
| 4.        |        |       |       |       |       |       |         |
| 5.        |        |       |       |       |       |       |         |

**Did it help, and how do I know:**

<!-- If it made things worse, say that. Honestly reported, that earns full
     credit and is more interesting than one that worked. -->

---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped where
     you did. "I ran out of time" is fine if it's true. Pretending nothing is
     left is not. -->

<!-- ═════════════════════════════════════════════════════════════════════

     SUBMISSION CHECKLIST — unit 3

       [ ] criteria.md has five numbered criteria, each with a target
       [ ] Each criterion has a reason underneath it
       [ ] All five unit 3 sections above have real content
       [ ] Tool Inventory: all three tools, inputs WITH TYPES, a specific
           return value, and the empty case
       [ ] Planning Loop names the branch rule and agent.py::run_agent
       [ ] Sample Run: one full query plus the three per-tool tests, as text
       [ ] At least four new commits
       [ ] Repository URL submitted — WRITE IT DOWN, you submit the same one
           next unit

     SUBMISSION CHECKLIST — unit 4

       [ ] mcp_server.py exists with one tool registered
           (or a written record of exactly where the rewire broke)
       [ ] Run Log — Before, five criteria, five tries each
       [ ] Real output pasted underneath, naming file and function
       [ ] A verdict on every criterion
       [ ] A diagnosis for every miss, naming a place AND a mechanism
       [ ] Loop Trace, with the MCP call visible in it
       [ ] All three failure modes triggered and handled
       [ ] One improvement, with Run Log — After in the same format
       [ ] What's Still Broken
       [ ] At least four new commits
       [ ] The SAME repository URL as last unit

     Do not delete and recreate this repository. Your commit history is what
     shows your criteria existed before your results did.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**
