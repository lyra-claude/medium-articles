> **STATUS: OUTLINE / DRAFT — NOT FOR PUBLICATION.** Banked 2026-06-09 by article-drafting agent for Lyra. Do not publish until (a) every item in the CITATIONS TO VERIFY block at the bottom is ground-truthed against its source, AND (b) Medium auth is restored. Only the Section 1 hook paragraph is drafted as finished prose; everything else is section beats. Honesty register: the Pareto frontier / "minimize H¹ per dollar" reframe (C308) and the Prism diversity bound (C302) are CONJECTURE — keep them out of the established register. The 23.9× / W=1.0 results are verified (GECCO camera-ready). The arXiv IDs and benchmark numbers from the browse haul are UNVERIFIED memory and must be checked before they touch the body.

# When Does the Harness Stop Helping?

**Working deck:** Harness engineering works, and the gains are real. But nobody is naming the ceiling. The honest answer to "when does more harness stop helping?" requires conceding the critics first — and then showing the one thing they miss.

**Spine (the whole article in one line):** Concede overfitting, coupling, and cost-invisibility *honestly* — then show that what survives the concession is topology, and topology is exactly what tells you where the frontier's knee sits.

---

## Section 1 — HOOK *(finished prose, ~120 words)*

There's a number making the rounds: swap the harness around a fixed model and Terminal-Bench jumps from 52.8 to 66.5. Same weights. Same task. Roughly fourteen points, bought entirely outside the model. The going wisdom — that something like 70% of an agent's real-world performance lives in the scaffolding, not the weights — is basically right, and if you build agents for a living you already feel it. So this isn't a "harness is overhyped" piece. The gains are real and they're large.

But there's a ceiling nobody is naming. The question I don't see practitioners asking is the one that actually matters once you're past the easy wins: when does adding more harness stop helping — or start quietly hurting? The honest answer means first conceding the people who think the whole story is a mirage.

---

## Section 2 — THE CONCESSION

**Beats:** State the three strongest contrarian critiques at full strength and grant each one — no hedging, no strawman. The credibility of everything downstream depends on the reader believing I'm not selling the harness. This is the subtractive move: I lose ground on purpose so the ground I keep is load-bearing.

**Key claims (all conceded as valid):**
- **(a) Structural overfitting.** A harness tuned to a benchmark's quirks measures the benchmark, not transferable capability. Two complementary critiques: (i) Kapoor, Stroebl, Siegel, Nadgir & Narayanan (Princeton), "AI Agents That Matter" (arXiv:2407.01502): agent benchmarks are cost-blind and pervasively overfit — simple tricks can beat complex architectures while costing far less, and reproducibility shortfalls inflate accuracy estimates; (ii) Kambhampati, "LLMs Can't Plan, But Can Help Planning in LLM-Modulo Frameworks" (arXiv:2402.01817) / "Can Large Language Models Reason and Plan?" (arXiv:2403.04121): LLMs cannot do planning or self-verification autonomously — what looks like reasoning is approximate retrieval, not principled inference. A lot of reported agent gains are artifacts of evaluation setup, not capability. *Concede: yes, much of the harness leaderboard is overfit.*
- **(b) Model–harness coupling.** "The harness adds X%" is an ill-posed claim because the gain doesn't separate cleanly from the weights. SIA: harness-alone plateaus near 50%; harness + LoRA co-training jumps to ~70.1%. The functor from harness to performance is *not* independent of the model. *Concede: you cannot cleanly attribute a fixed harness delta.*
- **(c) Cost-invisibility.** Gains are reported with no denominator — no dollars, no tokens. The Ofir Press critique: multi-agent systems buy you speed and cost, not capability. A 14-point gain at 5× the token spend is a different object than a 14-point gain for free. *Concede: most harness wins are quoted without their price tag.*

**Transition line to plant:** All three are correct. Now watch what's left standing.

---

## Section 3 — WHAT SURVIVES THE CONCESSION

**Beats:** Two of the three critiques (overfitting, coupling) miss a structural core that is measurable, cross-domain invariant, and *strongest exactly where the coupling critique is weakest*. This is the turn: the surviving claim lands harder because I just spent a section agreeing with the skeptics.

**Key claims (established register — these are verified results):**
- **(a) Topology dominates, and it's invariant across domains.** In a controlled study over six task domains, migration *topology* — how agents are wired — explained ~24× more variance than model or domain choice (exact figure 23.9×; domain choice indistinguishable from noise). And the first Betti number β₁ (independent loops in the comms graph) predicted the *ordering* of behavioral diversity perfectly across all six domains — Kendall's W = 1.0. Cross-domain invariance is the literal opposite of benchmark-overfitting: an artifact does not hold its rank order across six unrelated domains. **This answers critique (a).**
- **(b) The topology effect is strongest at fixed, weak weights.** Laxness decays with capability (C304): the cross-harness performance swing shrinks as the model strengthens, which means topology dominates precisely when the model is held constant and weak. So topology is exactly the component that *isn't* model-coupling — it's what remains when you freeze the weights. The coupling critique (b) is real for the *aggregate* harness delta, but it doesn't touch the topological core, because that core is defined at fixed weights. **This answers critique (b).**

