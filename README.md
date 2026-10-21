# TakeMeter

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first** — setup, the
> notebook, the baseline, and what to do when something breaks.
>
> Once `python test.py` passes:
>
> ```bash
> head -5 data/practice_labels.csv     # the shape your labels.csv needs
> ```
>
> Then open `takemeter.ipynb` **in this folder** — in VS Code, or with
> `jupyter notebook` if you prefer. Pick the kernel: the `.venv` inside this
> project. Run section 1, which reports the hardware you'll be training on.
> Everything else waits until you have data.
>
> Nothing to upload, nothing to connect, no accounts and no keys. The notebook
> runs on your machine and writes next to your code.
>
> **The rest of this file is your submission.** Fill it in as you go.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     HOW TO USE THIS FILE

     Unit 5 asks for the first five sections. Unit 6 adds the five below them.

     Everything is pasted as TEXT. No screenshots, no images.

     ⚠️ The confusion matrix especially. The notebook prints one as a markdown
     table, ready to copy. A screenshot of a matrix earns nothing. Paste the
     table.
     ───────────────────────────────────────────────────────────────────────── -->

<!-- ═══════════════════════ UNIT 5 — THE BUILD ═══════════════════════ -->

## What This Does

<!-- Your community, and what your classifier sorts posts into. Three or four
     sentences. -->

This project trains a text classifier named TakeMeter to sort posts from **r/gaming**, an active public community of 2.7M members discussing game design, industry news, hardware, and community culture. Online gaming forums contain a mix of factual reporting, mechanical critique, and conversational inquiries that can be noisy to parse. TakeMeter categorizes incoming posts into four distinct functional buckets (`news_industry`, `critique_opinion`, `community_inquiry`, and `setup_nostalgia`) so readers can filter news and in-depth design discussions from general advice requests and setup threads.

---

## Label Taxonomy

<!-- Each label: a one-sentence definition and two real examples from your
     reading. Then your decision rule for the hardest boundary.

     The decision rule is worth a point on its own and it's the thing most
     people leave out. Every taxonomy has a hardest boundary. Name yours. -->

### `news_industry`

**Definition:** Reports factual developments, company financial actions, studio announcements, engine migrations, or release dates backed by external sources or quotes.

**Example 1:**
>"We're using AI": Xbox business strategist claims he could be fired "because I told you the truth that nobody wants you to know"

**Example 2:**
> "PlayStation founder Ken Kutaragi says discs became "metaphor for 'owning' games", and that upset at their discontinuation is a "mentality issue"

### `critique_opinion`

**Definition:** Makes an evaluative argument about game mechanics, progression pacing, difficulty, or design systems based on playtime experience.

**Example 1:**
> "I do not recommend Silent Hill: Townfall. I really gave the game a chance and for the most part it was cool, but there are just too many amazing games to play and it’s just not worth it unless on discount."

**Example 2:**
> "Witcher 3 is exactly as good as I remember it being from 10 years ago."

### `community_inquiry`

**Definition:** Solicits game recommendations, purchase advice, genre suggestions, or help identifying unknown games and mechanics from community members.
**Example 1:**
> "What unassuming game has surprisingly deep lore?"

**Example 2:**
> "I want to get my wife into gaming but I don’t know how"

### `setup_nostalgia`

**Definition:** Shares personal battlestations, physical hardware collections, retro shop discoveries, or vintage memories to foster shared community bonding.

**Example 1:**
> "Finally upgraded my steam machine… to a steam machine!"
**Example 2:**
> "I found it on my parents house after playing it on release date around 2002. Pure joy 🥹

### The hardest boundary

**Which two labels:** `critique_opinion` vs. `community_inquiry`

**The decision rule I used every time:**
"If the primary intent of the post is to solicit answers, buying advice, or suggestions from the community (signaled by direct questions such as 'Which should I get?', 'Is it worth it?', or 'What games are like this?'), label it `community_inquiry`, even if the body contains extensive critique or scoring of games the poster already played."



---

## The Dataset

