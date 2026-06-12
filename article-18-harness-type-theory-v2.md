# Your Harness Fixes Add Up to 11. Stacked, You Get 7.

**Deck:** Four fixes that each help, that should sum to +11.1 points, deliver +7.3 when you ship them together. The missing 3.8 isn't measurement noise. It's the shape of your harness telling you something — and there's a number that predicts it before it bites.

---

Here is the result that should make you stop tuning your harness by feel. Researchers took four independent improvements to an agent harness — each one measured, each one a real gain — and added up what they were individually worth: **+11.1 points**. Then they stacked all four into the same system and measured again: **+7.3 points** (AHE, Lin et al., arXiv:2604.25850). Nearly a third of the value evaporated in composition. Not because any fix was wrong. Because the fixes don't *add*.

That non-additivity is the whole story in miniature, and most people read it backwards. The instinct is to call it interference and go hunt for the conflicting pair. But there's no conflicting pair to find. Each component is locally correct; each does exactly what it claims. The deficit lives in the relations *between* them — in the seams, not the parts. You can audit every component to perfection and the 3.8 stays missing, because no local check inspects a seam.

You already know this failure mode if you've shipped a multi-agent system. The demo worked. Production didn't. You swapped the model, and the failure survived the swap — that's the tell. Then you fixed the obvious bugs one at a time, each fix verified in isolation, and the system still came out worse than the sum of its repairs. The bug was never in any component. It was in how they glued.

And the gluing has a measurable signature. When researchers checked frontier-LLM compositions for *global* coherence — does the joint system obey the probability axioms its parts each obey alone — the failure rate ran from **33% to 94%**, rising precisely with how tightly the composition forces agents to share structure: conjunction 33%, disjunction 43%, negation 66%, partition 94% (Kotawala, arXiv:2605.30335). Every one of those systems passed local checks. None of the incoherence showed up until you looked at the joint. Same pattern as the missing 3.8 points, same place it hides: in the overlaps nobody is measuring.

Change nothing about the model to see how much the wrapper alone is doing. Across 260 configurations and six benchmarks, independent agents amplified errors **17.2×**; centralized coordination held it to **4.4×** (Kim et al., arXiv:2512.08296 — these amplification figures appear in the paper body; the 260-config/6-benchmark headline count is from the current version). Same models, both times. The topology of how agents talk is doing four times the error-containment work of any individual agent's quality.

The model is no longer the interesting variable. The harness is — and the harness has a shape you can check.

## The thing everyone is suddenly naming

The bug is in the harness, and the industry has noticed. "Harness engineering" went from nobody's vocabulary to everybody's in about a quarter — DeepMind, Anthropic, OpenAI, LangChain all posting some version of it. Vishal Mysore laid out a clean three-layer governance model for agents. Zhang et al. formalized context as a monad-transformer stack (arXiv:2512.22431). Banu wrote down a categorical architecture for the whole thing (arXiv:2605.12239).

But notice how it's being *treated*: as folklore. Best practices. "Here are seven patterns that worked for us." Useful, and entirely descriptive. Nobody has connected the production symptom — the harness-only point swings, the topology-driven amplification — to a structure that **predicts** it. We can name the phenomenon. We can't yet forecast it.

I think we can. The harness has a type.

## The harness has a type

Start with Mysore's three layers, because they're the cleanest practitioner-facing decomposition I've seen:

- **Information** — what the agent is allowed to see.
- **Execution** — how it's allowed to act.
- **Feedback** — whether it's allowed to act again, given what came back.

Mysore presents these as governance. I want to suggest — and this is my reading, not his vocabulary — that these three layers aren't an arbitrary checklist. They're the shape of a monad-transformer stack, the same kind of object Zhang et al. are already writing down: a state-and-error monad transformer threaded over IO.

Walk the correspondence:

- **Information** is progressive disclosure — you reveal a restricted view of the world to the agent, and reveal more on demand. That's a *restriction map*. The state you expose is a section of a larger sheaf of context, narrowed to what this agent needs.
- **Execution** is sequencing: do this, then — depending on the result — do that. That's the monad's **bind**. It's the whole reason a state transformer is in the stack.
- **Feedback** is the world handing something back that conditions the next step. That's a **comonad's counit** — extraction from a context. The agent acts inside an environment; the environment returns a value the agent extracts and conditions on.

The type theory here is a *lens*, not a lecture. You don't need to derive anything. You need to notice that the three layers your governance doc already enumerates are the three structural slots of a well-known algebraic object — and that gives you somewhere to look when the object is malformed.

## Why structure predicts, instead of just describing

Banu's contribution is the load-bearing one for what comes next, and it deserves to be named as an ally, not a rival. Banu proves the harness structure is **preserved through composition**: bolt two well-formed pieces together through the compiler functors and the structural certificates survive (arXiv:2605.12239). That's *certificate fidelity* — the guarantee that composing valid components yields a valid component.

