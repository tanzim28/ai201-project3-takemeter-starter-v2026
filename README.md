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

TakeMeter is a text classifier built to categorize posts from the [r/gaming](https://www.reddit.com/r/gaming/) community into four operational categories: `news_industry`, `critique_opinion`, `community_inquiry`, and `setup_nostalgia`. It distinguishes factual reporting and corporate developments from player evaluations, troubleshooting/recommendation inquiries, and hardware/nostalgia retrospectives.

---

## Label Taxonomy

<!-- Each label: a one-sentence definition and two real examples from your
     reading. Then your decision rule for the hardest boundary.

     The decision rule is worth a point on its own and it's the thing most
     people leave out. Every taxonomy has a hardest boundary. Name yours. -->

### `news_industry`
*Definition:* Posts reporting factual studio announcements, release dates, executive remarks, acquisitions, trailer drops, or industry financial/legal news.
*Example 1:* "PlayStation founder Ken Kutaragi says discs became 'metaphor for owning games', and that upset at their discontinuation is a 'mentality issue'"
*Example 2:* "Blizzard warns World of Warcraft: Forever players it will 'very aggressively' punish anyone who buys gold with real money"

### `critique_opinion`
*Definition:* Posts offering subjective evaluations, reviews, design praise, or critiques of gameplay mechanics, pacing, or narrative choices.
*Example 1:* "Hot Take: I liked Hellraiser: Revival more than Silent Hill: Townfall and Resident Evil 9. My Horror Game of the Year without a doubt"
*Example 2:* "Why do developers insist on adding unskippable slow-walking dialogue sections? It ruins replayability when you just want to get back into the actual gameplay loops."

### `community_inquiry`
*Definition:* Posts asking for game recommendations, technical identification, gameplay assistance, or solicitations of player experiences.
*Example 1:* "What is the terminology for when you can fire a shot as rapidly as you can press the button?"
*Example 2:* "Please help me identify this game: I remember watching a trailer of an open world fantasy game within the last year-ish, and you played as a cat-person, you could do magic, and you were part of a clan."

### `setup_nostalgia`
*Definition:* Posts showcasing physical hardware, retro collections, vintage media, battlestations, or reflective retrospectives on classic hardware.
*Example 1:* "Antique shop find today. Local shop is selling a GBA Special edition in box. They're asking $189 and it's been sitting for over a year."
*Example 2:* "Found my old binder of Nintendo Power magazines from 1996 through 2001 while helping clean out my childhood attic"

### The hardest boundary

**Which two labels:** `critique_opinion` vs. `community_inquiry`

**The decision rule I used every time:**
If a post contains a personal evaluation or review but concludes by prompting the community with an open-ended question or request for advice/recommendations (e.g., "What did you think?" or "Has anyone else felt this?"), label it `community_inquiry`. Label as `critique_opinion` only when the primary intent is delivering an evaluation or take.


---

## The Dataset

<!-- Where you collected from, how you labelled, your counts, and three hard
     cases. -->



**Where the posts came from:**
200 public organic user submissions gathered directly from [r/gaming](https://www.reddit.com/r/gaming/).

**How I labelled them:** <!-- Cold first? Pre-labelled with AI and corrected?
The first 20 rows were labeled cold by hand without tool assistance. The remaining 180 rows were pre-labeled in batches against the written taxonomy definitions and individually checked and corrected.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| `news_industry` | 75 | 37.5% |
| `community_inquiry` | 55 | 27.5% |
| `setup_nostalgia` | 36 | 18.0% |
| `critique_opinion` | 34 | 17.0% |
| **Total** | **200** | **100%** |

**Three hard cases**

<!-- Any post that made you pause: what it was, which two labels it could have
     been, and what you chose. These are worth more than the easy 190. -->

**1.**
>*The post:* "Late to the party, but just beat RE Village... Capcom has been on fire the last few years, and Village holds to that standard. Visuals are gorgeous, areas are varied. What did you folks think about it?"
>
>*Could have been:* `critique_opinion` or `community_inquiry`
>
>*I chose, because:* `community_inquiry`. Under the boundary rule, the prompt ends with a direct question asking the community for their opinions, making engagement the primary intent.

**2.**
> *The post:* "Halo 2 (2004) Elite. Looking back at character model fidelity 20+ years ago."
>
> *Could have been:* `critique_opinion` or `setup_nostalgia`
>
> *I chose, because:* `setup_nostalgia`. The post reflects retro appreciation of legacy 2004 tech rather than offering an argumentative critique of gameplay mechanics.

**3.**
> *The post:* "A new report, which interviewed 101 leaders from studios across North America and Europe, found that 94% said their studios use AI at some stage in development..."
>
> *Could have been:* `critique_opinion` or `news_industry`
>
> *I chose, because:* `news_industry`. It cites structured survey findings and quotes industry executives rather than offering personal commentary.
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

**Anything I changed from the defaults, and why:** Maintained defaults to establish the required baseline for the 4-label taxonomy.

**Split sizes:** <!-- train / val / test, and per-label counts in the test
**Split sizes:**
- Train: 139 (news_industry: 52, critique_opinion: 24, community_inquiry: 38, setup_nostalgia: 25)
- Val: 31 (news_industry: 12, critique_opinion: 5, community_inquiry: 9, setup_nostalgia: 5)
- Test: 30 (news_industry: 11, critique_opinion: 5, community_inquiry: 8, setup_nostalgia: 6)



---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->

**Moment 1**
- *What I asked for:* Suggested taxonomy boundary rules to distinguish review posts ending in questions from standalone opinion takes.
- *What came back:* A proposed rule prioritizing interrogative syntax over review body text.
- *What I changed:* Narrowed the definition so rhetorical questions within essays remain `critique_opinion`, reserving `community_inquiry` for genuine calls for answers or recommendations.

**Moment 2**
- *What I asked for:* Formatting and pre-labeling scraped post titles into CSV format matching the project schema.
- *What came back:* Structured CSV rows categorized into the 4 target labels.
- *What I changed:* Manually adjusted several borderline retro review posts that had been misassigned to `setup_nostalgia` instead of `critique_opinion`.

**Pre-labelling disclosure** Rows 21 through 200 in `labels.csv` were pre-labeled with an LLM in batches against the project taxonomy and individually reviewed and verified before training.
<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy | 0.333 | 0.633 | +0.300 |
| Macro F1 | 0.302 | 0.385 | +0.083 |
| F1 — `community_inquiry` | 0.222 | 0.696 | +0.473 |
| F1 — `critique_opinion` | 0.370 | 0.000 | -0.370 |
| F1 — `news_industry` | 0.000 | 0.846 | +0.846 |
| F1 — `setup_nostalgia` | 0.615 | 0.000 | -0.615 |



**What I predicted before I looked:**
<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->
     The fine-tuned DistilBERT model will beat the zero-shot BART baseline on news_industry and community_inquiry because subreddit-specific conventions (like questions framed around user builds or studio leaks) are better learned through in-domain post structure than generic zero-shot prompts.

**What the gap actually means:**
<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->
Fine-tuning delivered a massive +0.300 gain in overall accuracy (0.633 vs. 0.333), proving that domain-specific training on r/gaming posts taught the model conventions that zero-shot prompting completely missed. The gap is heavily split by class:
- On `news_industry`, zero-shot BART collapsed completely (F1 0.000) because industry posts often quote headlines or developer quotes without explicit journalistic marker words, while DistilBERT mastered them (F1 0.846).
- On `community_inquiry`, fine-tuning gained +0.473 (0.696 vs. 0.222) by learning common subreddit inquiry structures (e.g., asking for recommendations within a setup description).
- Conversely, the trained model collapsed to 0.000 F1 on both minority classes (`critique_opinion` and `setup_nostalgia`). Because only 5 and 6 examples of these classes landed in the Seed 42 test split, DistilBERT favored the dominant classes, whereas zero-shot BART relied on broad lexical cues to achieve 0.615 F1 on `setup_nostalgia`. The overall gain is real, but fragile on minority labels under a single split.


---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->
Device: `mps` (Apple Silicon GPU)

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. Overall accuracy | ≥ 0.65 | 0.633 | 0.600 | 0.633 | MISSED |
| 2. Macro F1 | ≥ 0.50 | 0.385 | 0.372 | 0.387 | MISSED |
| 3. Beats baseline on accuracy | yes | yes (0.633 vs 0.333) | yes (0.600 vs 0.333) | yes (0.633 vs 0.333) | MET |
| 4. F1 on `news_industry` | ≥ 0.70 | 0.846 | 0.710 | 0.786 | MET |
| 5. Every label F1 | ≥ 0.40 | 0.000 (critique/setup) | 0.000 (critique/setup) | 0.000 (critique/setup) | MISSED |

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

| true \ predicted | news_industry | critique_opinion | community_inquiry | setup_nostalgia |
|---|---|---|---|---|
| **news_industry** | 11 | 0 | 0 | 0 |
| **critique_opinion** | 2 | 0 | 3 | 0 |
| **community_inquiry** | 0 | 0 | 8 | 0 |
| **setup_nostalgia** | 2 | 0 | 4 | 0 |

**My biggest off-diagonal number, and what it means:**
<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->
The biggest off-diagonal confusion is 4 real `setup_nostalgia` posts called `community_inquiry`, with 0 going the other direction. This represents a complete directional collapse: the model never predicted `setup_nostalgia` or `critique_opinion` across the entire test set. Because `community_inquiry` and `news_industry` dominate the dataset (65%), and posts showing hardware or retro setups frequently mention seeking advice, parts, or opinions, the model learned a strong majority shortcut that absorbs minority classes.


---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 | Overall accuracy | ≥ 0.65 | MISSED | Seeds produced 0.633, 0.600, and 0.633. The target must hold across all three seeds, not turn up sometimes. Because no seed met or exceeded 0.65, this criterion is missed. |
| 2 | Macro F1 | ≥ 0.50 | MISSED | Seeds produced 0.385, 0.372, and 0.387. Because the model completely failed on the two minority classes, the macro-averaged F1 remained stuck in the high 0.30s. |
| 3 | Beats baseline on accuracy | yes | MET | The zero-shot baseline scored 0.333. The fine-tuned model scored 0.633, 0.600, and 0.633 across the three seeds, outperforming the baseline by 26.7 to 30.0 percentage points on every split. |
| 4 | F1 on `news_industry` | ≥ 0.70 | MET | Seeds scored 0.846, 0.710, and 0.786. All three runs exceeded the 0.70 threshold on this dominant class. |
| 5 | Every label F1 | ≥ 0.40 | MISSED | `critique_opinion` and `setup_nostalgia` both scored 0.000 across all three seeds (Seed 42: 0.000 / 0.000; Seed 7: 0.000 / 0.000; Seed 2024: 0.000 / 0.000). |

**Diagnoses**

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->
* **Criterion 1 (Overall Accuracy) & Criterion 2 (Macro F1):**
  * *Cause:* Severe class imbalance combined with insufficient training epochs.
  * *Evidence:* `news_industry` (75 rows) and `community_inquiry` (55 rows) account for 65% of the 200 training examples. With only 3 epochs at default learning rate, DistilBERT rapidly minimized cross-entropy loss by predicting only the dominant classes. In the confusion matrix, not a single test post was predicted as `critique_opinion` or `setup_nostalgia` across any seed, directly capping overall accuracy at ~0.63 and pulling Macro F1 down to ~0.38.

* **Criterion 5 (Every Label F1 ≥ 0.40):**
  * *Cause:* A combination of too few examples for minority classes and inconsistent labeling boundaries between `setup_nostalgia` and `community_inquiry`.
  * *Evidence:* The confusion matrix reveals that 4 out of 6 true `setup_nostalgia` posts were misclassified as `community_inquiry`, while 0 went the other way. This directional asymmetry was corroborated by the agreement report, where 3 out of 5 disagreements were Pile (a) errors—applying rules loosely when posts combined hardware descriptions with user questions. When a post contains both retro gear and a question (e.g. asking for setup feedback), the model latches onto the dominant question syntax of `community_inquiry` because the boundary was applied inconsistently in the 200 training rows.


---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** 25 / 30 = 83%

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
> *The post:* "Three of us ran the same session on the same day, different shoes, within 10 seconds of each other over 5 miles. I'm not saying shoes do nothing, but whatever they do was swallowed by who had coffee that morning."
>
> *Staff said / I said:* Staff said `analysis` / I said `hot_take`
>
> *My call, and why:* **My call holds up (I am right).** While the post cites numbers (three runners, 10 seconds, 5 miles), the conclusion that whatever shoes do was "swallowed by who had coffee" is an unmeasured, dismissive quip rather than a conclusion derived from data. Under Rule 1, a fact must carry the argument; here, the caffeine claim is decorative sarcasm, making this a hot take.
>
> *Which pile:* **(c)** The rule is ambiguous here and my reading is defensible — a legitimate win.

**2.**
> *The post:* "Times are down about 3% since the shoe rules changed, so obviously the shoes are the whole story and the sport has a problem."
>
> *Staff said / I said:* Staff said `hot_take` / I said `analysis`
>
> *My call, and why:* **Staff is right.** I saw the percentage stat "3%" and immediately treated it as evidence. However, Rule 1 explicitly cautions that if a number is mere decoration on an unargued conclusion, it remains a hot take. The author uses "so obviously" to jump to an extreme claim without demonstrating causation. I applied Rule 1 loosely by reflexively tagging any number as analysis.
>
> *Which pile:* **(a)** The rule covered it and I applied it loosely.

**3.**
> *The post:* "Every injury thread on this forum is the same injury and the same three replies."
>
> *Staff said / I said:* Staff said `hot_take` / I said `analysis`
>
> *My call, and why:* **Staff is right.** The post mimics observational data analysis, but supplies no actual count, thread links, or verifiable tracking. It is a sweeping, cynical generalization about forum behavior. I mistook an empirical-sounding claim for an actual fact-based argument.
>
> *Which pile:* **(a)** The rule covered it and I applied it loosely.

**4.**
> *The post:* "Third DNF this year. I don't really want to talk about it, just wanted to put it somewhere."
>
> *Staff said / I said:* Staff said `reaction` / I said `analysis`
>
> *My call, and why:* **Staff is right.** "Third this year" is a verifiable integer, but Rule 3 states that recency does not outrank evidence, and Rule 2 clarifies that an unargued post expressing the writer's immediate emotional state ("don't really want to talk about it") is a reaction. The integer does zero argumentative work.
>
> *Which pile:* **(a)** The rule covered it and I applied it loosely.

**5.**
> *The post:* "Just realised the shoes I set every PB in this year are the ones I bought by accident in the wrong size."
>
> *Staff said / I said:* Staff said `reaction` / I said `hot_take`
>
> *My call, and why:* **Taxonomy gap.** The post functions as an ironic critique of running gear dogma, fitting the spirit of a hot take, yet it is framed as a personal epiphany regarding the runner's own shoes. The written rules provide no instruction on whether ironic personal realizations belong under subjective claims (`hot_take`) or immediate personal experiences (`reaction`).
>
> *Which pile:* **(b)** The rule genuinely doesn't say — a gap in the definitions.

**What the pattern in my disagreements tells me:**
Three of my five disagreements (60%) landed in Pile (a), revealing a consistent habit of over-crediting surface-level lexical features—specifically treating any integer, percentage, or observational phrase as an argumentative fact rather than verifying whether it carried the weight of the claim. In my own 200 posts, this loose standard likely led me to label posts containing superficial hardware mentions or casual queries inconsistently, feeding ambiguous training signal to the model and encouraging it to collapse into dominant classes.



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
