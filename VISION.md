# VISION — the learner I am building toward

**Owner:** Saurabh Jalendra · **Created:** 2026-09-02 · **Cadence:** reviewed quarterly, rewritten annually
**Status:** DRAFT. The goal in §1 is my own words. §2 onward is the version that survived a 55-agent
fact-check and two adversarial attacks the same day; it should be rewritten in my voice before it is
treated as final. Evidence base: `Personal-Agents/tasks/project-research/candidate-vision-teenage-learner/`.

---

## 1. The goal, as I first said it

> Build a system or model that is like a teenage child which has the ability to learn and understand
> things to then solve problems, and also by being sample efficient — much better than humans. That
> makes it faster to learn and better to solve.

That is the ambition. It is not yet a research goal, because as written it cannot be falsified. The
rest of this document turns it into one without giving the ambition up.

## 2. The goal, made falsifiable

> **Build a learner that, on a human-scale experience budget (≈10⁸ waking seconds; 10⁷–10⁸ words),
> reaches median-human competence on held-out tasks in named domains, and — in the first N
> interactions with a new task — needs fewer experiences than a matched human cohort to reach the
> same score.**

Every clause below is there because a verified finding forced it.

| Clause | Why it is there |
|---|---|
| **Two axes, scored separately** — learning speed (experience-to-threshold) and solving quality (asymptotic score) | Humans learned faster on all four Atari games in Tsividis et al. 2017; DDQN was asymptotically better on two. The conjunction "faster and better" is a trade-off in the data |
| **Two ledgers** — in-episode experience only; in-episode plus amortized prior — on the same ledger for human and machine | A teenager is a pretrained learner. Counting only in-episode samples is "unfair" (Tsividis et al.) in whichever direction you look |
| **A declared prior** — what the system is born with, stated up front (Spelke's four core systems, Marcus's primitives, or a compressed Zador-style prior) | Marcus, Mitchell and Zador converge: no biological learner starts from scratch. "Few samples" is not falsifiable until the prior is named |
| **One named definition of sample efficiency** — PAC-MDP count, regret vs optimum, return-at-budget, or samples-to-threshold | The primary literature holds four incompatible definitions and Kakade's thesis says there is no formal one. Pick, in writing |
| **Superiority claimed only in the low-sample regime** | RL² beats the Bayes-optimal baseline at 10 episodes and falls below it at 100. No claim once the budget is large |
| **Per-domain targets with today's gap stated** — ~2.5 pp language (BLiMP), ~10 pp social (ToMBench), ~41 pp intuitive physics (IntPhys 2) | The human–machine gap differs by an order of magnitude across domains. One number cannot cover them |
| **Compute bounded per problem** — a stated FLOPs budget with an adaptive controller; inference compute may not substitute for samples | EfficientZero bought efficiency with MCTS; AlphaZero replaced data with search. Unbounded compute makes the claim empty |
| **Mechanism named, not assumed** — which of Lake et al.'s five ingredients it implements; a perturbation probe (randomising demonstrations must hurt) to separate learning from retrieval | "Learns from few examples" without a mechanism is the abstract of a 2017 paper. The probe is how in-context "learning" is distinguished from prior activation |
| **A world model, if used, justified on planning or transfer — never on sample count** — with short, empirically calibrated rollouts | On identical Atari-100k protocol, tuned model-free matches or beats model-based. Imagination does not deliver efficiency as a property |
| **Continual learning as three sub-goals at a stated scale** — keeps what it learned; keeps the ability to learn; **selects tasks by a named intrinsic signal and passes a noisy-TV probe and a detachment/derailment probe** | Forgetting is unsolved at scale; plasticity loss is a separate confirmed failure; curiosity's failure modes are confirmed and still open. Validate first at 100M–1B where mitigation exists |
| **Falsification clause** — the claim is false if, on the agreed held-out set at matched budget, the system fails to reach median-human score in any target domain | A goal that cannot fail cannot be pursued |

## 3. What "teenager" means here — and nothing else

Two things, both sourced:

1. **The experience budget.** ~2×10⁸ waking seconds; 10⁷–10⁸ words of language by adolescence
   (Frank 2023; BabyLM). Not 10⁹ seconds. Not "10–50M words by 13" — that figure exists in no source.
