# Few-Shot Example Bank — Customer Support Extraction

These examples are included in the final prompt to teach the model correct behavior on edge cases that the zero-shot version handled incorrectly. Each example pairs a raw customer message with its **exact** expected JSON output.

> **Note:** These examples were deliberately chosen to cover: a clean happy-path case, a missing-order-ID case, a high-sentiment/angry case, and a multi-issue case where one issue must be selected as dominant.

---

## Example 1 — Happy Path (Refund Request)

**Input:**

```
<customer_message>
Hi, this is Priya Nair. I returned my blender (Order #ORD-88421) two weeks ago and still haven't received my refund of $64.99. Can you please check on this? It's been a while and I'd appreciate an update soon.
</customer_message>
```

**Output:**

```json
{
  "customer_name": "Priya Nair",
  "order_id": "ORD-88421",
  "issue_category": "refund_request",
  "sentiment": "neutral",
  "urgency": "medium",
  "requested_resolution": "Process and confirm the refund of $64.99 for the returned blender.",
  "summary": "Customer is requesting an update on a delayed refund for a returned blender."
}
```

---

## Example 2 — Missing Order ID (Null Handling)

**Input:**

```
<customer_message>
I never got my package and it's been almost three weeks. I don't have my order number on hand right now but I ordered the wireless headphones in the silver color. This is really frustrating, I needed them for a trip.
</customer_message>
```

**Output:**

```json
{
  "customer_name": null,
  "order_id": null,
  "issue_category": "shipping",
  "sentiment": "frustrated",
  "urgency": "high",
  "requested_resolution": "Locate the missing package and provide a delivery update.",
  "summary": "Customer has not received their headphones order after nearly three weeks and is frustrated."
}
```

> **Why this matters:** The zero-shot draft of this prompt invented a plausible-looking order ID here. The few-shot example explicitly teaches the model to return `null` rather than fabricate a value.

---

## Example 3 — High-Intensity Sentiment (Angry)

**Input:**

```
<customer_message>
This is the THIRD time I'm contacting you about this. My account got locked after your last "security update" and I have been unable to access my business dashboard for two days. I am losing money every hour this is down. Fix this immediately or I am cancelling my subscription.
</customer_message>
```

**Output:**

```json
{
  "customer_name": null,
  "order_id": null,
  "issue_category": "account_access",
  "sentiment": "angry",
  "urgency": "critical",
  "requested_resolution": "Restore immediate access to the locked business dashboard account.",
  "summary": "Customer's business account has been locked for two days, causing financial loss, and is demanding immediate resolution."
}
```

> **Why this matters:** Distinguishes `"angry"` from `"frustrated"` — repeated contact, all-caps emphasis, explicit threat of cancellation, and quantified business impact push this to the highest sentiment and urgency tiers.

---

## Example 4 — Multiple Issues, Single Dominant Category

**Input:**

```
<customer_message>
Hello, my name is Marcus Webb. Two things — first, I was charged twice for order #A-55209, and second, the item that did arrive was the wrong size. The double charge is the bigger problem for me right now since it's $89 extra on my card.
</customer_message>
```

**Output:**

```json
{
  "customer_name": "Marcus Webb",
  "order_id": "A-55209",
  "issue_category": "billing",
  "sentiment": "negative",
  "urgency": "high",
  "requested_resolution": "Refund the duplicate charge of $89.",
  "summary": "Customer was double-charged $89 on an order and also received the wrong item size, with the billing error being the priority."
}
```

> **Why this matters:** Teaches the model to select a single dominant `issue_category` (billing, per the customer's own stated priority) even when the message describes two distinct problems, and to focus `requested_resolution` and `summary` on the dominant issue.

---

## Selection Rationale

| Example | Edge Case Taught |
|---|---|
| 1 | Standard clean extraction — establishes baseline format |
| 2 | `null` handling for missing identifiers (prevents hallucination) |
| 3 | Calibrated sentiment/urgency escalation language |
| 4 | Dominant-issue selection when multiple problems are mentioned |
