# Stripe Fundamentals

Core concepts you need to understand.

---

## Payment Intent

A PaymentIntent represents a single attempt to move money.

```typescript
const paymentIntent = await stripe.paymentIntents.create({
  amount: 2000,        // Amount in cents
  currency: 'usd',      // Currency code
  automatic_payment_methods: {
    enabled: true,     // Enable automatic payment methods
  },
})
```

### Key Properties

| Property | Description |
|----------|-------------|
| `id` | Unique identifier (pi_xxx) |
| `client_secret` | Used by frontend to complete payment |
| `status` | Payment state (requires_payment_method, succeeded, etc.) |
| `amount` | Amount in smallest currency unit |

---

## Webhooks

Webhooks notify your server about async events.

### Events

- `payment_intent.succeeded` - Payment completed
- `payment_intent.payment_failed` - Payment failed
- `customer.created` - New customer
- `invoice.paid` - Invoice paid

### Webhook Payload

```typescript
interface StripeEvent {
  id: string
  type: string
  data: {
    object: any
  }
}
```

---

## Customers

Customers represent users in Stripe.

```typescript
const customer = await stripe.customers.create({
  email: 'user@example.com',
  name: 'John Doe',
})
```

---

## API Keys

| Key Type | Use | Prefix |
|----------|-----|--------|
| Secret Key | Server-side | `sk_live_xxx` / `sk_test_xxx` |
| Publishable Key | Client-side | `pk_live_xxx` / `pk_test_xxx` |
| Webhook Secret | Verify webhooks | `whsec_xxx` |

**NEVER expose secret keys in frontend code.**

---

## Idempotency

Idempotency prevents duplicate operations.

```typescript
const paymentIntent = await stripe.paymentIntents.create(
  {
    amount: 2000,
    currency: 'usd',
  },
  {
    idempotencyKey: 'unique-key-123',
  }
)
```

---

*Last updated: 2026-03-06*
