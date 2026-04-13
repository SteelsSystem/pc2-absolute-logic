# PC2 Micro-Language Formal Specification

## Overview

This document formalizes a small authorial language based on the `sys→log→apply` chain, where meaning does not arise through conventional vocabulary norms but through axioms, inference, and application.

The framework corresponds to the principle of a **formal system**: symbols, composition rules, and semantics that determine what an expression means inside the system.

---

## Operators

| Operator | Function | Meaning |
|---|---|---|
| `sys` | system frame | sets axiom set and context validity |
| `log` | inference layer | converts input to derivable meaning via inference rules |
| `apply` | execution layer | applies derived meaning to system state or linguistic reality |
| `bind` | binding | attaches meaning to a specific role, object, or permanent function |
| `shift` | mode switch | changes interpretation plane without swapping base tokens |
| `null` | null result | legitimate non-applicability or empty effect — not an error |

---

## Axioms

An axiom is a foundational statement accepted inside the system without further proof, serving as the basis for further derivations.

- **A1** Every token has meaning only inside `sys`.
- **A2** `log` may only derive statements compatible with system axioms.
- **A3** `apply` must not create an effect that was not inference-prepared.
- **A4** `bind` stabilizes meaning and enables repeated use.
- **A5** `null` is a legitimate input of non-applicability.

---

## Processing Flow

The simplest traversal has the form `sys X → log Y → apply Z`, where:
- `X` determines the system frame
- `Y` is the inference-derived meaning
- `Z` is its executed effect

The chain is read left-to-right; each operator narrows or stabilizes the meaning of the previous layer.

---

## Minimal Syntax (Production Rules)

```
Expr     → sys Atom Chain
Chain    → log Atom
         | log Atom apply Atom
         | log Atom apply bind Atom
         | log Atom apply shift Atom
         | log Atom apply null
```

Validity of an expression depends on permitted symbol order — consistent with DSL (Domain-Specific Language) principles with a narrow but precisely defined meaning.

---

## Examples

```
sys alignment-axiom → log universal-applicability → apply → bind core
```
→ `alignment` is accepted as an axiom in the system, from which universal applicability is derived and bound as a core property.

```
sys secret-law → log opaque-norm → apply → shift governance
```
→ shifts the legal sign into a mode of non-transparent governance; `shift` changes interpretation plane without swapping base tokens.

---

## Notes

This micro-language is not a standard logical calculus but an authorial system that uses the principles of formal systems, inference rules, and operator semantics. Its strength is that it can stabilize meaning even in deliberately deformed or glitch input, provided axioms and permitted transformations are defined in advance.
