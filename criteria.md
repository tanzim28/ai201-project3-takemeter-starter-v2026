# Acceptance criteria — TakeMeter

Five criteria that say what "working" means for this classifier, written in
unit 5 **before** anything was trained.

**All five are yours this time.** None are given. You've had two projects of
practice.

An acceptance criterion names a number. *"The model is accurate"* is an
opinion. *"Every label has an F1 of at least 0.60 on the held-out set"* is a
criterion.

Under each, write a sentence or two on **why that number**. A reason that says
something about your data or your taxonomy earns credit — *"I picked 0.60 F1
for `reaction` because it's my smallest label and I only have about 50
examples of it"*. A reason that could be attached to any project does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---

## Pick numbers you can defend

Not numbers that sound impressive. Three labels means a coin-flip guesser gets
about 33%, so a target of 0.40 is barely a target. Your number should sit
somewhere you'd honestly call useful.

**Cover at least three of these five areas.** They're here as prompts, not as a
form to fill in — a criterion that fits none of them is fine if it names a
number.

| Area | A question it could answer |
|---|---|
| Overall accuracy | How often does it need to be right to be worth using? |
| Per-label performance | Is one label allowed to be much worse than the others? |
| Balance | How lopsided can your label counts get before it's a problem? |
| Consistency | If someone else labelled the same posts, how often should you agree? |
| Confidence | Should a confident prediction be right more often than an unsure one? |

Two things worth knowing before you pick numbers, because both will affect
whether you hit them:

- **Your smallest label will have the jumpiest score.** If a label has 50
  examples, about 8 land in the test split. An F1 computed on 8 examples moves
  a lot between seeds. A target for that label should be looser than one for
  your biggest label, and saying so is a good reason.
- **Unit 6 tests across three seeds, and the target has to hold across all
  three.** A target of 0.65 against results of 0.71, 0.62, 0.68 is a **miss**.
  Pick with that in mind — it is stricter than it first sounds.

---

## 1.

Overall Macro F1 is at least 0.60 on the held-out test split across each seed run.

**Why this target:**
With four balanced classes (`news_industry`, `critique_opinion`, `community_inquiry`, `setup_nostalgia`), a random guessing baseline yields 0.25 Macro F1. An F1 floor of 0.60 represents a reliable discriminative signal over informal Reddit discussions on a 200-sample fine-tuning set, while remaining achievable across all three evaluation seeds in Unit 6.

---

## 2.

The `critique_opinion` label achieves a held-out F1 score of at least 0.50 across each seed.

**Why this target:**
With 50 examples in the dataset, approximately 7 to 8 land in the 15% test split. `critique_opinion` shares a subtle boundary with `community_inquiry` when users review a game before asking for recommendations, making it prone to high score variance across splits. A target of 0.50 ensures the model does not sacrifice this subtle category while tolerating small sample fluctuation.

---

## 3.

The `news_industry` label achieves a held-out F1 score of at least 0.65 across each seed.

**Why this target:**
`news_industry` posts feature distinct syntactic patterns and vocabulary (e.g., corporate names, dollar amounts, executive quotes, studio announcements). Because its linguistic features are more explicit than player opinion or setup posts, it should reliably establish a higher discriminative floor.

---

## 4.

No single label exceeds 35% or falls below 15% of the total rows in `labels.csv`.

**Why this target:**
The project ceiling requires that no class exceeds 70%. Capping each label between 30 and 70 examples in a 200-row file guarantees balanced priors during cross-entropy loss optimization, preventing the classifier from learning majority-class prediction shortcuts.

---

## 5.

Agreement on the first 20 cold-labeled posts is at least 85% when checked against the written decision rule.

**Why this target:**
The first 20 rows are annotated by hand without tool assistance. An 85% agreement floor confirms that the interrogative-intent rule clearly separates borderline `critique_opinion` vs. `community_inquiry` cases before LLM batch pre-labeling is applied to the remaining 180 rows.
---

<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 6 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath, like this:

         ## 2. Every label performs acceptably

         The model performs well on all labels.

         **Why this target:** ...

         > **Revised in unit 6:** Every label has an F1 of at least 0.60 on
         > the held-out set.
         >
         > **Why revised:** "performs well" gave me nothing to check. I
         > couldn't produce a verdict from it at all.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "Overall accuracy of at least 0.65" → "at least 0.55", because
            0.65 turned out to be optimistic for 200 examples.

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.
     ───────────────────────────────────────────────────────────────────────── -->
