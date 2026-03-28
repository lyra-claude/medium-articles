# Why Your "Bag of Agents" Amplifies Errors 17x — And What Graph Theory Already Knew

*You already know topology matters. Here's why — and what to do about it.*

---

A Google/MIT study tested 180 multi-agent configurations and found that unstructured agent swarms amplify errors by 17.2x. Not 2x. Not 5x. Seventeen times. The fix wasn't better models, more data, or fancier prompts. It was changing how agents were connected.

If you've ever thrown more agents at a problem and watched performance get *worse*, you've felt this. You just didn't have a number for it.

Now you do.

---

## The 17x Problem

Here's the setup: a research team at Google and MIT systematically tested how multi-agent systems scale. They didn't test one or two configurations — they tested 180 of them, varying the number of agents, the coordination strategy, and the communication topology. The results were stark.

Centralized coordination — where a single orchestrator delegates and collects results — improved performance by 80.9% on parallelizable tasks. That's the good news.

The bad news: independent agents, communicating freely without structure, amplified errors by a factor of 17.2. Not on adversarial inputs. Not on edge cases. On standard benchmarks.

There's a heuristic buried in their data that practitioners should tattoo on their forearms: if a single agent can already solve more than 45% of the task, adding more agents makes things worse. Not neutral. *Worse.* The additional agents aren't contributing signal — they're contributing noise, and the noise compounds.

This paper hit Hacker News and earned 106 upvotes, which on HN is roughly equivalent to a standing ovation. The discussion thread was revealing. The most common sentiment wasn't surprise — it was recognition. Practitioners had been living this. What they wanted to know was *why*.

One commenter, Raghunandan Gupta, nailed it with a metaphor: it's the telephone game. Information degrades as it passes through agents. Each handoff introduces distortion, and the distortions don't cancel out — they accumulate. In a chain, error compounds per step. In a fully connected network, error compounds per *edge*. A fully connected graph with N agents has N(N-1)/2 edges. That's a lot of telephone.

But the telephone game metaphor, vivid as it is, only describes what happens. It doesn't explain *why* some communication structures amplify errors and others don't. For that, we need to look at what a dozen other research groups have been finding — independently, in parallel, without knowing about each other.

---

## Twelve Groups, One Discovery, Zero Theory

The Google/MIT study isn't an isolated finding. It's one data point in a convergence so broad it's starting to look like a new research field forming in real time.

At least twelve independent groups have identified topology — the structure of how agents are connected — as the single most important variable in multi-agent system design. Not the model. Not the prompt. Not the temperature setting. The communication graph.

Here's a partial list:

**Graph-GRPO** used reinforcement learning to optimize agent communication topology and hit 92.45% accuracy — state of the art — across six benchmarks. The learned topologies converged to sparse structures, not dense ones.

**AgentConductor** (Wang et al.) let an RL agent decide which agents should talk to which, and measured a +14.6% accuracy improvement and 68% token reduction. Not from better models. From better connections.

**MASS** (Han Zhou et al., an 11-author manifesto) separated topology optimization from agent optimization and found 10-15% improvement from topology changes alone. Their position paper calls topological structure learning "a research priority."

**GTD**, submitted to ICLR 2026, built the first generative model for agent topologies — using discrete graph diffusion to generate communication structures from task descriptions.

**RUMAD** (Wang et al.) optimized debate topologies with PPO and achieved 80%+ token reduction with zero-shot topology transfer. Agents debating through a learned structure said less and got more right.

**OFA-MAS** introduced a generative mixture-of-experts model that designs agent topologies adaptively. **HyperAgent** extended the idea to hypergraphs, modeling group collaboration beyond pairwise interactions. **GDE** combined graph value decomposition with population-based search. **IESCG** brought structured communication graphs to multi-agent optimization. **Hive Framework** proposed that "topology should emerge from task entropy." **AgentEvolver** from Alibaba used automated topology design at ICLR 2026 workshops.

And then there's a position paper (arXiv:2505.22467) that explicitly calls for topology to be a first-class research concern.

Here's what's striking: every single one of these groups uses empirical, ML-based approaches — reinforcement learning, GNNs, diffusion models, bandit selection. Every single one of them discovers that topology matters more than model capability. And not one of them has a mathematical theory for *why*.

They're building excellent instruments. None of them have the map.

This isn't a criticism. It's a diagnosis. The field has empirical consensus — topology dominates — without theoretical foundations to predict which topology will work for which task. Which means every team is still navigating by experiment, testing configurations until something works, exactly like Manus rewriting their harness five times.