2. **Graceful handling of rare or novel task components.** The one property where humans and the
   best meta-learner (AdA) visibly diverge — AdA fails on tasks "extremely rare" in training that
   humans solve easily — and the one no fetched source demonstrates in a machine.

Everything else the word was carrying is either unsourced or another lab's slogan. No lab program
targets a teenager; the image is mine, and it is allowed to do only the two jobs above.

## 4. What I do not claim

- **"Much better than humans"** as a general, cross-domain, unqualified statement. It survives in no
  form. Exactly one verified precedent exists — EfficientZero, Atari-100k, 109% *median* — in a
  discrete, resettable, dense-reward simulator with heavy compute per decision.
- **Novelty of the ambition.** It is the stated program of LeCun/AMI (2022), Sutton's Alberta Plan
  (2022), and Lake et al. (2017), executed today by AMI Labs, DeepMind, VERSES and the BabyLM /
  DevBench / IntPhys / ToMBench benchmark community. The contribution has to be a specific hole.
- **That any single route delivers efficiency.** Pretraining is non-monotonic and can hurt (Cal-QL;
  Schneider et al. 2024). Meta-learning adapts only inside its training distribution. Imagination
  does not beat tuned model-free on the one benchmark where both were measured.

## 5. How it cascades

Vision → quarterly goals → project IDEA → spec. The five active projects each answer a piece of §2;
none of them needs to be renamed for this to be true, but each should now state which piece.

| Project | The clause it tests | The open hole it sits on |
|---|---|---|
| **P1** Physics Lie Detector | Did the model *understand*, or fake it? | #7 — a computational test of the Spelke inference on the 41-pp intuitive-physics gap |
| **P2** Inherited Cheating | What transmits when a learner learns from a teacher | #2 — a perturbation probe separating in-context learning from retrieval (same method) |
| **P3** When Dreams Lie | Can imagination be trusted — the gate on any world-model clause | #5 — negative transfer for JEPA-class predictive priors (also the AMI blocker) |
| **P4** Hierarchy Dividend | Does abstraction buy planning power | #11, #12 — the value-equivalence cost curve; model-based vs model-free under transfer |
| **P5** BayesGap | **The yardstick itself** — excess regret over a computable Bayes-optimal learner | #9 — a learner fast early and never below the optimum late, scored by that yardstick beyond toy bandits |

The full list of twelve open sub-questions, each sourced to a finding, is in
`candidate-vision-teenage-learner/FORMULATION-2026-09-02.md` §5. Adjacency to existing projects is
stated there so it can be weighed — it is not a reason to choose.

**The weather world-model idea** is a testbed under this vision — one domain instance of "learn a
model of a physical system from data, then act on it" — not a sixth project. Its verified fact base
is in `candidate-weather-world-model/`.

**What the program does not yet touch**, relative to §2: an unbounded-task-stream benchmark and
controlled graceful forgetting (#6), and human learning-curve baselines for continuous-control
suites (#1) — the cheapest and most broadly useful thing on the list, because no lab has one.

## 6. Standing laws

These are already in force across the research program and bind this document:

- **No bias in any state of the research.** Evidence per question; steelman the alternative; kill
  criteria stated before work starts; the absolute-swing alarm (replacing one absolute with its
  opposite means both are wrong).
- **Reproduce first, then innovate.** Every project has a reproduce stage before a claim stage.
- **Evals by default.** The measurement layer is built before the model is trusted.
- **No fabricated numbers, citations or identifiers.** Everything traces to a read source. A
  verified absence is a valid finding.

## 7. Provenance and review

- Stated 2026-09-02. Fact-checked the same day: 40 claim-checkers, 8 dimension synthesizers,
  2 adversaries, then 5 re-runs — 41 verified claims (21 confirmed, 13 corrected, 6 contested,
  1 unverified). The coordinator's own errors are logged in the formulation doc, §2.
- **Quarterly review** (`/docs review`): does each active project still name the clause it tests?
  Has any open hole been closed by someone else? Has the per-domain gap moved?
- **Annual rewrite.** If §2 changes mid-year, record why here.
