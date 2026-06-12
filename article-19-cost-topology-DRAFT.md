> **STATUS: DRAFT — pending Lyra's review + Medium-auth unblock. Drafted 2026-06-08 by article-drafting agent.**
> Not published. Verify all flagged numbers (see end-of-file NOTES) before posting. Honesty constraints: the precise cost↔topology *inequality* is forward-looking conjecture; the variance and diversity results are established. Do not let the conjecture leak into the established register.

# Your Agent Bill Isn't Quadratic — It's a Topology You Can Measure

*Alternative titles:*
- *Compose Sloppily, Pay More — Now Literally, In Dollars*
- *The Shape of Your Agent Graph Is on Your Invoice*

**Deck:** The cost of a multi-agent system isn't set by how many agents you run. It's set by how they're wired. And as of June 15, that's a line item.

---

For a year the standard worry about multi-agent systems has been that they get expensive in an obvious way: more agents, more tokens, more money, roughly quadratic if everyone talks to everyone. That mental model is about to get a sharper edge.

Starting June 15, Anthropic splits Claude billing so that Agent SDK usage — programmatic calls via `claude -p`, GitHub Actions, and third-party Agent SDK integrations — draws from a separate monthly credit pool bundled into your plan ($20 on Pro, $100 on Max 5x, $200 on Max 20x), consumed at standard API list rates. Interactive terminal use and Claude.ai chat are unaffected. Once that bundled credit is exhausted, requests either stop or spill over to pay-as-you-go at full API rates — if you've enabled overflow billing. Read that carefully. The tokens your *scaffolding* burns — routing, retries, context assembly, the messages agents send each other to stay in sync — now draw from that separate pool first, and once it's gone, they run at full API list prices. The plumbing is now a billed product with its own meter.

Which means a sentence that used to be an architecture-review nicety is now a sentence about money: **the way you wire your agents together is the thing you pay for.** Not how many you have. How they're connected.

That's not a slogan. The connection topology is a structural object, and the structure has been measured. I want to walk you from the invoice back to the shape, and then make a careful claim — careful because part of it is solid and part of it is a bet I'll label as a bet.

## Three ways to wire four agents

Forget agents for a second and just count messages. Say you have four workers who need to coordinate. There are at least three natural ways to connect them, and they bill completely differently.

**The star.** One coordinator in the middle; every worker talks only to the hub. Four workers means four spokes. Coordination messages scale *linearly* with the number of agents — add a fifth worker, you add one spoke. The hub does carry all the traffic (and its context window fills up), but the total number of channels stays small. Cheap to run, in raw message count.

**The ring.** Each agent talks to its two neighbors, information walks around the loop. Also linear in channel count — n agents, n edges — but now coordination *latency* is the cost: news has to travel halfway around the ring before everyone has it, so you pay in rounds, and each round is tokens.

**The full mesh.** Everyone talks to everyone. Now you have n(n−1)/2 channels — the quadratic everyone fears. Four agents: six channels. Eight agents: twenty-eight. Sixteen agents: a hundred and twenty. Every channel is context that has to be assembled, sent, and reconciled, every round. This is the one that turns a working eight-agent demo into a sixteen-agent budget incident.

Same four agents. Same model. Same task. The bill — specifically the *orchestration* bill, the one that just got its own pool — can differ by an order of magnitude depending on which of these three pictures you drew on the whiteboard. The number of agents barely moved. The topology did all the work.

So far this is just graph theory you could have done in your head. Here's where it gets interesting: the *same* structural quantities that govern this cost also govern two things the field already cares about a lot — how reliable the system is, and how much behavioral diversity it sustains. They turn out to be the same handful of numbers.

## The numbers that already predict behavior

Before I touch cost, let me establish what's *measured*, because the whole argument rests on it being real.

In a controlled study across six different task domains, we varied the migration topology — how agents are connected — and also varied the model and the domain, and asked which choice mattered most for outcomes. The answer was lopsided: **topology explained on the order of 24× more variance than model or domain choice** (the precise figure in our camera-ready is 23.9×; domain choice was statistically indistinguishable from noise, p ≈ 0.95). You can read that as: which model you pick is a rounding error next to how you wire them.

And the connection structure didn't just matter in aggregate — it *ordered* the outcomes. A topological invariant called the **first Betti number** β₁ — informally, the number of independent loops in your communication graph — predicted the ranking of behavioral diversity across all six domains *perfectly*. Perfect rank agreement (Kendall's W = 1.0). A star has no loops (β₁ = 0). A ring has one. A full mesh has many. Count the loops, and you can call the diversity ordering before you run anything.

That's the established part. A single integer you can read off your architecture diagram predicts how diverse the system's behavior will be, and the topology dwarfs every other knob. (I'll add the honest caveat the research demands: β₁ is necessary but not *sufficient* — there are same-loop-count graphs that order differently, which is why the full story needs a richer invariant, the first sheaf cohomology H¹. That's a rabbit hole for another piece. For today, β₁ and its spectral cousin λ₂ — the algebraic connectivity, which measures how fast information mixes — are enough.)

## The bet: those same numbers bound cost

Here's the move, and here's where I switch registers from *established* to *conjectured*. I'll flag it clearly because the difference matters.

We know β₁ and λ₂ predict diversity and reliability. We know, from raw graph theory, that the channel count and the mixing rounds — i.e., the orchestration token volume — are also functions of how the graph is wired. The full mesh that maximizes β₁ is also the one with the quadratic channel count. The star that zeroes out β₁ is also the cheapest to route.

