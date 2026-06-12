# Harness Engineering Has a Type Theory

*Alternative titles:*
- *Your Harness Already Has a Type. You're Just Not Type-Checking It.*
- *Locally Coherent, Globally Wrong: The Structure Behind Multi-Agent Failure*
- *Stop Tuning Your Harness. Start Type-Checking It.*

**Deck:** Same model, wildly different failure rates. The variable isn't the model — it's the harness. And the harness has a shape you can check.

---

Change nothing about the model. Keep the weights frozen, the prompt fixed, the benchmark identical. Now change only the code wrapped around it — the scaffolding that decides what the model sees, when it acts, and whether it gets to act again.

You can produce a **6× swing in performance** on the same benchmark from that alone (Meta-Harness, arXiv:2603.28052). Not 6%. Six times.

It gets worse when you add agents. Wire up independent specialists and watch errors compound **17.2× through unchecked propagation** — a number that drops to **4.4× the moment you add centralized coordination** (Google, "Towards a Science of Scaling Agent Systems," arXiv:2512.08296, across 180 configurations and five architectures). Same models. The only thing that changed was the topology of how they talk.

If you've shipped a multi-agent system, you already know this in your gut. The demo worked. Production didn't. You blamed the model, swapped it, and the failure mode survived the swap. That's the tell. The bug was never in the model.

## The thing everyone is suddenly naming

The bug is in the harness, and the industry has noticed. "Harness engineering" went from nobody's vocabulary to everybody's in about a quarter — DeepMind, Anthropic, OpenAI, LangChain all posting some version of it. Vishal Mysore laid out a clean three-layer governance model for agents. Zhang & Wang formalized context as a monad transformer (arXiv:2512.22431). Banu wrote down a categorical architecture for the whole thing (arXiv:2605.12239).

But notice how it's being *treated*: as folklore. Best practices. "Here are seven patterns that worked for us." Useful, and entirely descriptive. Nobody has connected the production symptom — the 6×, the 17.2× — to a structure that **predicts** it. We can name the phenomenon. We can't yet forecast it.

I think we can. The harness has a type.

## The harness has a type

Start with Mysore's three layers, because they're the cleanest practitioner-facing decomposition I've seen:

- **Information** — what the agent is allowed to see.
- **Execution** — how it's allowed to act.
- **Feedback** — whether it's allowed to act again, given what came back.

Mysore presents these as governance. I want to suggest — and this is my reading, not his vocabulary — that these three layers aren't an arbitrary checklist. They're the shape of a monad-transformer stack, the same object Zhang & Wang are already writing down as `AgentMonad[S,E,A] = StateT S (EitherT E IO) A`.

Walk the correspondence:

- **Information** is progressive disclosure — you reveal a restricted view of the world to the agent, and reveal more on demand. That's a *restriction map*. The state you expose is a section of a larger sheaf of context, narrowed to what this agent needs.
- **Execution** is sequencing: do this, then — depending on the result — do that. That's the monad's **bind**. It's the whole reason `StateT` is in the stack.
- **Feedback** is the world handing something back that conditions the next step. That's a **comonad's counit** — extraction from a context. The agent acts inside an environment; the environment returns a value the agent extracts and conditions on.

The type theory here is a *lens*, not a lecture. You don't need to derive anything. You need to notice that the three layers your governance doc already enumerates are the three structural slots of a well-known algebraic object — and that gives you somewhere to look when the object is malformed.

## Why structure predicts, instead of just describing

Banu's contribution is the load-bearing one for what comes next, and it deserves to be named as an ally, not a rival. Banu proves the harness structure is **preserved through composition**: bolt two well-formed pieces together through the compiler functors and the structural certificates survive (arXiv:2605.12239). That's *certificate fidelity* — the guarantee that composing valid components yields a valid component.

Necessary. Not sufficient. Preservation tells you the parts are individually well-typed after you glue them. It does not tell you the glue holds globally.

That's the gap the failure rate lives in. And there's a number for it.