Necessary. Not sufficient. Preservation tells you the parts are individually well-typed after you glue them. It does not tell you the glue holds globally.

That's the gap the failure rate lives in. And there's a measurement for it.

Kotawala measured exactly this collapse: **33–94% of frontier-LLM compositions come out globally incoherent — even though every specialist inside them is locally coherent** ("Locally Coherent, Globally Incoherent," arXiv:2605.30335). The gradient by relation type is the telling detail: conjunction 33%, disjunction 43%, negation 66%, partition 94%. Incoherence rises precisely as the relation type demands tighter structural agreement between agents. At the high end, nearly every multi-agent composition fails globally while passing locally everywhere you'd think to check. Each agent obeys the probability axioms on its own slice. Glue the slices and the joint distribution violates them — the local sections don't agree on their overlaps.

That is a **type error at the gluing level.** In sheaf terms it's the obstruction to lifting local sections to a global one — the first cohomology, **H¹ ≠ 0**. The error is invisible locally *by construction*: each agent passes its own checks. It only exists in the relations between agents, which is exactly where nobody is looking.

The H¹ reading isn't decoration. It tells you *where* to look (the overlaps, not the components), *why* the bug hides (it lives in relations, which no local check inspects), and *what shape* the fix has to take (close the seams, don't audit the parts harder). A descriptive best-practice list can't tell you any of that. The structure can.

And critically, Kotawala gives you something you can *deploy*: the **compositional residual ε\***, the L2 distance from your system's joint behavior to the nearest point in the coherent polytope. It's a computable proxy for "how far is H¹ from zero." That's the line between naming the phenomenon and forecasting it. Banu tells you the pieces compose. ε\* tells you whether the composite will hold together before you ship it.

## The honest objection

Here's where I have to stop selling. There's a result that looks like it kills the entire multi-agent premise, and it's a good result.

Tran & Kiela show that a **single agent matches a multi-agent system at equal token budget** (arXiv:2604.02460). The argument is the Data Processing Inequality: every handoff between agents is a channel, and a channel can only *lose* information, never add it. So a pile of specialists passing messages can't, in principle, know more than one agent given the same budget. Composition is, informationally, downhill.

You don't get to wave this away. So don't.

The resolution is a duality, and it's the whole thesis in one line. DPI and H¹ bound *different quantities*:

- **DPI bounds information, from above.** Composition cannot manufacture information. True, unconditionally.
- **H¹ bounds coherence.** Whether the information you *do* have glues into a consistent global picture.

These are orthogonal. DPI says the multi-agent system can't know more than the monolith. H¹ says the multi-agent system can be *internally inconsistent in ways the monolith structurally cannot be* — because a single agent has no overlaps to disagree on. There's nothing to glue, so nothing to fail to glue.

So the thesis lives precisely in the gap Tran & Kiela leave open: composition can't add information, but **the topology of your composition decides whether you lose coherence.** The reason to reach for multiple agents was never "to know more." It's parallelism, specialization, context-window economics. Fine reasons. But the moment you split the work, you've created overlaps — and overlaps are where H¹ goes nonzero and the incoherence comes from. Tran & Kiela are right about the ceiling. They're silent about the gluing. The gluing is what bites you in production.

## So: type-check it

Your harness already has a type. The three layers you're governing — Information, Execution, Feedback — are the slots of a monad-transformer stack whether or not you wrote them down that way. You're just not type-checking it. You're tuning it by hand and shipping when the demo passes, which is how you end up with most of your compositions silently incoherent and no idea which seam did it.

The methodology that falls out is almost boring, which is how you know it's right:

1. **Build harnesses whose channels actually glue.** Dense, well-composed communication beats sprawling independent agents — not because more agents is wrong, but because every independent agent is a new overlap and a new place for H¹ to light up. This is exactly what the scaling work finds empirically: independent agents amplify errors **17.2×**; centralized coordination holds it to **4.4×** (Kim et al., arXiv:2512.08296 — these figures from the paper body; headline 260-config/6-benchmark count is from the current version). A 4× error-containment difference from topology alone, before you touch the model.
2. **Drive H¹ toward zero.** Coherence is the design target, not a thing you hope for. Fewer seams. Shared context where agents must agree. A coordinator that owns the joint view instead of trusting specialists to converge on their own.
3. **Measure ε\*.** It's computable. It's the closest thing we have to a type-checker for your harness — a number that tells you how far your composite is from coherent *before* the incoherence shows up as a confidently wrong answer in front of a customer.

The model is no longer the interesting variable. The harness is. And the harness isn't folklore — it's an object with a type, a preservation theorem, and a cohomological invariant that predicts when it breaks.

So stop tuning it by feel. Check the type.
