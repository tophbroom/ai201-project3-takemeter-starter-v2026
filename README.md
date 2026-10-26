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

TakeMeter sorts comments from `c/games` on lemmy.world, a public gaming
community with no login needed to read it. Each comment gets one of three
labels: `analysis`, `hot_take`, or `reaction`.

**What I noticed reading 40 comments** (a random sample of the newest comments,
before I designed any labels). I first tried Hacker News, then switched to
`c/games`; these notes are from `c/games`.

1. **Some comments back a point with something checkable, most don't.** A few
   name a number, a date, a quote or a link (for example, a comment on Tencent's
   40% stake in Epic, or one quoting a project's AI policy). Most just assert.
2. **Many comments are strong opinions about a company or a game with nothing
   behind them** ("PvP sucks ass," "Valve doesn't give a shit either").
3. **Many are short personal reactions or experience** ("I'm having a blast!",
   "Oh wow. Didn't realize it had a phone port").
4. **Jokes, sarcasm and replies are common, and some only make sense inside
   their thread** ("What's the first supposed to be, Adolf Toastler?").


---

## Label Taxonomy

<!-- Each label: a one-sentence definition and two real examples from your
     reading. Then your decision rule for the hardest boundary.

     The decision rule is worth a point on its own and it's the thing most
     people leave out. Every taxonomy has a hardest boundary. Name yours. -->

### `analysis`

**Definition:** A point supported by a specific detail the poster actually
gives as support (a number, date, named source, quote, link, or described
mechanic) that the claim rests on. A fact that is merely asserted with no source
or number, background facts, and numbers about the poster's own experience do
not count.

**Example 1:**
> Tencent bought 40% afaik, and they had several rounds since then. Maybe Sweeney owns a *controlling* portion with a privileged share class, but I would be surprised if he owned a majority. …You are not wrong though. I guess this wouldn't be happening if he didn't have absolute control.
> https://lemmy.world/comment/26522765

**Example 2:**
> This is not big daddy N loosing a bunch of money, this is a market correction. People purchased shares 2 days ago expecting an announcement and the price has returned to normal https://finance.yahoo.com/quote/NTDOY/?guccounter=1 The shares were at 15.56 and are now at 14.81. It is still up 2% over the past week, 6% over the last 6 months, an 8.5% over the last year, and a whopping 51% over the last 5 years.
> https://lemmy.world/comment/14539448

### `hot_take`

**Definition:** A confident claim about a game, company, or the industry that
someone could disagree with, with no support offered. Rankings and comparisons
count, even when phrased from experience, and so do jokes or sarcasm that imply
a claim about the world.

**Example 1:**
> PvP sucks ass and I never ever want to go into PvP. So many games try to force people into PvP because so few people want to play PvP but those that do froth at the mouth for it so they need to force people into the meat grinder to make them happy. Fuck PvP.
> https://lemmy.world/comment/26519828

**Example 2:**
> Valve doesn't give a shit either
> https://lemmy.ca/comment/26017055

### `reaction`

**Definition:** The poster's own feeling, experience, plan, or question, or a
pure joke that implies no claim, without an argued claim. Short replies that
only make sense inside their thread, and recaps or quotes with no claim of
their own, go here.

**Example 1:**
> Huge recommend to bump that 0 up, yeah. I'm having a blast!
> https://lemmy.world/comment/26523738

**Example 2:**
> Oh wow. Didn't realize it had a phone port and it shooks shockingly good
> https://lemmy.world/comment/26522767

### The hardest boundary

**Which two labels:** `analysis` and `hot_take`. A second, close boundary is
`hot_take` and `reaction`.

**The decision rule I used every time:**

1. If the poster gives a specific detail (a number, date, named source, quote,
   link, or described mechanic) as support and the claim rests on it, it's
   `analysis`, even if the tone is heated or the fact is hedged ("afaik"). A
   fact that is only asserted, with nothing to point to, is not `analysis`.
   Background facts and numbers about the poster's own experience or setup do
   not count either.
2. If it only says what *I* felt, did, or plan to do, it's a `reaction`. If it
   asserts something about the world that another person could argue with,
   it's a `hot_take`. Rankings and comparisons are `hot_take`.
3. A joke or sarcasm that implies a claim about the world is a `hot_take`. A
   pure joke with no claim is a `reaction`.
4. Questions, short replies that only make sense in their thread, and recaps or
   quotes with no claim of the poster's own are `reaction`.

*These rules were tightened once, after a second Claude labeled 20 posts and
disagreed with me on 6 (see How I Used AI).*



---

## The Dataset

<!-- Where you collected from, how you labelled, your counts, and three hard
     cases. -->

**Where the posts came from:** Public comments from `c/games` on lemmy.world,
pulled through Lemmy's public API (no login). I used four sorts (Top, Hot, New,
Controversial), kept at most 3–4 comments per thread (200 threads in the final
file),
and kept whole comments between 40 and 1,000 characters. I dropped deleted and
removed comments, bot accounts, image-only comments, and comments tagged as
spoilers. Source links, dates and thread titles are in `labels_sources.csv`.