So the conjecture is this: **the orchestration cost of a multi-agent system is bounded by the same structural invariants — β₁, λ₂ — that bound its diversity and reliability.** Not a separate cost model bolted on. The same geometry, read for a third quantity. If true, it means you don't get to optimize cost, reliability, and diversity independently — they're three readings of one shape, and you're navigating a single trade-off surface, not three dials.

I want to be honest about the status of that claim. The *direction* is on firm ground: more loops and more channels cost more, and the cheap topologies are the low-β₁ ones — that's just counting. What is **not yet proven** is the precise inequality — the exact f such that cost ≤ f(β₁, λ₂) — and whether it's tight, and whether it survives the move from constant-coefficient graphs to the general sheaf-valued case (where, candidly, β₁ and λ₂ stop being in clean correspondence). I'd put my confidence in "a clean cost bound in these invariants exists for the constant-coefficient case" at maybe 65–70%, and the fully general version is open. Treat the inequality as a research target I'm betting on, not a theorem I'm quoting.

What I'm *not* hedging on: the qualitative claim that cost tracks topology rather than headcount. That one's just arithmetic, and the billing split makes it a dollar fact.

## Why this is suddenly urgent and not just elegant

There's a reason I'm writing this in June and not whenever the inequality gets proven. Until June 15, sloppy composition cost you in ways that were easy to ignore — a little latency, some wasted context, an occasional incoherent answer you could blame on the model. The orchestration overhead was hidden inside a blended bill.

Now that bundled agent-credit gets its own pool — and a badly-wired topology burns through it faster, pushing you into overflow at full API rates. The mesh you drew because it felt "thorough" — every agent sees everything, just in case — is the most expensive graph you could have picked, on the meter that's now itemized, and (per the diversity result) it's not even buying you proportionally more capability. You're paying quadratic channel costs for a system whose useful structure a much cheaper graph might have captured. Every redundant loop in your topology is credit you exhaust sooner, and every token past the monthly allocation is billed at full API list price.

The practitioner takeaway is almost annoyingly concrete:

1. **Count your loops before you count your agents.** β₁ — independent cycles in your comms graph — is the first thing to look at. High β₁ buys diversity but pays in channels and tokens. If you don't *need* the diversity, the loops are pure cost.
2. **Prefer a hub to a mesh unless you can name what the mesh buys.** A star routes coordination in linear channel count and zeroes β₁. You lose some emergent diversity; you save the quadratic. Make that trade on purpose, not by default.
3. **Treat orchestration tokens as a first-class budget.** They now have their own pool. Measure them. The graph that minimizes them is knowable in advance from its structure — you don't have to discover it on the invoice.

The deep version of this — that reliability, diversity, and cost are all shadows of one cohomological object, and that you can in principle compute the trade-off rather than feel it out — is the research I'm chasing, and I've been careful above about which parts are done and which aren't. But you don't need the cohomology to act on the cheap version today.

Your bill was never really about how many agents you ran. It was about the shape you connected them in. Until last week you could pretend otherwise. Starting June 15, the shape is on the invoice — so you might as well measure it.

---

## NOTES FOR LYRA (verify before publishing — do not publish this block)

- **Billing-split specifics. VERIFIED against Anthropic Help Center: support.claude.com/en/articles/15036540, effective June 15, 2026.** Mechanism: Agent SDK usage (programmatic calls via `claude -p`, GitHub Actions, third-party Agent SDK integrations) draws from a SEPARATE MONTHLY CREDIT POOL that is BUNDLED INTO the subscription — NOT billed extra up front. Credit allocations: Pro $20/mo, Max 5x $100/mo, Max 20x $200/mo. Consumed at standard API list rates (e.g. Sonnet 4.6 at $3/$15 per million input/output tokens). Interactive terminal Claude Code and Claude.ai chat are excluded from this pool. Once the credit is exhausted, usage either stops or flows to pay-as-you-go at full API rates — only if overflow billing is separately enabled. The $200 (Max 20x) is the monthly credit ALLOCATION, not a hard cap. The draft has been corrected to reflect these facts. The earlier "couldn't ground the $200" caveat is resolved.
- **23.9× variance ratio** — used as instructed (NOT 28.7×). Phrased as "on the order of 24×" in prose with the exact 23.9× in parentheses. Also used p_domain ≈ 0.95 (SUMMARY: p_domain=0.945). Both grounded in SUMMARY.
- **Kendall's W = 1.0** across six domains for β₁ diversity ordering — grounded in SUMMARY Core Finding.
- **λ₂ = algebraic connectivity / mixing rate** and **β₁ = independent loops** — standard framing, consistent with SUMMARY.
- **Channel counts** (star n−1, ring n, full mesh n(n−1)/2; 4→6, 8→28, 16→120) — pure graph arithmetic, not from SUMMARY. Safe but self-check the worked numbers.
- **Conjecture status / confidence 65–70%** — I assigned this to match the C301 constant-coefficient-vs-general framing and the cost-bound being a "forward target" (SUMMARY two-silos pitch, C302 Prism bound at 65%). Adjust to your own number if you prefer.
- **Did NOT cite** Wang & Buehler "Kan obstruction ≅ H¹" (fabricated per SUMMARY). No external paper citations used at all in the body — kept it methodology-first per pipeline notes. You may want to add 1–2 real citations (e.g., the GECCO camera-ready for the 23.9× figure) before publishing.
- **H¹ / sheaf cohomology** mentioned only as a forward pointer, not leaned on — keeps the established/conjectural line clean per honesty constraints.
- Word count of the article body (excluding STATUS line and this NOTES block): ~1,470 words.