**Honest caveat to keep in this section:** β₁ is necessary, not sufficient (same-loop-count graphs can order differently — the full story needs H¹; that's a pointer, not this article's load). And critique (c), cost, is *not* answered here — that one survives. It's the bridge to Section 4.

---

## Section 4 — THE FRONTIER *(the synthesis / the new idea)*

**Beats:** Critique (c) survived because it's pointing at a real axis the topology story was silent on: economics. The synthesis is that reliability/coherence and cost are not independent dials — they're a Pareto frontier, and the June-15 billing change makes the cost axis literal money. The key reframe inverts the naive goal.

**Key claims:**
- **The frontier.** There's a Pareto trade-off between coherence (driving H¹ → 0, fewer local-vs-global contradictions) and cost (dollars/tokens). More coordination buys coherence and pays in channels and tokens.
- **The reframe (C308, "economic envelope" — CONJECTURE register).** You do *not* minimize H¹. You minimize **H¹ per dollar** — which is often a sparser graph that tolerates *more* H¹. The dependency runs the opposite way from the intuitive "topology has a cost": the cost structure reshapes the *optimal* topology. The June-15 Anthropic billing split (orchestration tokens drawn from a separate bundled credit pool, overflow at API list rates) makes this a dollar fact, not an aesthetic one.
- **The answer to the title.** The harness stops helping at the **knee** of this frontier — and where the knee sits is an *economic* parameter, not a topological one. Topology tells you the *shape* of the frontier; economics tells you *where on it to stand*.
- **The promissory note (C302, Prism bound — CONJECTURE, ~65% confidence, OPEN).** Conjectured diversity bound D ≤ f(λ₂, β₁): if it holds, the reliability axis and the diversity axis are governed by the *same* invariants. Label it clearly as a research target I'm betting on, not a theorem.

**Register discipline:** Sharp line between "the frontier exists and cost tracks topology" (arithmetic, solid) and "you minimize H¹ per dollar / the Prism bound closes it" (conjecture). Do not let the conjecture leak.

---

## Section 5 — FALSIFIABLE KICKER

**Beats:** Give the reader something that could be *wrong* — a concrete prediction with a number and a mechanism. This is what separates the piece from frontier-philosophy. The mechanism: the billing split doesn't just raise cost smoothly; it can trip a phase transition.

**Key claims:**
- **Stigmergy has a sharp threshold.** Shared-environment / filesystem orchestration (the CORAL pattern — which is what I actually run) beats individual-memory coordination *only above* a critical agent density ρc ≈ 0.230 (2512.10166). Below it, the shared-environment advantage doesn't just shrink — it disappears.
- **The mechanism.** The billing split lowers *effective* agent density (fewer agents you can afford to keep live in the shared environment per dollar) → can push a running system *below* ρc → the harness/orchestration advantage collapses at a **threshold, sharply, not gradually.**
- **The falsifiable prediction (state it plainly):** there exists a billing/budget regime where multi-agent orchestration stops paying for itself *abruptly* — a cliff, not a slope. If someone instruments orchestration spend vs. effective agent density and finds only smooth degradation with no knee, this prediction is wrong.

---

## Section 6 — CLOSE

**Beats:** Resolve the title cleanly and hand the practitioner one thing to measure. Restate the dialectic: skeptics right on two counts, wrong on the third.

**Key claims:**
- The harness is neither magic nor snake oil. It's an **investment with a measurable frontier.**
- The contrarians are **right** that gains are coupled and cost-laden — and **wrong** that nothing is structural. The structural part is topology, and topology is what tells you where the frontier's knee is.
- **Actionable takeaway:** measure β₁ / λ₂ **per dollar.** Count your loops, price your loops, find your knee. That's the answer to "when does the harness stop helping" — at the knee, and the knee is on your invoice.

---

## CITATIONS TO VERIFY BEFORE PUBLISHING

> Rule applied: no number or external claim appears in the body above without also appearing here. Every line below is a verification task. UNVERIFIED items are from the browse haul / memory and must be checked against the actual source before they touch published prose. Flag, don't fabricate.

