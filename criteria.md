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

## 1. Overall accuracy is at least 0.65

Accuracy on the held-out test split is at least 0.65, on each of the three
seeds.

**Why this target:** With three labels a random guesser gets about 33%, and
always guessing the biggest label (probably `reaction`) could get 40–50% on
`c/games`. 0.65 is clearly above both, and still reachable with only about 140
training examples and a fuzzy `analysis` / `hot_take` boundary.



---

## 2. Every label has an F1 of at least 0.55

Each of `analysis`, `hot_take` and `reaction` has an F1 of at least 0.55 on the
held-out test split, on each of the three seeds.

**Why this target:** It's lower than the accuracy target on purpose. `analysis`
will probably be my thinnest label, since few `c/games` comments cite anything
checkable, so only a handful will land in a test split of about 30 posts and
one wrong post moves its F1 a lot. A floor of 0.55 still stops the model from
ignoring a label entirely.



---

## 3. No label is above 60% or below 15% of labels.csv

In `labels.csv`, each label makes up between 15% and 60% of the rows.

**Why this target:** The assignment's cap is 70%, but I expect `reaction` to
dominate this community, so I set a stricter 60%. The 15% floor is there
because a label with under about 30 of 200 examples puts fewer than 5 in the
test split, which makes its F1 meaningless.



---

## 4. I agree with my own earlier labels on at least 14 of 20 posts

I re-label 20 randomly chosen posts from `labels.csv` at least a day later,
using only the written definitions in `label_definitions.txt` and not looking
at my first labels. At least 14 of 20 (70%) must match.

**Why this target:** The model can only learn a boundary I apply consistently.
My `analysis` vs `hot_take` rule depends on whether a detail is what the claim
"rests on," which is a judgment call, so I expect some drift. 70% is lenient
enough to allow for that, and low enough agreement would mean my labels, not
the model, are the problem. This is only a check against myself, not a second
person.



---

## 5. Confident predictions are right at least 80% of the time

Among held-out test predictions where the model's top-label probability is at
least 0.80, at least 80% are correct, with at least 10 such predictions on each
seed.

**Why this target:** A confidence score is only useful if the confident
predictions are more reliable than the unsure ones. 0.80 sits above my
accuracy target of 0.65, so the model has to show that its confidence means
something. I require at least 10 predictions because a test split of about 30
posts could leave only a few confident ones, and 80% of 4 posts isn't a result.



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
