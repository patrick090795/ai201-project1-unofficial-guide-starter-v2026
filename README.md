# The Unofficial Guide


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

I used AI during this unit to help me inspect my test results, identify mistakes
in my evaluation setup, and make one measured improvement.

One important moment was when my first Before evaluation used the
`campus_life` corpus instead of the `city_guides` corpus. I shared the run output
with AI, and it pointed out that the retrieved files were campus-related files
such as course and dining documents. I then checked the command-line options,
rebuilt the index with the correct `city_guides` corpus, and reran the
evaluation.

I also used AI to help interpret the Before results against my original
acceptance criteria. I still checked the actual retrieved chunks and outputs
myself before marking each criterion MET.

For the improvement, I asked AI for help adding hybrid search to `store.py`.
The suggested approach combined the existing semantic vector search with BM25
keyword search while keeping the original cosine distance for the relevance
gate. I implemented the change, ran `python test.py`, checked retrieval output,
and then ran the full After evaluation.

Finally, I used AI to help compare the Before and After results. The acceptance
criteria stayed at 5/5, so I did not claim that hybrid search improved the
system. I reported that it changed retrieval rankings but did not produce a
measurable improvement on my current test set.

---

# Unit 2

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

Evidence file: `results/run_2026-09-30_1914_before.md`

Produced by: `run_eval.py::main`

Retrieval produced by: `store.py::search`


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

I added hybrid search by combining the existing semantic vector search with
BM25 keyword search. I chose this because several of my questions contain exact
place names and transportation terms such as Brightwater, Halden Bay, and
Kestrelford. BM25 can give more weight to those exact terms while semantic
search still captures meaning.

### Before vs. After

| Criterion | Before | After |
|---|---|---|
| Retrieved chunks contain the answer | 5/5 | 5/5 |
| Every answer names a source | 5/5 | 5/5 |
| Relevance gate stops out-of-corpus questions | 5/5 | 5/5 |
| Chunks stay within one guide section | 5/5 | 5/5 |
| Correct source | 5/5 | 5/5 |

The hybrid-search change did not improve the acceptance-criteria scores because
the semantic-only system was already meeting all five criteria.

It did change the retrieval ranking. For example, for the Brightwater train
question, the regional transport guide moved higher in the retrieved results.
However, the final answer remained correct before and after the change.

Because the measured criteria stayed the same, I cannot claim that hybrid
search improved the system based on this test. The result shows that the
original semantic retrieval was already strong for these five questions.

### Run Log — After


After adding hybrid search, I ran the same five test questions again using the
same `city_guides` corpus, `top-k = 5`, relevance cutoff of `0.55`, and three
runs per generated-answer question.

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunks contain the answer | At least 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 2. Every answer names a source | 5 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 3. The relevance gate stops out-of-corpus questions | At least 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 4. Chunks stay within one guide section | At least 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 5. Correct source | At least 4 of 5 | 5/5 | 5/5 | 5/5 | MET |

**Did it help?**

No measurable improvement was shown by the acceptance criteria. All five
criteria scored 5/5 before and after the change. Hybrid search changed some
retrieval rankings, but it did not improve the measured success rate on this
test set.

## What's Still Broken

After the hybrid-search improvement, none of my five acceptance criteria were
still missed. The system continued to meet all five criteria in the After run.

However, the evaluation also showed that hybrid search did not produce a
measurable improvement in the acceptance-criteria scores. The Before and After
results were both 5/5 across the measured criteria.

The retrieval rankings changed in some cases, but the changes were not always
clearly better. For example, some additional less-relevant guide files appeared
in the top retrieved results after hybrid search was added.

If I continued working on the system, I would test it with a larger and more
difficult set of questions. I would especially include questions with similar
place names, exact numbers, and information that appears in multiple guide
documents. This would make it easier to determine whether hybrid search is
actually better than semantic-only retrieval.

I stopped here because the required improvement was implemented and measured,
and the current acceptance criteria were already being met.

## What I'd Do Differently

If I wrote the acceptance criteria again, I would make Criterion 1 stricter.

My original criterion required the retrieved chunks to contain the answer for
at least 4 of 5 questions. The system achieved 5 of 5 in both the Before and
After evaluations, so the original target turned out to be fairly easy for this
test set.

In the next unit, I would change the target to 5 of 5, or make the criterion
more demanding by requiring the correct answer-containing chunk to appear in
the top three retrieved results.

That would make the criterion more useful for distinguishing between a system
that simply retrieves a relevant chunk somewhere in the results and one that
ranks the best evidence near the top.