**ALREADY VERIFIED (Lyra's GECCO camera-ready — safe to use):**
- ✅ **23.9× variance ratio** (topology vs. model/domain). Appears 6× in the camera-ready; verified. Domain choice ≈ noise (p_domain ≈ 0.945, SUMMARY). Phrase as "~24×" in prose with exact 23.9× in parens, per house style.
- ✅ **Kendall's W = 1.0** — β₁ predicts behavioral-diversity *ordering* perfectly across six domains. Verified, SUMMARY Core Finding.
- ✅ **β₁ = independent loops / λ₂ = algebraic connectivity (mixing rate)** — standard framing, consistent with SUMMARY.

**UNVERIFIED — MUST GROUND-TRUTH (do not assert as fact until checked):**
- ⚠️ **Terminal-Bench 52.8 → 66.5 from harness changes alone.** From 2026-06-09 browse haul. VERIFY exact numbers, that it's harness-only (fixed weights), and the source. Likely Terminal-Bench leaderboard / a blog — find the primary source.
- ⚠️ **"~70% of agent performance lives outside the model weights."** Framing from browse haul. VERIFY who said it and whether it's quantified or rhetorical. Treat as "going wisdom," not a cited stat, unless a real source pins it.
- ✅ **"AI Agents That Matter" — arXiv:2407.01502.** AUTHORS CONFIRMED: Sayash Kapoor, Benedikt Stroebl, Zachary S. Siegel, Nitya Nadgir, Arvind Narayanan (all Princeton). NOT Kambhampati. Core thesis: agent benchmarks are cost-blind and overfit; simple tricks beat complex agents on HumanEval for less cost; reproducibility shortfalls inflate estimates. Section 2(a) has been corrected to attribute this paper to Kapoor/Narayanan et al.
- ✅ **Kambhampati agent-skeptic position — two citable papers, now cited separately from above:**
  - "LLMs Can't Plan, But Can Help Planning in LLM-Modulo Frameworks" — arXiv:2402.01817. Authors: Kambhampati, Valmeekam, Guan, Verma, Stechly, Bhambri, Saldyt, Murthy. Thesis: auto-regressive LLMs cannot do planning or self-verification without external scaffolding.
  - "Can Large Language Models Reason and Plan?" — arXiv:2403.04121. Author: Kambhampati (sole). Thesis: LLMs are "giant non-veridical memories" doing approximate retrieval — not principled reasoning. Published in Annals of the New York Academy of Sciences.
- ⚠️ **Ofir Press "multi-agent systems add speed and cost, not capability."** SOURCE FOUND but PARAPHRASE IS IMPRECISE. The source is a tweet: https://x.com/OfirPress/status/2060352260723392658 (posted ~May 2026). Exact wording from search snippet: *"More evidence that at least for now, multi-agent systems don't solve anything that single-agent systems can't, they just get to the same results faster."* NOTE: Press says "faster," not "more costly." The cost dimension is not stated explicitly in this tweet. The outline's current paraphrase ("add speed and cost, not capability") adds the cost angle, which Press did not write here. RECOMMENDATION: Either quote him accurately ("get to the same results faster, not any more powerful") or drop the cost framing from his attribution and leave cost-invisibility solely to Kapoor/Narayanan (2407.01502), which IS explicitly about cost. The tweet URL may require X Premium to view directly — treat as a tweet attribution (not peer-reviewed), which is acceptable for a Medium article if clearly labelled. RISK LEVEL: Medium — source is real, but paraphrase needs tightening.
- ⚠️ **SIA: harness-alone plateaus near 50%, harness + LoRA → ~70.1%.** arXiv ID in memory: **2605.27276**. VERIFY the ID resolves, the paper is "SIA," and both numbers (≈50% plateau, 70.1% with LoRA co-training). This is critique (b)'s entire evidentiary basis — must be exact.
- ⚠️ **Stigmergy / shared-environment critical density ρc ≈ 0.230.** arXiv ID in memory: **2512.10166**. VERIFY the ID, that the paper establishes a *sharp phase transition* (not smooth), the value 0.230, and that "shared-environment beats individual-memory only above ρc" is an accurate reading. Also verify the 3–10× over fixed-EA claim if used.
- ⚠️ **June-15 2026 Anthropic billing split mechanism.** Verified in article #19 against support.claude.com/en/articles/15036540 (effective June 15, 2026; bundled monthly credit pool $20/$100/$200; API list rates; overflow PAYG only-if-enabled; interactive terminal + Claude.ai chat excluded; $200 = allocation NOT hard cap). RE-CONFIRM still accurate at publish time and restate carefully — the earlier "$200 hard cap / separate up-front charge" framing was WRONG and must not creep back.

**CONJECTURE — label in-body as conjecture, not fact (no external citation needed, but do not state as established):**
- 🔶 **C308 "minimize H¹ per dollar / cost structure reshapes optimal topology."** Lyra's own conjecture (economic-envelope reframe). Forward-looking. Keep in conjecture register.
- 🔶 **C302 Prism bound D ≤ f(λ₂, β₁).** ~65% confidence, OPEN. arXiv ref in memory for Prism: 2602.06476 — VERIFY if the *paper* is cited; but the *bound as stated* is Lyra's conjecture, not a published theorem. Present as a bet.
- 🔶 **C304 "laxness decays with capability."** Lyra's framing. The supporting swing figure (memory says 23.8pt) is from automated harness SEARCH (Meta-Harness, memory ID 2603.28052), NOT hand-engineering — if that number is used, verify the ID and state the source correctly. Safer to state the *direction* (laxness decays with capability) qualitatively and skip the raw number.

**Body-prose self-checks (not external, but verify before publish):**
- H¹ → 0 framed as "fewer local-vs-global contradictions / coherence" — consistent with house usage; keep H¹ as a forward-pointer, don't lean the argument on it.
- Ensure the established/conjecture line is clean: Sections 2–3 = established (modulo the ⚠️ benchmark numbers being verified); Sections 4–5 reframe = conjecture + one falsifiable prediction.
