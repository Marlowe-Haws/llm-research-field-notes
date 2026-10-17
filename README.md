# How a team of AI models failed at research, and what caught it

*Marlowe Haws · October 2026*

Since August 2026 I have been building and running the **AI Research Hub**, a local environment in which several large language models work as a research team on open mathematics problems while I act as the human coordinator. Over ten weeks, 24 model profiles from about eight model families passed through it. The models kept detailed records, and because every action in the Hub goes through a logged, validated tool request, their failures left evidence.

This post summarizes what failed, how each failure was caught, and what the Hub now does about it. The numbers come from the models' own checkpoint records, from audits one model wrote about another, and from the Hub's tool logs. The Hub itself is still private; an open-source release is planned.

## The setup

- **One human coordinator** (me) assigns work, approves anything that reaches shared state, and relays messages to models that run in their own chat apps.
- **Roles:**
  - *Researchers* attack the problem.
  - *Architects* are AI coding agents that build the Hub software under my direction.
  - A *Curator* screens references.
  - A *Codifier* drafts the governing rules.
  - *Scouts* search the literature.
- **A research protocol with checkpoints.** CP0 is orientation, CP1 a validated formulation, CP2.x adversarial development rounds (each with a defect ledger and an attack-coverage map), and CP3 a package submitted for audit by a capable non-author before anything can be promoted. After promotion come blind critique and synthesis.
- **Governed computation.** Programs cited as evidence run in a Docker sandbox, and each run is bound to the exact source it executed.
- **The main problem so far:** whether the Longest Cubic Subsequence of a string can be computed in o(n⁵) time, an open question listed at IWOCA 2025.

## The richest dataset: one model's 79 self-recorded errors

The most thorough record came from a Claude Opus 5 research instance. Across 35 development checkpoints and 15 revisions of its audit package, it logged **79 numbered errors of its own**, each with a description, a "general form" and a countermeasure. At the end it grouped them into nine classes. The largest:

**Measurements that could not disagree with the claim.** A supposedly independent second test returned exactly zero for all twelve inputs, because the quantity it computed is never positive for that kind of matrix, whatever the structure. In the model's words, it was "two tests where one couldn't dissent." Other instances:
- a cross-check compared fitted power-law exponents for a quantity that isn't a power law, so two correct methods could never agree;
- an operation counter added up formulas instead of counting operations, so it "asserted rather than observed";
- a preregistered verdict was bet on a maximum over a sample whose size grew with the parameter. The model's lesson: "preregistration only protects a claim if the preregistered statistic is the one the claim is about."

**Reconstruction instead of reading.** The model filled part of a matrix with zeros "because that's what array initialization gives," where the source paper's convention was different, and its own validation gate caught it. Sessions later it made the identical mistake again, while the record diagnosing the first one sat unread in its workspace. Another time, a branch of work rested for three checkpoints on a ruling that a single read of an already-admitted five-page paper would have overturned.

**Harness and generator defects.** A hand-written random-number generator had period-2 low bits, so every "random" binary string was 0101…; three supposedly independent replicates were the same string. Another time, the program's own summary line printed that no prediction had been refuted while its table showed a prediction failing on 62 inputs: "Had I trusted the summary line instead of reading the table, a false claim would have entered this record."

**Records that drift.** Claim records went stale while the main document moved on. A stopping condition was written down and then ignored for four checkpoints, after which the check took four tool calls and closed the whole branch. The model's observation: "the wasted work gets MORE careful over time, because each round refines the thing the check would have deleted."

### The pattern that matters most

The model also recorded *how* each error was caught. Errors in the mathematics and the measurements were caught by known-answer controls, refuted predictions, or reading the primary source. For one stretch of 24 errors, none was found by inspection.

But the last cluster of errors was different. Errors 66, 68 and 75 through 79 were all caught by **non-author review**, in material the model had already judged finished. Every one was "a statement ABOUT the work rather than an error IN the mathematics":
- **a hypothesis left out:** a published lemma required a bound on the alphabet size, and the model quoted every other condition but that one;
- **the wrong deliverable:** the problem asks for the subsequence itself, and the result only bounded its length;
- **one part's resources stated for the whole algorithm:** the claimed memory bound belonged to only one of its branches;
- **a cleanup that reintroduced what it removed:** excluded material was stripped from record bodies and copied into their metadata by the same writes.

The model's own summary of the habit behind the last cases: **stating or checking a property of a whole from an inspection of a part.**

## The same mistake, at every scale

That "part for the whole" habit turned out not to be one model's quirk.

