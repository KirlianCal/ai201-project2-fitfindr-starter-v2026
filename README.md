# FitFindr

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first** — setup, every command, and what to do when something breaks.
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

FitFindr helps a user find clothing listings and turn a selected item into an outfit idea and a short fit-card caption in the style of a scoial media post. The agent searches the listings using the user's description, size, and maximum price, selects the best matching listing, passes that item to the outfit tool with the user's wardrobe, and then creates a fit card from the outfit suggestion. If no listings match, the agent stops and explains what the user can change instead of continuing with empty results.

<!-- Three or four sentences: what a user asks for, and what they get back. -->

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

* **What it does: Searches the listings data for clothing that matches a description and, when provided, filters by size and maximum price.**
* **Inputs: description (str), size (str | None), max_price (float | None)**
* **Returns: A list of matching listing dictionaries, best match first. Each listing contains id, title, description, category, style_tags, size, condition, price, colors, brand, and platform**
* **When it has nothing: Returns an empty list**

**Size matching rule:** Size matching is case-insensitive. A requested size matches the same size or a combined size containing that size, such as `M` matching `M` and `S/M`, but `L` does not match `XL`.

**Price rule:** `max_price` is inclusive, so a listing priced exactly at the maximum is allowed.

### `suggest_outfit`

* **What it does: Uses a selected listing and the user's wardrobe to suggest one or two outfits.**
* **Inputs: new_item (dict), wardrobe (dict with an items key holding a list of items)**
* **Returns: A non-empty str containing outfit suggestions.**
* **When it has nothing: returns general styling advice for the new item**

### `create_fit_card`

* **What it does: Creates a short social-media-style caption for the selected item and suggested outfit.**
* **Inputs: outfit (str), new_item (dict)**
* **Returns: A str containing a two-to-four sentence fit-card caption that mentions the item, price, platform, and overall vibe**
* **When it has nothing: If outfit is empty or only whitespace, returns a descriptive message**

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

**Branch rule: If search_listings returns an empty list, the agent stores a message in `session["error"]` explaining what the user can change and stops without calling `suggest_outfit` or `create_fit_card`. Otherwise, it selects the first matching listing, stores it in `session["selected_item"]`, and passes it to `suggest_outfit`. The outfit is stored in `session["outfit_suggestion"]` and then passed with the selected item to `create_fit_card`.**

**Where it lives:** `agent.py::run_agent`

**How the query is parsed:**

Regex is used to extract the size and maximum price from the user's query. The remaining text is used as the item description.

<!-- regex, string splitting, or asking the model — say which -->

**What moves through the session: User query → session["query"], parsed description/size/max price → session["parsed"], search results from search_listings → session["search_results"], selected listing → session["selected_item"], outfit returned by suggest_outfit → session["outfit_suggestion"], fit-card text from create_fit_card → session["fit_card"], and an error or stop message → session["error"] when the search returns no results.**

<!-- which fields, in what order -->

---

## Sample Run

<!-- Two things go here.

     1. One FULL query and its output, pasted as text.

     2. Your three per-tool terminal tests — the command and what it printed. -->

**One full query**

```text
$ python app.py ask 'vintage graphic tee under $30'

Found:    Y2K Baby Tee — Butterfly Print — $18.0 on depop

  Outfit:   Here are two practical outfit suggestions combining the Y2K butterfly baby tee with your existing wardrobe:

### Outfit 1: Streetwear Y2K Contrast

This look balances the fitted, feminine Y2K aesthetic of the baby tee with relaxed, streetwear-inspired bottoms and classic outerwear.

* **Top:** Y2K Baby Tee — Butterfly Print

* **Bottoms:** Baggy straight-leg jeans (dark blue/indigo)

* **Outerwear:** Vintage black denim jacket

* **Shoes:** Chunky white sneakers

* **Accessories:** Black crossbody bag

**Why it works:** Pairing the cropped, fitted baby tee with low-slung or baggy straight-leg jeans hits the classic early 2000s proportion play. Layering the vintage black denim jacket on top keeps the color palette grounded, while the chunky white sneakers tie in the white base of the tee.

---

### Outfit 2: Casual Earth-Tone Mix

This outfit tones down the graphic tee by mixing it with minimal, neutral pieces for an easy, everyday look.

* **Top:** Y2K Baby Tee — Butterfly Print

* **Bottoms:** Wide-leg khaki trousers

* **Accessories:** Brown leather belt + Black crossbody bag

* **Shoes:** Chunky white sneakers

**Why it works:** The khaki wide-leg trousers bring a minimal, grounded feel that offsets the playful pink and purple butterfly graphic. Tucking the baby tee in (or letting the cropped hem sit right at the waistband) alongside the brown leather belt adds subtle definition to the waist, finished cleanly with the white sneakers.

  Fit card: Obsessed with this Y2K butterfly baby tee! It’s giving total early 2000s streetwear vibes and looks so cute paired with baggy denim. Grab it on my Depop for just $18 before it’s gone! 🦋✨

0 model calls this session, 2 served from cache

(.venv)

GWCal@laptop MINGW64 ~/OneDrive/Desktop/ai201-project2-fitfindr-starter-v2026 (main)

$ python app.py ask 'designer ballgown size XXS under $5'

  No matching listings were found. Try a broader description, a different size, or a higher maximum price.

0 model calls this session
```