**How I labelled them:** Claude, an AI assistant, pre-labelled every one of the
259 rows against my written definitions. I did not label any posts cold, and I
did not read all 259 rows myself. What I did review: the definitions and
decision rules, the 46 rows Claude marked as hard or relabelled, via a second,
independent Claude chat that labelled them without seeing Claude's labels
(it matched on 37 of 46), and I approved each of the 6 resulting changes. Every
row's `note` says `ai-prelabeled`, and 36 rows carry a `hard:` note naming the
two labels they sat between. Claude read only the first ~350–400 characters of
longer comments before labelling them.

Two things about the sample: I added rows after the first pass to keep
`analysis` above my own 15% floor, and I found many of them by searching for
numbers, links and quotes. The label mix is therefore not the natural mix of
`c/games`, which is probably more `hot_take`-heavy.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| `hot_take` | 127 | 49.0% |
| `reaction` | 89 | 34.4% |
| `analysis` | 43 | 16.6% |
| **Total** | 259 | 100% |

**Three hard cases**

<!-- Any post that made you pause: what it was, which two labels it could have
     been, and what you chose. These are worth more than the easy 190. -->

**1.**
> *The post:* "Disney took the popular comedian Jimmy Kimmel off-air after he joked about it," No. Kimmel ragged on trump and maga but did not joke about kirk. Stop the gaslighting. (https://piefed.social/comment/9665324)
>
> *Could have been:* `analysis` or `hot_take`
>
> *I chose:* `hot_take`, because it states what Kimmel did and didn't say but gives no source, quote or number to point to. It was `analysis` at first, because the fact is checkable. I tightened the rule to require a detail actually given as support, and this moved.

**2.**
> *The post:* I believe it's still better than renting games. Here I can just backup my saves and move elsewhere (other provider or on real hardware if I want). I've been playing like 100h per year, so I thing spending 2K$ in hardware when I can just spend 40€/month is not worth it for me. (https://lemmy.ml/comment/28527426)
>
> *Could have been:* `hot_take` or `reaction`
>
> *I chose:* `reaction`, because the numbers (100h a year, 2K$ vs 40€ a month) are about the poster's own use and the conclusion is "not worth it for me." A second Claude labelled it the same way.

**3.**
> *The post:* \>Video Game Plus pointed to its policy of not selling games that only offer digital codes, and as a result, it will not sell the biggest game of 2026. The retailer noted that this isn't a reflection of Rockstar Games or the game itself, but argued that it's part of their goal of preserving physical media. Based (https://pawb.social/comment/22897120)
>
> *Could have been:* `analysis` or `reaction`
>
> *I chose:* `reaction`, because the poster only quotes an article and adds "Based." The detail is in the quote, not offered as support for a claim of their own. This is also why a similar recap, row 94, ended up `reaction`.

---

## The Training Run

<!-- Your starting model, your settings, and anything you changed and why. -->

**Base model:**

**Settings:** <!-- epochs, learning rate, batch size, seed -->

**Anything I changed from the defaults, and why:**

**Split sizes:** <!-- train / val / test, and per-label counts in the test
split. If a label had fewer than about 8 in test, say so — it explains a lot
of next unit's variance. -->



---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->

**Moment 1**

- *What I asked for:*
- *What came back:*
- *What I changed:*

**Moment 2**

- *What I asked for:*
- *What came back:*
- *What I changed:*

**Pre-labelling disclosure:**

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
