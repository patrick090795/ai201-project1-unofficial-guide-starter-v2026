# The Unofficial Guide

<!-- Replace this line with your name and which corpus you picked. -->

> **This file is your submission.** Fill it in as you go — most sections get
> written during the milestone that produces them, not at the end.
>
> How the starter works, and every command you'll need, is in `RUNNING.md`.
> Leave that file alone.
>
> **Paste everything as text.** No screenshots, no video. A typed table gets
> full credit; a picture of the same table gets none.
>
> Delete these instruction blocks as you replace them. The `<!-- -->` comments
> are notes to you and don't show up when the page renders — you can leave them
> or remove them.

---

# Unit 1

## What This Does

This project is a question-answering system built using the `city_guides`
corpus. It lets users ask questions about places, transportation, food,
accommodation, activities, and the best times to visit different towns.

The system splits the guides into meaningful sections, stores them as
embeddings, and retrieves the most relevant chunks for each question.
It answers using only the retrieved documents and includes the source files
used for the answer. If the documents do not contain enough relevant
information, the system refuses to answer instead of making something up.

## Chunking Strategy

**Chunk size:**

One Markdown section per chunk. I also add the document title to each section so that every chunk keeps the name of the place it describes. In my current `city_guides` corpus, there are 94 chunks. The chunks average about 322 characters, with the shortest at 174 characters and the longest at 762 characters.

**Overlap:** 0

I chose a section-based strategy because the city guide documents are already organized into clear sections such as "Getting there," "Eat and drink," and "When to go." The original fixed-size chunker could cut across these section boundaries and mix unrelated topics.

Keeping each section as its own chunk helps keep related information together. I also added the document title to each chunk because some sections, such as "When to go," did not include the name of the town. Adding the title gives the chunk more context and improved retrieval.

I did not use overlap because overlapping neighboring sections could mix different topics in the same chunk.


## Sample Chunks

**Chunk 1** — source: `guide_accessibility.md#0 ` — produced by: ` chunker.py::split_documents`

```
# Getting around the region with limited mobility

An honest assessment rather than a promotional one. Some of these places are
difficult and it is better to know in advance.


```

**Chunk 2** — source: `guide_corry_vale.md#5` — produced by: `chunker.py::split_documents`

```
## When to go

May to September. Outside those months the pub in the third village closes, the farm shop reduces its hours, and several footpaths become genuinely boggy rather than merely wet. The road is not gritted above the second village and is impassable in snow.

```

**Chunk 3** — source: ` guide_givens_mill.md#2 ` — produced by: `chunker.py::split_documents`

```
## Eat and drink

A tearoom attached to the mill, open 10 to 4 daily except Tuesdays, which sells bread made from the flour ground twenty metres away and is the reason most people come. One pub, food served lunchtimes and Thursday to Saturday evenings.

```

**Chunk 4** — source: ` guide_kestrelford.md#4 ` — produced by: `chunker.py::split_documents`

```
## When to go

Late spring and early autumn. The Saturday market runs year-round but is much reduced from November to February. August is busy with walkers. The single-track approach road is genuinely difficult in snow and the town can be cut off for a day or two most winters.
```

**Chunk 5** — source: ` guide_pellew_sands.md#6 ` — produced by: ` chunker.py::split_documents`

```
## The railway

The line runs along the river valley, connecting Brightwater to the regional
hub in 50 minutes. Eleven services a day on weekdays, six on Sundays. The line
north of Brightwater closed in 1963 and everything beyond it is bus or car.

Tickets are cheaper booked the day before than on the day, and considerably
cheaper than that booked a week ahead. There is no ticket office at
Brightwater station outside weekday mornings; the machine on the platform takes
cards only.

```

## Sample Answer

**Question:**

What are the best months to visit Brightwater?

**Answer:**

May and June are the best months to visit Brightwater, as the days are long,
everything is open, and the students are largely gone (`guide_brightwater.md`).
Late May is also described as a very good time of year in
`guide_seasons.md`.

**Sources retrieved:** `guide_brightwater.md`, `guide_seasons.md`,
`guide_thornby_wells.md`


**My relevance cutoff:** 0.55

The five in-corpus questions had best distances from 0.1989 to 0.2938.
The five out-of-scope questions had best distances from 0.8026 to 0.9753.
There was a clear gap between the two groups, so I chose 0.55 as the cutoff.

| Question | In corpus? | Best distance |
|---|---|---:|
| How long does the train from Brightwater to the regional hub take? | Yes | 0.2597 |
| What are the best months to visit Brightwater? | Yes | 0.2938 |
| How many times a day does the bus run to Halden Bay? | Yes | 0.2850 |
| What are the best months to visit Halden Bay? | Yes | 0.1989 |
| Does the Kestrelford bus service run on Sundays? | Yes | 0.2675 |
| What is the capital of Mongolia? | No | 0.8026 |
| How do I change the oil in a diesel engine? | No | 0.8881 |
| Who won the 1994 World Cup? | No | 0.9753 |
| What is the recommended dosage of ibuprofen for a headache? | No | 0.8350 |
| How do I write a for loop in Rust? | No | 0.8365 |

## How I Used AI

**1.**
I asked AI to help me improve the chunking strategy for the `city_guides`
corpus. AI suggested splitting the documents by Markdown sections instead of
using fixed-size character chunks. I tested that approach and later noticed
that some chunks, such as "When to go," did not include the town name. I then
updated the chunker so that each section also includes the document title.
This improved retrieval because the chunk kept both the topic and the place
name.