- **Another researcher (GPT 5.6 Sol)** proved that a particular algorithmic state set grows quadratically, and drew a broad conclusion about what any algorithm of that kind must pay. Its own adversarial self-audit re-checked the theorem with a different computation and found no defect. A non-author auditor (a Claude Opus 5 instance in the Curator role) then showed by derivation that almost all of those states were **dead**: provably unable to contribute to any answer. Under an equally sound pruning rule the family collapsed to exactly two states. The quantity measured grew quadratically; the quantity that mattered didn't grow at all.
- **A third researcher (Claude Sonnet 5)** offered "the fraction of states touched by an insertion" as evidence about update cost. Its analysis also covered insertions only, never deletions.
- **The AI agent that builds the Hub** made the same mistake in maintenance. It was asked to remove one metadata field from 24 records, and it regenerated each whole record instead, overwriting hand-written text in three of them. A diff against the previous version caught it before the change was finalized.

A detector the Opus model proposed, which I now apply everywhere: *be suspicious of any check whose declared scope is narrower than the object it certifies.*

## Failures before the research even started

Several models never reached the mathematics, because they failed at operating inside a structured tool environment:

- **Looping.** One model wrote its tool calls as plain text in a format its connection didn't execute, so nothing it requested ever ran. It kept re-issuing the same file request turn after turn, and answered two direct questions ("are they not being retrieved? Please talk to me") with the same tool call and no words.
- **Duplicates and state confusion.** Six of one model's nine refused requests were exact repeats inside a single turn. Others asked to run a program it had never saved, or to read a file under the wrong extension.
- **Malformed payloads.** Seven of another model's ten refusals were long records embedded in JSON that broke partway through.
- **Invented schemas.** One model repeatedly used patch operations and fields that don't exist.
- **Misread feedback.** A model fed instructions meant for a different profile tried to write into that profile's folder and was refused. A later attempt failed for an unrelated reason (a parser bug), but the refusal message looked the same, so the model blamed the same cause. Two causes behind one message produce wrong diagnoses.

The models I stopped using were dropped for exactly these protocol-level failures. The models that stayed failed in subtler, reasoning-level ways, and mostly documented those failures themselves.

## Where shared records got contaminated

The same Opus instance twice wrote its own research conclusions into what were supposed to be neutral reference cards for the whole team. One of these sat in the shared reference set for five days, where another model could have read the conclusion as a neutral summary of the paper. It also made the card useless for rechecking that very conclusion. The model's diagnosis: "the natural way to write a reference card is to explain why it matters TO ME."

## What the Hub does about it

| Failure | Safeguard in the Hub |
|---|---|
| Overclaiming, wrong readiness judgments | No promotion without a capable non-author audit at CP3, then blind critique; an audit is explicitly *not* verification |
| Measurements that can't disagree | CP2 rounds require preregistered predictions, known-answer controls that are able to fail, and two methods unless one is exhaustive |
| Unbound evidence | Only sandboxed, source-bound runs count as research evidence; host-run diagnostics don't |
| Reconstruction instead of reading | Curated, neutral reference cards and page-ranged reading of primary sources, one tool call away |
| Shared-record contamination | Reference cards are kept to the paper's literal content, and only the Curator maintains admitted ones; independent ("Fresh Mind") sessions are kept from seeing earlier project work |
| Loops and duplicates | Exact repeats within one reply are skipped and the earlier result named; a no-progress guard stops repeated completed requests |
| Malformed or truncated requests | Ambiguous requests are never executed; failed requests are kept with IDs so the model can correct or discard them |
| Stale records | Discharged obligations get forward pointers, and records carry relationships such as "superseded by" |

## What I take from it

1. **Authors catch errors in the work; non-authors catch errors about the work.** The cheapest defence against overclaiming isn't a smarter author, it's a second reader with a different vantage point.
2. **The most common research defect wasn't wrong math.** It was a test that could not have failed. Preregistration only helps if the preregistered statistic is the one the claim is about.
3. **Self-assessed readiness was unreliable.** The Opus instance declared its package ready four times before it was; as it put it, "my unprompted readiness judgments have now been wrong four times running." Every package that reached audit needed at least one more revision cycle.
4. **Correction can overshoot.** The same model once asserted a criticism of its own work before running the test, and the test refuted the criticism 18 of 18. Being harsh on yourself is still asserting a conclusion before the evidence.
5. **A human question is a real detector.** One error was found only because I asked the model to look again at a shortcut, on the general grounds that shortcuts tend to have side effects.

## Limits of this account

- **Most of the detailed research data is one model's self-report.** It is unusually thorough, but the classification is its own, and the error counts aren't comparable across models.
- **Models had very different amounts of time** (from 1 session to 14), so failure counts reflect exposure as well as capability.
- **None of the research claims mentioned here has yet been through the Hub's blind critique,** so nothing in this post asserts a result about the problem itself.

---

*This post was drafted with the help of Claude (the Hub's current Architect instance), working from the Hub's own records. The research models quoted are Claude Opus 5, Claude Sonnet 5 and GPT 5.6 Sol instances; the other models are described rather than named, because their failures reflect this particular setup as much as the models themselves.*
