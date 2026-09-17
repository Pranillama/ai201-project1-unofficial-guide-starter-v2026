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
Most `campus_life` documents are short, single-topic notes (average 317
characters), so a well-matched question should land on its document almost
every time — my Milestone 1 test (`"is the housing lottery random?"`) came
back with a best distance of 0.254, well inside a close match. I'm not
setting 5 of 5, though, because several topics have near-duplicate document
clusters: six housing halls each have a main/laundry/noise triple, and seven
dining halls each have a main/followup pair. A question like my laundry one
about Aldridge Hall is competing against five other halls' laundry
documents that use almost identical phrasing ("Best time to do laundry
here is..."), so I expect at least one of my five to be a near-miss where a
topically-adjacent chunk outranks the correct one.

---

## 2. Every answer names a source

Every answer the system produces names at least one source document.

**Why this target:**
This is 5 of 5, not 4 of 5, because it isn't a retrieval-quality question —
it's a formatting question. `generate.py`'s grounding prompt instructs the
model to always cite the source file it pulled the answer from, and that
instruction doesn't get harder or easier depending on which document was
retrieved. In my Milestone 1 test run, the answer named
`admin_housing_lottery.txt` without being asked twice. The only way this
should fail is a prompt-following breakdown in the model itself, which
would be a bug worth catching, not a target worth softening.

---

## 3. The relevance gate stops out-of-corpus questions

When I ask a question my documents clearly don't cover, the relevance gate
stops it and the system returns "I don't have enough information about that" —
in at least 4 of 5 tries.

<!-- The five questions are the ones in `OUT_OF_SCOPE` at the bottom of
     `questions.py`, and `run_eval.py` puts them through the gate and writes
     what happened into your run log. Swap them for your own if you'd rather —
     just keep five of them, or the "4 of 5" above has nothing to be 4 of. -->

**Why this target:**
I haven't run the Milestone 4 calibration yet, so I'm setting this target
from what I can already see rather than from measured distances. My
`OUT_OF_SCOPE` questions (capital of Mongolia, changing diesel engine oil,
a 1994 World Cup result, ibuprofen dosage, a Rust for-loop) sit in domains
`campus_life` never touches at all — no document even mentions cars,
sports, or programming — so I expect their best distances to sit well
above anything in-corpus, similar to how my one Milestone 1 in-corpus test
scored 0.254. I'm keeping the target at 4 of 5 rather than 5 of 5 because I
haven't yet measured the actual gap between the two groups, and I'd rather
under-promise before Milestone 4's real numbers than claim a perfect score
I haven't earned.

---

## 4. Something about your chunks

At least 4 of 5 chunks sampled with `python app.py chunks -n 5` read as a
complete thought — no sentence is cut in half at either the start or the
end of the chunk.

**Why this target:**
When I ran `python app.py index` in Milestone 1, the summary reported 88
chunks from 88 documents (317 characters average, shortest 178, longest
549) — meaning the current `CHUNK_SIZE` of 800 is already larger than
every document in my corpus, so today's naive chunker happens to keep each
document whole almost by accident, not by design. That won't necessarily
hold once I build a real chunker in Milestone 3, especially for the
longer, multi-sentence documents (like the 549-character ones) if I pick a
smaller chunk size to get tighter, more topic-focused chunks. I'm setting
4 of 5 rather than 5 of 5 because a sentence-aware splitter can still
misjudge a boundary (e.g. an abbreviation or a colon) on an unusual
sentence, and I'd rather catch that with a real target than pretend it
won't happen.

---

## 5. Your choice

For at least 4 of my 5 test questions, the source the system cites is the
*correct* one — the same file that contains my `expects` substring — not
merely any source at all.

**Why this target:**
Criterion 2 only checks that an answer names *a* source; it doesn't check
that the source is the *right* one. That gap matters specifically in
`campus_life` because of its near-duplicate document clusters — six
housing halls each have a laundry doc and a noise doc with nearly
identical phrasing, and seven dining halls each have a main doc and a
"followup" doc. It would be easy for the system to retrieve a
topically-close but factually-wrong chunk (say, Calder Annexe's laundry
times instead of Aldridge Hall's) and still confidently cite *a* file,
passing criterion 2 while actually misleading the user. I care about
catching that failure mode specifically, so this criterion checks
attribution correctness, not just attribution presence. I set 4 of 5
rather than 5 of 5 for the same reason as criterion 1: the near-duplicate
clusters make at least one mix-up plausible.

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