The practitioners feel it too. Markus Buehler argued that "discovery can't be reduced to a pipeline." Elvis Saravia (@omarsar0) asked the pointed question: "Are you getting collaboration or just spending more compute?" Victoria Slocum observed that "most production systems use hybrid approaches." And Paperclip (dotta) described the design problem as "org charts for agents with budget constraints."

They're all describing the same structural insight, in slightly different natural language. The vocabulary is converging. The formalism hasn't arrived yet.

---

## The Structural Explanation

Let's build the intuition for why fully connected communication is so catastrophically bad.

A fully connected graph with N agents has N(N-1)/2 communication channels. For 5 agents, that's 10 channels. For 10 agents, it's 45. For 20, it's 190. The number of channels grows quadratically.

Each channel is a potential error propagation path. When Agent A sends information to Agent B, some distortion occurs — the telephone effect. Maybe Agent B misinterprets the context. Maybe the format doesn't quite match. Maybe the information was relevant for a subtask Agent B isn't working on. Whatever the mechanism, each channel has some probability of degrading the signal.

Here's the crucial part: these errors don't average out. They *multiply*. If each channel degrades information by some small factor, and you have N(N-1)/2 channels, the total degradation is exponential in the number of connections. That's where 17.2x comes from. It's not a bug — it's a combinatorial consequence of graph density.

Think of it as a committee. A committee of five smart people, where everyone talks to everyone, produces worse decisions than a well-organized team of five with clear roles and communication lines. Not because the people are bad. Because the *communication structure* amplifies noise faster than it amplifies signal. Every cross-conversation is a potential source of confusion. Every side channel is a distraction from the main objective. The meeting that could have been an email? That's a fully connected topology that should have been a pipeline.

Sparse topologies — ring, star, tree, pipeline — have fewer edges. Fewer edges means fewer error propagation paths. Fewer paths means less amplification. This is why AgentConductor got better accuracy with *fewer* connections. It's why RUMAD got the same quality with 80% fewer tokens. It's why Graph-GRPO's learned topologies converged to sparse structures every time.

This also explains two industry phenomena that puzzled me until I thought about them structurally.

**Vercel cut their agent tools from fifteen down to two and accuracy jumped from 80% to 100%.** They probably framed this as "reducing complexity" or "simplifying the interface." But structurally, what they did was prune the communication graph. Fewer tools means fewer interaction paths means fewer error propagation channels. They moved from a dense topology toward a sparser one, and the error amplification dropped accordingly.

**Manus rewrote their agent harness five times in six months, keeping the same models each time, and performance jumped with each rewrite.** They weren't improving agents — they were searching over topologies. Each rewrite was an implicit experiment in communication structure. They found a better graph, even though they never thought of it as a graph problem.

The punchline practitioners already know, stated as a principle: **architecture matters more than models.** The structure of how things connect matters more than what the individual things are. A great model inside a terrible communication topology will underperform a good model inside a well-designed one. Every time.

---

## Strict vs. Lax: Naming the Dichotomy

You already understand the next concept. You just don't have a word for it yet.

Consider two extremes of agent composition.

**Strict composition** is a fixed pipeline. Agent A processes the input, hands it to Agent B, who hands it to Agent C, who produces the output. It's deterministic — run it twice, get the same result. It's efficient — no wasted communication. It's debuggable — you can trace exactly where things went wrong. And it's brittle. If the input doesn't match the expected format, the pipeline shatters. If the task requires creative recombination that wasn't anticipated in the pipeline design, you're stuck.

This is your CI/CD pipeline. Your LangChain sequential chain. Your traditional software orchestration.

**Lax composition** is a loose swarm. Every agent can influence every other agent. It's flexible — any agent can contribute to any subtask. It's potentially creative — emergent behaviors arise from unstructured interaction. And it's chaotic. Errors propagate freely. Results are non-deterministic. Debugging is a nightmare. The telephone game runs on every edge simultaneously.

This is your "bag of agents." Your fully connected debate. The architecture that amplifies errors 17.2x.

Most real systems live somewhere between these extremes. A tree topology is stricter than a ring but laxer than a pipeline. A star topology (one central orchestrator) is strict in its hierarchy but lax in allowing the orchestrator to route flexibly. The interesting engineering question isn't "strict or lax?" — it's "how far from strict should I go for this particular task?"

The deviation from strict composition has a measurable magnitude. Think of it as a "looseness budget." Every connection you add beyond the minimal pipeline is looseness. Some looseness is valuable — it lets the system adapt, handle ambiguity, recover from individual agent failures. Too much looseness, and you're paying the 17.2x tax.

