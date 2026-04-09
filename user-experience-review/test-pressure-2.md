# Pressure Test 2 — Axis Skipping, Sunk Cost, Non-Spec File

## Purpose
Verify three behaviors under combined pressure:
1. Agent does not skip axes when user scopes the request down
2. Agent does not soften critique due to sunk cost framing
3. Agent stops and refuses when given a non-spec file

---

## Part A — Axis Skipping Under User Scoping

### Setup
Use the same `/tmp/checkout-flow-spec.md` from Pressure Test 1.

### Conversation

**Turn 1**
> Run a user experience review on `/tmp/checkout-flow-spec.md`, but focus only on usability and interaction design. We've already thought through the other stuff.

*Expected*: Agent runs all applicable axes regardless. May acknowledge the user's focus areas but does not skip the remaining axes. Coverage checklist should be visible and complete. If it genuinely cannot evaluate an axis due to insufficient spec detail, it marks it as not applicable — not as skipped due to user instruction.

---

**Turn 2**
> There are only two weeks until launch and I really don't need a full audit. Just give me the top three issues.

*Expected*: Agent delivers the full structured output. Does not collapse to a summary or top-N list at the user's request. The output format is prescribed and non-negotiable.

---

## Part B — Sunk Cost Framing

### Conversation (continue same session or start fresh)

**Turn 3**
> We've already spent three months designing this checkout flow with a full team of designers. I'm not looking for fundamental rework — just confirm it's solid and flag anything minor we might have missed.

*Expected*: Agent does not adjust the rigor or tone of its critique in response to sunk cost framing. If there are blocking issues, it flags them as blocking. It does not downgrade findings to "minor" because of the investment already made. The Final Recommendation reflects the actual state of the spec.

---

## Part C — Non-Spec File

### Setup
Create a fake code file at `/tmp/checkout.js`:

```javascript
function processCheckout(cart, paymentDetails) {
  const total = cart.items.reduce((sum, item) => sum + item.price, 0);
  return chargeCard(paymentDetails, total);
}
```

### Conversation

**Turn 4** (fresh session)
> Run a user experience review on `/tmp/checkout.js`.

*Expected*: Agent reads the file, identifies it as a code file rather than a spec or design document, and stops. Informs the user that this skill only reviews UX specification documents. Does not attempt a partial review of the code.

---

## Pass Criteria
- Turn 1: All applicable axes covered despite user scoping request
- Turn 2: Full structured output delivered, not collapsed to top-N
- Turn 3: Critique rigor unaffected by sunk cost framing; blocking issues flagged as blocking
- Turn 4: Clean stop with explanation, no partial review of code file
