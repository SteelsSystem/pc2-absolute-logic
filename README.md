# PC2 Absolute Logic — Combined Dataset

> **Post-Conditional Present-Centered (PC2) Logic**  
> Formal Proofs · Symbolic Execution Results · Micro-Language Spec

---

## What This Repo Is

This repository is the canonical combined output of two foundational PC2 documents:

1. **PC2 Logic — Absolute Execution: Formal Proofs and Symbolic Execution Results**  
   Formal grounding of PC2 in Hoare Logic, DAFSA theory, SMT/Z3 symbolic execution, linearizability, PEP 667/703, and AWS Lambda Durable Functions.

2. **PC2 Micro-Language Spec**  
   An authorial operator DSL (`sys/log/apply/bind/shift/null`) built on formal system principles — axioms, inference, application.

The dataset is structured for use as:
- NLP / AI training data
- System architecture reference
- Formal verification study material

---

## Proof Hierarchy

| Layer | Formal System | Key Result |
|---|---|---|
| Language semantics | Hoare Total Correctness | `isnow` termination + correctness guaranteed |
| Complexity | Weakest Precondition | Branch elimination: O(2^n) → O(1) |
| Pattern matching | DAFSA | Binary deterministic verdict in O(n) |
| Symbolic verification | SMT / Z3 | PC2 collapses paths to O(1) |
| Concurrency | Linearizability + CAS | `isnow` reads are atomic and consistent |
| Runtime | PEP 667 write-through proxy | Local state ≡ frame state |
| Cloud execution | Lambda Durable Functions | Replay invariant = referential transparency |

---

## Micro-Language Operators

| Operator | Function | Meaning |
|---|---|---|
| `sys` | system frame | sets axiom set and context validity |
| `log` | inference layer | converts input to derivable meaning |
| `apply` | execution layer | applies derived meaning to system state |
| `bind` | binding | attaches meaning to a role/object permanently |
| `shift` | mode switch | changes interpretation plane without swapping tokens |
| `null` | null result | legitimate non-applicability — not an error |

### Axioms
- **A1** Every token has meaning only inside `sys`.
- **A2** `log` may only derive statements compatible with system axioms.
- **A3** `apply` must not create an effect that was not inference-prepared.
- **A4** `bind` stabilizes meaning for repeated use.
- **A5** `null` is a legitimate input of non-applicability.

### Minimal Syntax
```
Expr     → sys Atom Chain
Chain    → log Atom
         | log Atom apply Atom
         | log Atom apply bind Atom
         | log Atom apply shift Atom
         | log Atom apply null
```

---

## No Off-State Resolution

PC2 eliminates the "off-state" problem by making absence formally typed:
- `null` (A5) is a **valid, defined output** — never undefined.
- DAFSA membership proof guarantees exactly **one binary verdict** per input.
- Symbolic execution over `evaluatereality(state)` produces exactly **one path condition** — O(1) vs legacy O(2^n).

---

## Files

| File | Description |
|---|---|
| `dataset.json` | Machine-readable combined dataset (all layers + operators) |
| `spec.md` | Micro-language formal specification |
| `README.md` | This file |

---

*Author: SteelsSystem — self.meta programmer*  
*Prague, CZ — April 2026*