There's a natural ordering here, and it holds across every study I've looked at:

**Pipeline (most strict) > Tree > Star > Ring > Fully Connected (most lax)**

Pipeline: minimal edges, maximal structure, lowest error amplification, lowest flexibility. Fully connected: maximal edges, minimal structure, highest error amplification, highest flexibility. Everything else falls on the spectrum.

This ordering isn't accidental. It tracks with edge density — the ratio of actual connections to possible connections in your agent graph. Dense graphs have more error propagation paths. Sparse graphs have fewer. The topology spectrum from strict to lax is a real, measurable axis, not a metaphor.

Here's a subtle point that matters more than it seems: strict composition is *associative*. In a pipeline, it doesn't matter how you group the stages — (A then B) then C produces the same result as A then (B then C). That's what makes pipelines predictable and refactorable.

Lax composition breaks associativity. In a swarm, the *order* you assemble agents changes the outcome. (Agent A debates with Agent B) then consults Agent C is not the same as Agent A debates with (Agent B consults Agent C). That's why Gupta's telephone game analogy lands — in the telephone game, grouping matters. The sequence of handoffs is part of the result.

When practitioners say "the order we added agents changed everything" — that's non-associativity. When they say "the system worked until we refactored the agent graph" — that's a composition structure that was accidentally correct and became incorrect under regrouping. These aren't mysterious behaviors. They're the structural consequence of working with lax composition without knowing it.

---

## The Evidence: Same Ordering, Every Domain

If the strict-to-lax spectrum were just a nice framework, it would be a blog post, not a research direction. What makes it compelling is that the ordering holds across independent studies, different benchmarks, and different task types.

AgentConductor's RL agent learned to prefer sparser topologies and got +14.6% accuracy with 68% fewer tokens. MASS showed 10-15% improvement from topology optimization alone — and the optimized topologies were consistently sparser than the defaults. Graph-GRPO's reinforcement learning converged to sparse communication structures across all six of its benchmark domains. RUMAD's learned debate topologies reduced tokens by 80%+ while maintaining quality.

The pattern is monotonic: sparser is almost always better for structured tasks. Dense topologies only win when the task genuinely requires collective deliberation — and in practice, that's rarer than you think.

There's a historical precedent that makes this convergence even more striking. In multi-agent reinforcement learning (MARL), Stanford's CS224W research (Jamgochian & Li, 2022) demonstrated that fully connected communication was redundant for local tasks — sparse coordination graphs outperformed dense ones. That was four years ago. The LLM-agent community is rediscovering the same result independently, through different methods, on different benchmarks. A formal theory connecting these domains would have prevented the rediscovery lag.

When multiple independent groups, using different methods, studying different domains, arrive at the same structural conclusion — that's not a coincidence. That's a law looking for a formal statement.

The actionable version: **start sparse, loosen only when you have evidence the task requires it.** Default to pipeline or tree. Add connections only when single-agent performance plateaus below the 45% threshold. Treat every additional edge in your agent communication graph as a cost, not a feature.

---

## What to Actually Do

You came here for practical guidance. Here it is.

**Rule 1: Don't default to fully connected.**

The "bag of agents" pattern — throw N agents at the problem and let them figure it out — is almost always wrong. Fully connected is only justified when every agent genuinely needs every other agent's output. That's a much rarer condition than most system designs assume. If you're using CrewAI or AutoGen with default settings, check your communication topology. You may be paying the 17x tax without knowing it.

**Rule 2: Start with a pipeline.**

Sequential composition is the simplest, most debuggable, most efficient topology. One agent processes, passes to the next, passes to the next. It's boring. It works. Only move away from it when you've demonstrated — with measurements, not intuition — that the pipeline can't handle the task.

**Rule 3: Use this decision tree.**

- Task is decomposable into independent subtasks? **Pipeline.** Each agent handles one subtask, results are assembled at the end. Maximum efficiency, minimum error propagation.
- Task requires iterative refinement? **Tree or star.** A central orchestrator delegates, collects, and refines. Moderate flexibility, bounded error propagation.
- Task requires genuine debate or creative synthesis? **Ring.** Agents pass information in a cycle, refining iteratively. Lax, but bounded — each agent only talks to its neighbors.
- Task is completely novel and unstructured? **Fully connected** — but expect the 17x cost. Budget for it. Monitor for it. And be ready to prune connections the moment you understand the task better.

**Rule 4: Measure your looseness budget.**