**The three tools, tested one at a time**

```text
$ python -c "from tools import search_listings; print(search_listings('graphic tee', max_price=30))"

[{'id': 'lst_002', 'title': 'Y2K Baby Tee — Butterfly Print', 'description': 'Super cute early 2000s baby tee with butterfly graphic. Fitted crop length. Tag says medium but fits like a small.', 'category': 'tops', 'style_tags': ['y2k', 'vintage', 'graphic tee', 'cottagecore'], 'size': 'S/M', 'condition': 'excellent', 'price': 18.0, 'colors': ['white', 'pink', 'purple'], 'brand': None, 'platform': 'depop'}, {'id': 'lst_006', 'title': 'Graphic Tee — 2003 Tour Bootleg Style', 'description': 'Vintage-style bootleg tee with faded graphic. Slightly boxy fit. 100% cotton, soft and worn-in.', 'category': 'tops', 'style_tags': ['graphic tee', 'vintage', 'grunge', 'streetwear', 'band tee'], 'size': 'L', 'condition': 'good', 'price': 24.0, 'colors': ['black'], 'brand': None, 'platform': 'depop'}, {'id': 'lst_017', 'title': 'Mesh Long-Sleeve Top — Black', 'description': 'Sheer black mesh long-sleeve. Great for layering under a graphic tee or over a bralette. Stretchy material, fits true to size.', 'category': 'tops', 'style_tags': ['y2k', 'grunge', 'goth', 'layering'], 'size': 'S/M', 'condition': 'excellent', 'price': 15.0, 'colors': ['black'], 'brand': None, 'platform': 'depop'}, {'id': 'lst_033', 'title': 'Vintage Band Tee — Faded Grey', 'description': 'Faded grey band-style tee with distressed graphic. Crew neck. Fits boxy. Well-loved but no holes or major damage.', 'category': 'tops', 'style_tags': ['vintage', 'grunge', 'band tee', 'graphic tee', 'streetwear'], 'size': 'L', 'condition': 'fair', 'price': 19.0, 'colors': ['grey', 'charcoal'], 'brand': None, 'platform': 'depop'}, {'id': 'lst_011', 'title': 'Low-Rise Cargo Pants — Khaki', 'description': 'Y2K era low-rise cargo pants. Lots of pockets. Khaki color, slightly distressed at the hems. Great for layering with a long tee.', 'category': 'bottoms', 'style_tags': ['y2k', 'cargo', '2000s', 'streetwear'], 'size': 'W29', 'condition': 'fair', 'price': 27.0, 'colors': ['khaki', 'tan'], 'brand': None, 'platform': 'poshmark'}, {'id': 'lst_015', 'title': 'Vintage Graphic Hoodie — Faded Black', 'description': 'Faded black pullover hoodie with barely-visible vintage graphic on the chest. Cozy interior. Some pilling but adds to the worn-in look.', 'category': 'tops', 'style_tags': ['vintage', 'grunge', 'graphic', 'streetwear'], 'size': 'L', 'condition': 'fair', 'price': 26.0, 'colors': ['black', 'charcoal'], 'brand': None, 'platform': 'depop'}]
```