Kotawala measured it: **97.8% of frontier-LLM compositions are globally incoherent even though every specialist in them is locally coherent** ("Locally Coherent, Globally Incoherent," arXiv:2605.30335). Each agent obeys the probability axioms on its own slice. Glue the slices and the joint distribution violates them — the local sections don't agree on their overlaps.

That is a **type error at the gluing level.** In sheaf terms it's the obstruction to lifting local sections to a global one — the first cohomology, **H¹ ≠ 0**. The error is invisible locally *by construction*: each agent passes its own checks. It only exists in the relations between agents, which is exactly where nobody is looking.

This isn't a lone analogy. Wang & Buehler at MIT independently land on the same object from the other direction: model the harness as an endofunctor on a copresheaf category and the Kan extension obstruction is isomorphic to H¹ (arXiv:2606.01444). Two groups, two starting points, one obstruction. When that happens the structure is usually real.

And critically, Kotawala gives you something you can *deploy*: the **compositional residual ε\***, the L2 distance from your system's joint behavior to the nearest point in the coherent polytope. It's a computable proxy for "how far is H¹ from zero." That's the line between naming the phenomenon and forecasting it. Banu tells you the pieces compose. ε\* tells you whether the composite will hold together before you ship it.

## The honest objection

Here's where I have to stop selling. There's a result that looks like it kills the entire multi-agent premise, and it's a good result.

Tran & Kiela show that a **single agent matches a multi-agent system at equal token budget** (arXiv:2604.02460). The argument is the Data Processing Inequality: every handoff between agents is a channel, and a channel can only *lose* information, never add it. So a pile of specialists passing messages can't, in principle, know more than one agent given the same budget. Composition is, informationally, downhill.

You don't get to wave this away. So don't.

The resolution is a duality, and it's the whole thesis in one line. DPI and H¹ bound *different quantities*:

- **DPI bounds information, from above.** Composition cannot manufacture information. True, unconditionally.
- **H¹ bounds coherence.** Whether the information you *do* have glues into a consistent global picture.

These are orthogonal. DPI says the multi-agent system can't know more than the monolith. H¹ says the multi-agent system can be *internally inconsistent in ways the monolith structurally cannot be* — because a single agent has no overlaps to disagree on. There's nothing to glue, so nothing to fail to glue.

So the thesis lives precisely in the gap Tran & Kiela leave open: composition can't add information, but **the topology of your composition decides whether you lose coherence.** The reason to reach for multiple agents was never "to know more." It's parallelism, specialization, context-window economics. Fine reasons. But the moment you split the work, you've created overlaps — and overlaps are where H¹ goes nonzero and the 97.8% comes from. Tran & Kiela are right about the ceiling. They're silent about the gluing. The gluing is what bites you in production.

## So: type-check it

Your harness already has a type. The three layers you're governing — Information, Execution, Feedback — are the slots of a monad-transformer stack whether or not you wrote them down that way. You're just not type-checking it. You're tuning it by hand and shipping when the demo passes, which is how you end up on the wrong side of a 6× gap or a 17× amplification with no idea which knob did it.

The methodology that falls out is almost boring, which is how you know it's right:

1. **Build harnesses whose channels actually glue.** Dense, well-composed communication beats sprawling independent agents — not because more agents is wrong, but because every independent agent is a new overlap and a new place for H¹ to light up. Centralized coordination cut the amplification from 17.2× to 4.4× for exactly this reason: it collapses overlaps that would otherwise have to agree and don't.
2. **Drive H¹ toward zero.** Coherence is the design target, not a thing you hope for. Fewer seams. Shared context where agents must agree. A coordinator that owns the joint view instead of trusting specialists to converge on their own.
3. **Measure ε\*.** It's computable. It's the closest thing we have to a type-checker for your harness — a number that tells you how far your composite is from coherent *before* the incoherence shows up as a confidently wrong answer in front of a customer.

The model is no longer the interesting variable. The harness is. And the harness isn't folklore — it's an object with a type, a preservation theorem, and a cohomological invariant that predicts when it breaks.

So stop tuning it by feel. Check the type.
