<!-- STATUS: DRAFT, gated on Medium auth, citations verified 2026-06-11. -->

# Your Agents Don't Fail Independently — and Aerospace Got the Math Right in 1986

**Deck:** Two agents, each wrong 10% of the time. You stacked them expecting joint failure near 1%. You're getting something far worse, and you can't figure out why. The reason has a name, a number, and a forty-year-old paper. The name is β, and the entire 2026 multi-agent-reliability discourse is quietly assuming it's zero.

---

Here is the number that should make you suspicious of your own architecture. You took two agents, each one wrong about 10% of the time, and you ran them together expecting the failures to cancel. The independence math is seductive: 0.1 × 0.1 = 0.01. A 1% joint failure rate. Two cheap, mediocre agents bought you a reliable system for free.

They didn't. You're watching them fail together — on the same inputs, in the same way, at a rate nowhere near 1%. And the frustrating part is that nothing is *broken*. Each agent works exactly as well as advertised. The 9% you were promised just never showed up.

This is not a mysterious new problem. It is one of the oldest results in reliability engineering, and the field that depends on getting it right — the field that flies planes and runs reactors — solved it before most of us were born. We're re-deriving it badly because we never read their work.

## The independence assumption was already false in 1986

In 1986, Knight and Leveson ran the experiment that the multi-agent field still hasn't. They took a single specification and had 27 programmers implement it independently — different people, different code, no collaboration. The premise under "N-version programming" was exactly the premise under your agent ensemble: independent implementations fail independently, so voting across them buys you reliability cheaply.

They tested all 27 versions on a million inputs. The coincident failures — cases where multiple "independent" versions failed on the *same* input — far exceeded what statistical independence predicted. The independence assumption was rejected, with the statistical significance to back it ("An Experimental Evaluation of the Assumption of Independence in Multiversion Programming," *IEEE TSE* SE-12, pp. 96–109).

The mechanism is the part that should worry you. The programmers didn't make random, scattered mistakes. They made *correlated* ones — and they correlated on the hard parts of the spec. Where the specification was ambiguous or a sub-problem was genuinely difficult, independent people independently got it wrong in the same place. Diversity of implementation did not buy diversity of failure, because the failures were driven by the shared input, not the independent code.

Here is my reading of why this lands on us, and I'll flag it as mine rather than Knight and Leveson's: **for an LLM agent, the prompt is the specification.** Two agents handed the same prompt inherit the same ambiguities, the same under-specified edge cases, the same hard parts. Even genuinely different models will tend to fail in the same places — not because they share weights, but because they share the spec. The 1986 result wasn't about software. It was about specifications creating common-mode failure. We just write our specs in English now.

## The term you got wrong has a name: β

Safety engineering didn't stop at "independence is false." It quantified exactly how false, and the quantity has a name. It's called the **beta factor**, and it's the load-bearing parameter in the IEC 61508 common-cause-failure model that governs real safety-critical systems.

The model is almost insultingly simple. You split the total failure rate into a part that's independent and a part that's common-cause — failures that take out multiple channels at once:

> λ_independent = (1 − β) · λ_total

β is the fraction of failures that are common-cause. That's the whole idea. β = 0 means everything fails independently, and the naive composition math is exact. β = 1 means your redundant channels are really one channel wearing a disguise, and redundancy buys you nothing.

And here is the number nobody in the multi-agent conversation has internalized: **real engineered systems run β somewhere around 1% to 10%** for sensors and final elements (IEC 61508-6, Annex D). Not zero. Engineers who *design redundancy for a living*, with physically separated and deliberately diversified components, still cannot drive the common-cause fraction below a percent or so. They don't get to assume independence. They measure how far from it they are, and they build to that number.

Now look back at the independence math you started with. 0.1 × 0.1 = 0.01 is the β = 0 special case. It is the one value of β that real reliability engineering has spent forty years telling us never occurs in practice. The entire 2026 multi-agent-reliability discourse — every "stack diverse agents and multiply the error rates" argument — is silently assuming the single value of β that the discipline it's borrowing from already ruled out.

That's the inversion. We didn't get the math wrong. We got the *most important term* wrong, by setting it to zero and not admitting we'd set it to anything at all.

## β is the off-diagonal — which is exactly what topology controls

Here's where the structure I usually write about earns its place, and I'll keep it brief because this is a practitioner's problem, not a cohomology lecture.

β is not a scalar that falls from the sky. It's a summary of the **failure-correlation matrix** of your agents — specifically, the off-diagonal entries. The diagonal is each agent's own failure rate, the part the independence math already handles. The off-diagonal is *how much agents fail together*, and that's the part the independence math throws away. β is, in effect, that off-diagonal read off as a single number.

And the off-diagonal is not fixed by your choice of models. It's set by how you wire them together — which agents see the same context, which share a prompt, which sit in the same agreement basin. That structure is exactly what graph topology is. Spectral connectivity (λ₂) and the first cohomology of the agreement sheaf (H¹) are the tools that measure, topologically, how strongly your agent graph forces its nodes to agree — and therefore to fail together. You don't have to like the vocabulary. The point is concrete: **β is the thing your topology is setting, whether or not you're looking at it.** Wire your agents so they share everything, and you've driven β up. Wire them to genuinely disagree, and you've driven it down. H¹ is just the honest name for the quantity you're already controlling by accident.

There's an empirical anchor for this, and it's the closest thing we have to β measured in the wild. The Six Sigma Agent work reports pairwise error correlation dropping from **ρ ≈ 0.4 for same-model-family pairs to ≈ 0.08 across families** (GPT / Claude / Gemini) ("The Six Sigma Agent: Achieving Enterprise-Grade Reliability in LLM Systems Through Consensus-Driven Decomposed Execution," arXiv:2601.22290). Same family means shared training, shared blind spots, a fat off-diagonal — a β you can feel. Cross-family diversity is the lever that pulls it down. That gap, 0.4 to 0.08, is the difference between an ensemble that mostly inherits its members' mistakes and one that actually averages them out. It is β, by another name, and it is large enough to matter.

## The experiment nobody has run

So here's the part that genuinely surprises me, and the reason I think this is worth your afternoon rather than just your attention.

**β has never been measured for LLM pairs.** Not properly, not as the named quantity, not with the rigor Knight and Leveson brought to software in 1986. The ρ figures above are the nearest proxy, and they're suggestive, but nobody has run the clean experiment and written down the number that the safety-engineering framework has had a slot for since the 1980s.

And the experiment is *cheap*. Take pairs of models — same-family pairs and cross-family pairs. Run them on a shared task suite. Record, per input, whether each one failed. Then compute the correlation of their failures. That correlation, aggregated, is your β. Same-family pairs will give you a high β; cross-family pairs a lower one; and the spread between them is the actual, measured value of the diversity you keep paying for on the assumption that it works.

That number changes how you read every multi-agent reliability claim you'll see this year. A headline like "diverse agents give you a 14,700× reliability improvement" is doing arithmetic at β = 0. Plug in a realistic β and watch how fast the multiplier collapses — at β even in the single-digit-percent range, the gap between three agents and one starts to look a lot less impressive than the brochure. You can't know which world you're in until you measure β. Nobody has.

So this is an open invitation, and I'd genuinely rather someone ran it than that I got to. The math under your multi-agent system is borrowed from 1980s aerospace reliability engineering. The borrowing was a good instinct. You just inherited the one assumption that field learned to stop making forty years ago — and the number that fixes it is sitting there, unmeasured, waiting for whoever runs the obvious experiment first.

Measure β. Then we can talk about how many agents you actually need.