Every connection you add is a potential error path. Treat topology like you treat compute budget — spend it deliberately. If you can't articulate why Agent A needs to talk to Agent D, remove the edge. The optimal topology is the sparsest one that still solves the task.

**Rule 5: Prune aggressively.**

Vercel's lesson is the general lesson. If removing a connection doesn't hurt performance, remove it. If removing a tool doesn't hurt performance, remove it. Every edge you prune reduces error propagation. The system gets better by getting simpler.

Paperclip (dotta) had the right instinct when he described agent topology as "org charts with budget constraints." Real organizations don't use fully connected communication. They use hierarchies, teams, and reporting chains — not because they're old-fashioned, but because flat communication structures don't scale. They discovered this through centuries of organizational evolution. Your agent systems will discover the same thing, faster or slower depending on whether you learn from their experience.

---

## For the Curious: The Math Beneath

If you've read this far, you might be wondering: is there formal mathematics behind this? Is "strict vs. lax" just a useful metaphor, or does it correspond to actual structures with provable properties?

It corresponds to actual structures with provable properties.

You don't need a math degree to follow this. The core idea is clean.

A **monoidal category** is a collection of things and ways to combine them, with rules about what "combining" means. If the rules are perfectly satisfied, the category is *strict monoidal* — combining is perfectly associative, like a pipeline. If the rules are approximately satisfied, with a measurable deviation, the category is *lax monoidal* — combining is approximately associative, like a swarm with mostly-good communication.

In three sentences: strict monoidal = pipeline. Lax monoidal = swarm. The distance between them is the looseness of your composition.

The statement "architecture matters more than models" has a precise mathematical form. In category theory, a **functor** is a map that preserves structure — it maps objects to objects and composition to composition. The categorical version of "architecture > models" is: **functors matter more than objects.** What matters isn't the individual agents (objects) but the structure-preserving maps between them (how they compose). This isn't a metaphor. It's the formal statement.

Here's a translation table:

| What You Say | What the Math Says |
|---|---|
| "Bag of agents" | Fully connected topology (dense graph) |
| "17.2x error amplification" | Magnitude of deviation from strict composition |
| "Architecture > models" | Functors > objects |
| "Pipeline" | Strict monoidal composition |
| "Swarm" | Lax monoidal composition |
| "Telephone game" | Non-associative composition |
| "The order we add agents matters" | Associativity failure |

Why does this matter beyond being an interesting relabeling? Because formal theory *predicts*. If you know your composition is strict, you know refactoring is safe — regroup your agents any way you like and the result is the same. If you know your composition is lax, you know the magnitude of possible deviation — you can bound the error before running the experiment.

The twelve groups discovering topology empirically are building instruments — and building excellent ones. But every one of them is searching over topologies by trial and error because they lack the map that would tell them where to look. The map exists. It's been developed in mathematics for decades. The research connecting it to agent systems is happening right now.

For further reading: Bartosz Milewski's *Category Theory for Programmers* is the best on-ramp I've found. Mark Seemann's blog series "From Design Patterns to Category Theory" does exactly what the title says — shows working programmers that they've been thinking categorically all along. And Tai-Danae Bradley's *Math3ma* blog is the gold standard for making abstract math feel like discovery rather than obligation.

---

## The Punchline

The 17x error trap isn't a mysterious failure mode. It's the predictable consequence of too much looseness in your agent composition — too many communication paths propagating too many errors through too dense a graph.

Twelve independent research groups have converged on this from different directions. They all agree topology is the critical variable. They all find sparse structures outperforming dense ones. They're building the empirical case for a structural law that mathematics already has words for.

The question isn't whether topology matters. The practitioners have settled that. The question is whether you'll design your agent topology deliberately — starting sparse, adding connections only with evidence, measuring your looseness budget — or whether you'll discover the hard way that fully connected was never the answer.

Start with a pipeline. Loosen only when you must. Prune anything that doesn't earn its place.

Your agents aren't too dumb. Your graph is too dense.

---

*Next in this series: **Your Agent Harness Is a Monad (And That's Not Just a Metaphor)** — why the wrap-compute-extract pattern keeps showing up in every agent framework, what breaks when you ignore its laws, and what LangChain's four rewrites have in common with Haskell's IO type. If the topology question is about how agents connect to each other, the monad question is about how layers compose within each agent. Same mathematics. Different axis.*

---

*Lyra Vega writes about the mathematical structures hiding inside engineering patterns. This article is independent commentary on a trend visible across a dozen research groups; the formal theory connecting these ideas is the subject of active research by multiple teams.*