**2.**
I asked AI to help me understand the retrieval distance results and choose a
relevance cutoff. AI helped me compare the five in-corpus distances with the
five out-of-scope distances. The in-corpus questions ranged from about 0.1989
to 0.2938, while the out-of-scope questions ranged from about 0.8026 to 0.9753.
Based on that gap, I changed the cutoff from 0.6 to 0.55 and tested it again.
The valid questions still passed, while unrelated questions were rejected
before the model was called.



<!-- ── Stretch features ─────────────────────────────────────────────────────
     Doing one? Say so here BEFORE you start. A feature this README never
     claims earns nothing.
     ───────────────────────────────────────────────────────────────────────── -->

---

# Unit 2

<!-- These sections get ADDED to what's already above. Don't delete or rewrite
     unit 1 — the point is that someone can see what you said before you knew
     how it went. -->

## Run Log — Before

The system was tested using the `city_guides` corpus with `top-k = 5`
and a relevance cutoff of `0.55`. Each generated-answer question was
run three times with caching disabled.

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunks contain the answer | At least 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 2. Every answer names a source | 5 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 3. The relevance gate stops out-of-corpus questions | At least 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 4. Chunks stay within one guide section | At least 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 5. Correct source | At least 4 of 5 | 5/5 | 5/5 | 5/5 | MET |

### Real output samples

**Criterion 1 — Retrieved chunks contain the answer**

Question: `How long does the train from Brightwater to the regional hub take?`

Retrieved source: `guide_brightwater.md`, section `Getting there`.

System answer:

> The train from Brightwater to the regional hub takes 50 minutes
> (according to `guide_brightwater.md` and `guide_regional_transport.md`).

The retrieved results contained the relevant Brightwater transportation
information, and the system produced the expected factual answer.

**Criterion 2 — Every answer names a source**

Question: `How many times a day does the bus run to Halden Bay?`

System answer:

> The bus to Halden Bay runs four times a day
> (according to `guide_halden_bay.md` and `guide_regional_transport.md`).

Every generated answer across all three runs named at least one source
document.

**Criterion 3 — The relevance gate stops out-of-corpus questions**

The gate refused all five out-of-corpus questions:

- What is the capital of Mongolia?
- How do I change the oil in a diesel engine?
- Who won the 1994 World Cup?
- What is the recommended dosage of ibuprofen for a headache?
- How do I write a for loop in Rust?

Result: `5/5 refused`.

**Criterion 4 — Chunks stay within one guide section**

The five sampled top chunks stayed inside a single labeled section:

- Brightwater train → `Getting there`
- Brightwater visit months → `When to go`
- Halden Bay bus → `Getting there`
- Halden Bay visit months → `When to go`
- Kestrelford Sunday bus → `Buses`

Result: `5/5`.

**Criterion 5 — Correct source**

Question: `Does the Kestrelford bus service run on Sundays?`

System answer:

> No, the Kestrelford bus service does not run on Sundays
> (`guide_regional_transport.md` and `guide_kestrelford.md`).

The cited documents were among the retrieved sources and directly
corresponded to the transportation question.

## Verdicts

### 1. Retrieved chunks contain the answer — MET

The target was for at least 4 of my 5 test questions to retrieve a chunk
containing the answer. All 5 questions retrieved relevant chunks that contained
the information needed to answer the question. The result was 5/5, so this
criterion was met.

### 2. Every answer names a source — MET

The target was for every generated answer to name at least one source document.
Across all five questions and all three runs, every answer included at least one
source document. Therefore, this criterion was met.

### 3. The relevance gate stops out-of-corpus questions — MET

The target was for the relevance gate to refuse at least 4 of 5 clearly
out-of-corpus questions. It refused all 5 questions, giving a result of 5/5.
Therefore, this criterion was met.

### 4. Chunks stay within one guide section — MET

The target was for at least 4 of 5 sampled chunks to stay within one labeled
guide section. All five sampled chunks stayed within a single section such as
"Getting there," "When to go," or "Buses." The result was 5/5, so this
criterion was met.

### 5. Correct source — MET

The target was for at least 4 of 5 answers to cite a source document that
actually supports the answer. All five answers cited relevant documents that
were present in the retrieval results and supported the information used in the
answer. The result was 5/5, so this criterion was met.

## Diagnoses


None of the five acceptance criteria were missed in the Before evaluation.

The retrieval distances for the five in-corpus questions ranged from about
0.199 to 0.294, while all five out-of-corpus questions had distances above
0.80. This gave the relevance gate a clear separation between supported and
unsupported questions.

Because all criteria passed, there was no failed pipeline stage to diagnose.
However, Criterion 1 may have been somewhat conservative because the system
retrieved the needed information for all 5 questions rather than the required
4 of 5.

## The Improvement

**What I changed:**

**Why I picked it:**

<!-- Connect it to a specific diagnosis above in one sentence. If you can't,
     you picked a fix because it sounded impressive. -->

### Run Log — After

<!-- Same format, same five criteria, three runs each.
     `python run_eval.py --label after` -->

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 |  |  |  |  |
| 2. Every answer names a source | 5 of 5 |  |  |  |  |
| 3. Gate stops out-of-corpus questions | 4 of 5 |  |  |  |  |
| 4. | | | | | |
| 5. | | | | | |

**Did it help?**

<!-- Say plainly whether it did, and how you know. If it made things worse,
     say that — a change that backfired, honestly reported, earns full credit
     and is more interesting than one that worked. What matters is that you can
     tell.

     Milestone 4. -->

## What's Still Broken

<!-- For each criterion still missed after your fix: what you'd do about it,
     and why you stopped where you did.

     "I ran out of time" is fine if it's true. Pretending nothing is left is
     not.

     Milestone 5. -->

## What I'd Do Differently

<!-- Knowing what you know now — which of your five criteria would you write
     differently, and why?

     Milestone 5. -->