<!-- Where you collected from, how you labelled, your counts, and three hard
     cases. -->



**Where the posts came from:**
Verbatim submissions and discussions gathered directly from the public subreddit `r/gaming` during October 2026.

**How I labelled them:** <!-- Cold first? Pre-labelled with AI and corrected?
I labeled the first 20 posts cold by hand with zero AI assistance (marked with `cold` in the `note` column). For the remaining 180 posts, I used an LLM to generate initial candidate labels based on my taxonomy, and then manually inspected and corrected every single row against my decision rule before committing.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| `news_industry` | 50 | 25.0% |
| `critique_opinion` | 50 | 25.0% |
| `community_inquiry` | 50 | 25.0% |
| `setup_nostalgia` | 50 | 25.0% |
| **Total** | 200 | 100% |
**Three hard cases**

<!-- Any post that made you pause: what it was, which two labels it could have
     been, and what you chose. These are worth more than the easy 190. -->

**1.**
> *The post:* "Thinking about Ape Escape… I’m going through my storage and looking at my old games. I spot all the Ape Escapes. Those games rocked. Will there ever be a new Ape Escape? I was so addicted to those games - just loved the weird story and getting new gadgets to find different monkeys on later playthroughs. Alternatively, are there any games to scratch this itch?"
>
> *Could have been:* `setup_nostalgia` vs. `community_inquiry`
>
> *I chose, because:* I chose `community_inquiry`. Although the author opens with retro nostalgia from cleaning out their storage, the actionable conclusion of the post asks the community for alternative games to scratch the itch.

**2.**
> *The post:* "Hot Take: I liked Hellraiser: Revival more than Silent Hill: Townfall and Resident Evil 9. My Horror Game of the Year without a doubt"
>
> *Could have been:* `critique_opinion` vs. `community_inquiry`
>
> *I chose, because:* I chose `critique_opinion`. The author is declaring an evaluative stance and GOTY ranking based on gameplay preference without asking the community for opinions or recommendations.

**3.**
> *The post:* "PlayStation founder Ken Kutaragi says discs became 'metaphor for owning games', and that upset at their discontinuation is a 'mentality issue'"
>
> *Could have been:* `news_industry` vs. `critique_opinion`
>
> *I chose, because:* I chose `news_industry`. Even though the quote expresses a provocative personal opinion about physical media, the poster is neutrally reporting an interview quote from an industry executive.

---

## The Training Run

<!-- Your starting model, your settings, and anything you changed and why. -->

**Base model:** `distilbert-base-uncased`

**Settings:** <!-- epochs, learning rate, batch size, seed -->
- Epochs: 3
- Learning Rate: 2e-5
- Batch Size: 16
- Max Sequence Length: 128
- Seed: 42
- Hardware Device: `mps` (Apple Silicon GPU)

**Anything I changed from the defaults, and why:** None; kept default hyperparameters (3 epochs, lr=2e-5, batch=16, seq_len=128) to establish a clean baseline run for Unit 5 before hyperparameter tuning in Unit 6.

**Split sizes:** <!-- train / val / test, and per-label counts in the test
- Train: 140 (70%)
- Val: 30 (15%)
- Test: 30 (15%)
- Per-label counts in test split: `news_industry`: ~7–8, `critique_opinion`: ~7–8, `community_inquiry`: ~7–8, `setup_nostalgia`: ~7–8. With 30 test items across 4 balanced labels, each class has approximately 7 to 8 examples in the test split.



---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->

**Moment 1**
- *What I asked for:* Assistance in establishing a decision rule between posts that contain both extensive game critique/reviews and a community question.
- *What came back:* A suggestion to check post length and classify anything over two paragraphs as `critique_opinion`.
- *What I changed:* Rejected the word-count rule because long Reddit posts often write extensive backstories before asking for game recommendations. Instead, I established an intent-based decision rule focusing on actionable interrogatives ('Is that worth it?', 'Which should I get?').

