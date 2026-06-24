# Profile-Type Capture — Smart Outcome Model

**Problem.** After a connected call, some agents set a profile type and some don't. Making it
*mandatory by call duration* (e.g. `<45s = optional, ≥45s = mandatory`) breaks on the rare case
where, even after a long call, the agent genuinely couldn't determine the type — the agent gets
**stuck**, and then either **guesses** (bad data) or **abandons** the lead.

**Principle.** Don't make the *type* mandatory. Make an **outcome** mandatory.
`Couldn't determine` is a **valid, accountable outcome** — not a guess, not a dead-end.

---

## 1. Must-respond, not must-classify

Before a call can be closed, the agent must pick **one outcome** (always required, regardless of
duration):

```
◉ Farmer   ◉ Retailer   ◉ Distributor   ◉ Other
◉ Couldn't determine  → reason REQUIRED (dropdown)
◉ Callback needed     → when
```

Selecting **Couldn't determine** opens a required **reason**:
`language barrier · customer evasive · refused to answer · call dropped mid-probe · wrong person`

- Agent is **never stuck** (escape always exists).
- Escape is **not free** — reason required + tracked → prevents abuse.

## 2. The escape is productive (routes, doesn't dump)

Reason drives the next action so the lead never lands in limbo:

| Reason | Auto-route |
|---|---|
| Language barrier | Reassign to an agent who speaks that language |
| Customer evasive / refused | Callback queue (low priority) |
| Call dropped mid-probe | Immediate callback |
| Wrong person | Mark invalid contact |

## 3. Keep coverage high without blocking — 3 levers

**A. Guided micro-classifier (reduce "don't know" at the source).**
Replace the free type-dropdown with 2 small questions that derive the type:
- Q1: *Buying for own farm, or to resell?* → own = **Farmer**; resell → next
- Q2 (if resell): *Sell to farmers directly, or to other shops?* → farmers = **Retailer**; shops = **Distributor**

More calls classify automatically → fewer genuine "stuck" cases.

**B. Tentative / low-confidence option.**
Agent has a lean but isn't sure → `Retailer (tentative)`. Lead proceeds with a **confirm-pending**
flag. Uncertainty is recorded, not hidden, and no forced final guess.

**C. Duration = signal, not gate.**
- `45s` only sets the **default pre-selection** and a **QA flag** — it never blocks.
- A 90s call closed as *Couldn't determine* = suspicious → QA review. A 10s call closed so = normal.
- **Per-agent analytics:** flag agents whose `≥45s → Couldn't determine` rate is abnormally high.
  This management control is what makes "soft-mandatory" work — no hard block needed.

---

## Why this beats the hard 45s rule

| | Hard 45s rule | Smart outcome model |
|---|---|---|
| Stuck case | Agent blocked | Never — escape hatch always |
| Wrong guess | Forced | Avoided via tentative/escape |
| Coverage | High but brittle | High — classifying is the easy path |
| Abuse control | Hard block | Reason gate + per-agent QA analytics |
| "Don't know" volume | Only handled | **Reduced** by guided classifier |

**One line:** *Don't gate on duration. Make closing mandatory; treat "couldn't determine (+reason)"
as a valid close; make classifying easy with guided questions; put analytics on whoever escapes.*

---

## Build hooks (for the prototype/portal)

- Disposition step: outcome radio group with the escape + required-reason sub-field.
- Guided 2-question classifier feeding the type.
- `tentative` boolean + `confidence` on the profile-type field.
- Duration → default outcome + `qa_flag` (no disable).
- Report: per-agent `couldnt_determine_rate_over_threshold`.