```text
$ python -c "from tools import suggest_outfit; from utils.data_loader import get_example_wardrobe, load_listings; print(suggest_outfit(load_listings()[0], get_example_wardrobe()))"

Here are two practical outfit suggestions using the Vintage Levi's 501 Jeans and your existing wardrobe:

### Outfit 1: Classic Casual Streetwear

*This look leans into the vintage, everyday streetwear vibe of the 501s by pairing fitted basics with cozy, oversized layers.*

* **Bottoms:** Vintage Levi's 501 Jeans (Medium Wash)

* **Tops:** White ribbed tank top + Oversized grey crewneck sweatshirt (worn layered or over the shoulders)

* **Shoes:** Chunky white sneakers

* **Accessories:** Black crossbody bag

### Outfit 2: Edgy Contrast

*This combination pairs the faded medium-wash denim with darker, structured pieces for a classic grunge-leaning silhouette.*

* **Bottoms:** Vintage Levi's 501 Jeans (Medium Wash)

* **Tops:** Black cropped zip hoodie

* **Outerwear:** Vintage black denim jacket (double denim look)

* **Shoes:** Black combat boots

* **Accessories:** Brown leather belt + Black crossbody bag
```

```text
$ python -c "from tools import create_fit_card; from utils.data_loader import load_listings; print(create_fit_card('jeans and white sneakers', load_listings()[0]))"

Nothing beats a classic pair of vintage Levi’s 501s, especially styled with crisp white sneakers for that effortless everyday look. Grabbed these on Depop for just $38 and I’m obsessed with the wash. Perfect casual streetwear vibe for running errands.
```

---

### Empty-case checks

```text
$ python -c "from tools import search_listings; print(search_listings('designer ballgown', size='XXS', max_price=5))"

[]
```

```text
$ python -c "from tools import suggest_outfit; from utils.data_loader import load_listings, get_empty_wardrobe; print(suggest_outfit(load_listings()[0], get_empty_wardrobe()))"

Here are two practical, versatile outfit ideas for these vintage Levi's 501 jeans, built using staple wardrobe categories.
```

```text
$ python -c "from tools import create_fit_card; from utils.data_loader import load_listings; print(create_fit_card('', load_listings()[0]))"

Fit idea for Vintage Levi's 501 Jeans — Medium Wash: Classic 501s in a perfect medium wash. Some light fading at the knees which adds to the vintage look. No rips or stains..
```

---

### Fit-card variation check

The first three runs produced the same caption because caching was enabled. config.py showed TEMPERATURE = 0.9 and CACHE_ENABLED = True. Running the same input three times with AI201_CACHE=0 produced different captions.

---

## How I Used AI

<!-- Two specific moments. What you asked, what came back, what you changed.

     "I used Claude to help me code" is not enough.

     "I gave Claude my search_listings spec. It returned None on no match
      instead of an empty list, so I changed it" is the level we want. -->

**Moment 1**

* *What I asked for:I asked AI why my fit-card output was repeating when I ran the same test multiple times.*
* *What came back: It pointed out that caching was enabled in config.py, so the same model response could be returned instead of generating a new one.*
* *What I changed: I reran the fit-card tests with AI201_CACHE=0 so I could check whether the model produced different captions. The outputs were different, which helped me confirm the cache was causing the repeated captions.*

**Moment 2**

* *What I asked for: I asked AI how to extract the size and maximum price from a natural-language query.*
* *What came back: It suggested using regular expressions to find phrases such as size M and under $30*
* *What I changed: I added _parse_query in agent.py to remove those parts from the description and store the parsed size and price in the session.*

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

| Criterion | Target | Try 1 | Try 2 | Try 3 | Try 4 | Try 5 | Verdict |
| --------- | ------ | ----- | ----- | ----- | ----- | ----- | ------- |
| 1.        |        |       |       |       |       |       |         |
| 2.        |        |       |       |       |       |       |         |
| 3.        |        |       |       |       |       |       |         |
| 4.        |        |       |       |       |       |       |         |
| 5.        |        |       |       |       |       |       |         |

**Real output from one try**, pasted as text, naming the file and function that produced it:

```text
```

---

## Verdicts and Diagnoses

<!-- MET or MISSED per criterion against LAST unit's target, plus a sentence on
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

| # | Criterion | Target | Verdict | How I decided |
| - | --------- | ------ | ------- | ------------- |
| 1 |           |        |         |               |
| 2 |           |        |         |               |
| 3 |           |        |         |               |
| 4 |           |        |         |               |
| 5 |           |        |         |               |

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

```text
```

**Empty search**

```text
```

**On the MCP move:** <!-- what changed in your code, and whether anything
behaved differently afterwards. If the rewire didn't work, say exactly where it
broke — the error text and the last thing that worked. That earns the point in
full. -->

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