**Moment 2**
- *What I asked for:* How to classify developer and executive interview quotes that express strong personal opinions on gaming trends.
- *What came back:* An initial suggestion to label them `critique_opinion` due to the subjective language used in the quote.
- *What I changed:* Overrode this advice and classified developer/executive quotes as `news_industry` because the poster's action is reporting industry news rather than sharing player analysis.

**Pre-labelling disclosure:**
The first 20 examples were labeled completely cold by hand with zero AI assistance (marked with `cold` in the `note` column). For the remaining 180 examples, an LLM was used to generate initial candidate labels based on my 4-label taxonomy, and I manually reviewed, validated, and corrected every single row against my decision rule before saving to `labels.csv`.
<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy |  |  |  |
| Macro F1 |  |  |  |
| F1 — `label_one` |  |  |  |
| F1 — `label_two` |  |  |  |

**What I predicted before I looked:**
<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->

**What the gap actually means:**
<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->



---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

| true \ predicted |  |  |  |
|---|---|---|---|
| **** |  |  |  |
| **** |  |  |  |
| **** |  |  |  |

**My biggest off-diagonal number, and what it means:**
<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->



---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 |  |  |  |  |
| 2 |  |  |  |  |
| 3 |  |  |  |  |
| 4 |  |  |  |  |
| 5 |  |  |  |  |

**Diagnoses**

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->



---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** ___ / 30 = ___%

<!-- Nobody grades this number. A 60% who argues every disagreement from the
     stated rules beats a 95% who wrote "staff was right" nine times. Several
     of the 30 were chosen because they're genuinely ambiguous — you should be
     winning some of these. -->

**Disagreements**

<!-- Three lines each: the post, both labels, and who you think is right and
     why — grounded in the staff definitions you were both applying.

     Then sort each into one of three piles:
       (a) the rule covered it and I applied it loosely → a consistency problem
       (b) the rule genuinely doesn't say               → a gap in the definitions
       (c) the rule is ambiguous here and my reading is defensible → argue it.
           This is a legitimate win.

     Pile (a) is the one that matters most for your diagnosis: if you applied a
     written rule two different ways on 30 posts, that is direct evidence about
     what you did across your own 200. -->

**1.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**2.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**What the pattern in my disagreements tells me:**



---

## The Improvement

**What I changed:**

**Which diagnosis pointed at it:**

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

**Did it help, and how do I know:**

<!-- If it didn't, say so. Relabelling that didn't help is a genuinely
     interesting result and earns full credit. -->



---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped. -->



**The gap between what I meant my labels to capture and what the model
learned:**
<!-- Two sentences. Your confusion matrix is the evidence. -->



<!-- ═════════════════════════════════════════════════════════════════════

     SUBMISSION CHECKLIST — unit 5

       [ ] criteria.md has five numbered criteria, each naming a NUMBER
       [ ] Each has a reason underneath tied to your data or taxonomy
       [ ] labels.csv: at least 150 rows, text/label/note, ONE file not split
       [ ] No label above 70%
       [ ] All five unit 5 sections have real content
       [ ] Label Taxonomy includes the decision rule for your hardest boundary
       [ ] The Dataset includes three hard cases
       [ ] results.json and test_split.csv committed (the notebook does this)
       [ ] At least four new commits
       [ ] Repository URL submitted — WRITE IT DOWN

     SUBMISSION CHECKLIST — unit 6

       [ ] Baseline vs. Trained table, with your prediction written beforehand
       [ ] Run Log — Before, five criteria across three seeds
       [ ] Confusion matrix TYPED AS A MARKDOWN TABLE
       [ ] A verdict on every criterion
       [ ] A diagnosis for every miss, using the agreement report as evidence
       [ ] Agreement Report with every disagreement adjudicated
       [ ] One improvement, with Run Log — After
       [ ] What's Still Broken
       [ ] results_three_seeds_before.json, results_three_seeds_after.json,
           baseline_results.json and
           agreement_results.json committed
       [ ] At least four new commits
       [ ] The SAME repository URL as last unit

     Do not delete and recreate this repository.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**
