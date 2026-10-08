# Temporary — task type ideas

Scratch space. Not part of the skill. Delete once the types are settled.

For each type worth a file in `references/`, it helps to have:

- **Name** — what the type is called in the routing table.
- **Trigger** — how the agent recognizes it from the conversation, without opening the file.
- **What differs** — the structure or principles that are not shared with other types. If nothing differs, it's another example inside an existing type, not a new one.
- **Example** — a real task of this kind, even a rough one.

---

## Ideas

### 1. Contract bug

Something we publish does not behave the way its API says it does. A UI component, an abstraction, an interface, a package in a monorepo — anything with **consumers**. The consumer wrote the usage correctly and still got the wrong result.

- **Name** — "Contract bug"? The abstraction promises X, delivers Y. Working title.
- **Trigger** — a shared abstraction misbehaves for someone consuming it; the fix lands in the abstraction, not the consumer. Design system, component library, package, public interface.
- **What differs from Impact**
  - Audience is engineers who own the abstraction. Not managers. Jargon is fine.
  - Opens with a **code snippet** of the consumer's usage, not a narrative about someone's day.
  - Actual vs expected is the spine of the description.
  - Likely no "Data supporting this" — one clean repro is the evidence. Frequency does not need proving.
- **Shape, roughly**
  1. The usage — snippet of what the consumer wrote.
  2. What happens — the wrong result.
  3. Why it does not work — the mechanism, if known.
  4. What should happen — the expected result, stated concretely.
- **Example** — component with a prop; consumer sets it, expects X, gets Y. Needs a real one.

**Open:** is "why it does not work" required, or optional when the cause is unknown? A bug report from a consumer often lacks it.

### 2. The shape axis (candidate structure for the router)

Types should divide by **what the reader has to be convinced of**, not by subject matter.

| Reader question | Shape | Status |
|---|---|---|
| Is this worth doing? | narrative + evidence + goal | **Impact** — written |
| What exactly is broken? | usage / actual / expected | **Contract bug** — idea 1 |
| What are we building? | behavior + acceptance | not written — for work already agreed, needs defining |
| What don't we know? | question + the decision it unblocks | not written — spike |

Roughly four, and closed. Test it against the topics a platform team actually ships:

migration/upgrade · adoption/rollout · deprecation/removal · DX/tooling · observability · incident follow-up · performance regression · cost reduction · codemod & lint governance · new abstraction for a consuming team · docs/enablement · spike

Every one lands in a shape above. Cost reduction → Impact. Node upgrade → Impact. Prop returns the wrong value → Contract bug. "Should we adopt X" → Spike. **Routing on topics would produce a dozen near-identical files the agent has to guess between; routing on shape stays small and the branches genuinely differ.**

Evidence the router is worth keeping: Impact demands *plain enough for a non-technical manager* and *every claim traces to a source*. Contract bug wants *jargon is fine, open with a snippet, one repro is the evidence*. Those contradict — a single flat file makes the agent choose between conflicting rules with no signal for which applies.

**Settle it from real tasks, not theory.** Write 8-10 the way they should be written, then count the distinct shapes. All one shape → delete the router. Three or four → the clusters name themselves.

<!-- dump freely below -->
