# Stripe Pitfalls

Common mistakes to avoid.

---

## Security Mistakes

### Mistake: Exposing Secret Key

```typescript
// ❌ BAD: Exposing secret key
const stripe = new Stripe('sk_live_xxx')

// ✅ GOOD: Use environment variable
const stripe = new Stripe(process.env.STRIPE_SECRET_KEY!)
```

### Mistake: Not Verifying Webhook Signatures

```typescript
// ❌ BAD: No signature verification
app.post('/webhook', async (req, res) => {
  const event = req.body // Never do this!
})

// ✅ GOOD: Always verify
const event = stripe.webhooks.constructEvent(
  req.body,
  sig,
  webhookSecret
)
```

---

## Implementation Mistakes

### Mistake: Using Amount in Dollars

```typescript
// ❌ BAD: Amount in dollars
await stripe.paymentIntents.create({
  amount: 100, // This is $100, not $0.01!
})

// ✅ GOOD: Amount in cents
await stripe.paymentIntents.create({
  amount: 10000, // $100.00
})
```

### Mistake: Not Handling Duplicate Webhooks

```typescript
// ❌ BAD: Process event every time
app.post('/webhook', async (req, res) => {
  const event = req.body
  await fulfillOrder(event.data.object.id) // Could run twice!
})

// ✅ GOOD: Check for duplicates
app.post('/webhook', async (req, res) => {
  const event = req.body
  const idempotencyKey = `order-${event.data.object.id}`

  // Check if already processed
  const exists = await db.processedEvents.find(idempotencyKey)
  if (exists) return res.send({ received: true })

  await fulfillOrder(event.data.object.id)
  await db.processedEvents.save(idempotencyKey)
})
```

---

## Error Handling Mistakes

### Mistake: Not Handling Specific Errors

```typescript
// ❌ BAD: Generic error handling
try {
  await stripe.paymentIntents.create({ ... })
} catch (e) {
  console.log('Payment failed') // Too vague!
}

// ✅ GOOD: Specific error handling
try {
  await stripe.paymentIntents.create({ ... })
} catch (error) {
  if (error.type === 'StripeCardError') {
    // Card declined - show specific message
    switch (error.code) {
      case 'card_declined':
        return 'Your card was declined'
      case 'expired_card':
        return 'Your card has expired'
      case 'insufficient_funds':
        return 'Insufficient funds'
    }
  }
}
```

---

## Testing Mistakes

### Mistake: Using Live Keys in Tests

```typescript
// ❌ BAD: Using live key
const stripe = new Stripe('sk_live_xxx')

// ✅ GOOD: Use test keys
const stripe = new Stripe(process.env.STRIPE_TEST_KEY!)
```

---

## Integration Mistakes

### Mistake: Not Using Idempotency

```typescript
// ❌ BAD: No idempotency
const paymentIntent = await stripe.paymentIntents.create({
  amount: 2000,
})

// ✅ GOOD: Add idempotency key
const paymentIntent = await stripe.paymentIntents.create(
  {
    amount: 2000,
  },
  {
    idempotencyKey: `payment-${orderId}-${Date.now()}`,
  }
)
```

---

## Summary

| Pitfall | Solution |
|---------|----------|
| Exposed secret key | Use env variables |
| No webhook verification | Always verify signatures |
| Amount in wrong unit | Use cents |
| Duplicate webhooks | Check for duplicates |
| Generic error handling | Handle specific errors |
| No idempotency | Add idempotency keys |
| Test keys in production | Use correct keys per env |

---

*Last updated: 2026-03-06*
