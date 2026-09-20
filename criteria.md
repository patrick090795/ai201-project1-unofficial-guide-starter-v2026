# Acceptance criteria — The Unofficial Guide

Five criteria that say what "working" means for this system, written in unit 1
**before** any results existed.

An acceptance criterion names a target: a number, a count, a rate, or something
a person could plainly observe. *"Retrieval works"* is an opinion. *"For at
least 4 of my 5 test questions, the top results include a chunk containing the
answer"* is a criterion.

Under each one, write a sentence or two on **why that target** and not a
stricter or looser one. A reason that says something about your corpus or your
pipeline earns credit; *"80% seemed reasonable"* does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---

## 1. Retrieved chunks contain the answer

For at least 4 of my 5 test questions, the retrieved chunks include one that
contains the answer.

**Why this target:**

 My five test questions ask about clear facts that are written in the city guide documents. I chose 4 of 5 because I expect the retriever to find the right information most of the time, but one question may still be harder because similar information can appear in more than one document. A lower target would be too easy.

---

## 2. Every answer names a source

Every answer the system produces names at least one source document.

**Why this target:**

The system is supposed to answer from the documents and show where the information came from. Because of that, I expect every answer to name at least one source. If an answer has no source, it would be hard to check whether the answer is really based on the documents. 

---

## 3. The relevance gate stops out-of-corpus questions

When I ask a question my documents clearly don't cover, the relevance gate
stops it and the system returns "I don't have enough information about that" -
in at least 4 of 5 tries.
 
**Why this target:**

The relevance gate should stop questions that are clearly not covered by the city guide documents. I chose 4 of 5 because I expect it to reject most unrelated questions, but one question may still look similar to something in the corpus and pass the gate by mistake. More than one failure would show that the cutoff may need more tuning. 

---

## 4. Chunks stay within one guide section

At least 4 of 5 sampled chunks should draw from a single labeled section of the guide, rather than blending material from two different sections.

**Why this target:**

When I reviewed the city guides, I saw that each one is organized into clearly separated sections like “Getting there,” “Eat and drink,” and “When to go.” Because those sections are fairly self‑contained, most chunks should naturally stay within one boundary. I chose 4 of 5 to allow for the occasional long section that may need to be split, while still ensuring that mixed‑section chunks remain rare. A target of 3 of 5 would tolerate too many chunks that blur unrelated topics, which would weaken retrieval quality.


---

## 5. Correct source

For at least 4 of 5 answers, the system should cite a source document that really supports the answer


**Why this target:**

When I read the city guide documents, I noticed that some information appears in more than one file. For example, transportation information can appear in both a city guide and the regional transport guide. I want to make sure the system gives the right source, not just any source that looks related. I chose 4 of 5 because one mistake can happen when two documents have very similar information, but more than one mistake may show a problem with how the system tracks sources 

---
<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 2 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath it, like this:

         ## 1. Retrieved chunks contain the answer

         For at least 4 of my 5 test questions, the retrieved chunks include
         one that contains the answer.

         **Why this target:** ...

         > **Revised in unit 2:** For at least 4 of 5 questions, the top three
         > results contain the answer.
         >
         > **Why revised:** I couldn't judge "the chunks include one that
         > contains the answer" the same way twice — I scored two questions
         > differently on Monday than on Wednesday. The new version is
         > something I can actually check.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "I said 4 of 5 but got 2 of 5, so 2 of 5 is more realistic."

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.

     The whole reason the originals stay visible is so someone can see what you
     said before you knew the answer.
     ───────────────────────────────────────────────────────────────────────── -->
