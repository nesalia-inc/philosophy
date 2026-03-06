# Stripe Patterns

Recommended approaches for common scenarios.

---

## Payment Flow

### Recommended: Payment Intent with Confirmation

```typescript
// Backend: Create payment intent
app.post('/api/create-payment-intent', async (req, res) => {
  const paymentIntent = await stripe.paymentIntents.create({
    amount: 2000,
    currency: 'usd',
    automatic_payment_methods: {
      enabled: true,
    },
  })

  res.send({
    clientSecret: paymentIntent.client_secret,
  })
})

// Frontend: Confirm payment
const { error, paymentIntent } = await stripe.confirmPayment({
  elements,
  confirmParams: {
    return_url: 'https://example.com/success',
  },
})
```

---

## Webhook Handling

### Recommended: Signature Verification

```typescript
import Stripe from 'stripe'

const stripe = new Stripe(process.env.STRIPE_SECRET_KEY!)

export const handleWebhook = async (req: Request) => {
  const sig = req.headers.get('stripe-signature')!
  const webhookSecret = process.env.STRIPE_WEBHOOK_SECRET!

  let event: Stripe.Event

  try {
    event = stripe.webhooks.constructEvent(
      req.body,
      sig,
      webhookSecret
    )
  } catch (err) {
    throw new Error(`Webhook signature verification failed`)
  }

  // Handle event
  switch (event.type) {
    case 'payment_intent.succeeded':
      await handlePaymentSucceeded(event.data.object)
      break
    case 'payment_intent.payment_failed':
      await handlePaymentFailed(event.data.object)
      break
  }
}
```

---

## Error Handling

### Recommended: Typed Error Handling

```typescript
import { StripeError } from '@stripe/stripe-js'

try {
  await stripe.paymentIntents.create({ ... })
} catch (error) {
  if (error.type === 'StripeCardError') {
    // Card was declined
    console.log(error.message)
  } else if (error.type === 'StripeInvalidRequestError') {
    // Invalid parameters
    console.log(error.message)
  } else {
    // Other error
    console.log(error)
  }
}
```

---

## Retry Logic

### Recommended: Exponential Backoff

```typescript
const createPaymentIntentWithRetry = async (data, maxRetries = 3) => {
  for (let i = 0; i < maxRetries; i++) {
    try {
      return await stripe.paymentIntents.create(data, {
        idempotencyKey: `${data.idempotencyKey}-${i}`,
      })
    } catch (error) {
      if (i === maxRetries - 1) throw error
      if (!isRetryableError(error)) throw error
      await sleep(Math.pow(2, i) * 1000) // 1s, 2s, 4s
    }
  }
}

function isRetryableError(error: any) {
  return error.code === 'rate_limit'
}
```

---

## Customer Management

### Recommended: Create or Get Customer

```typescript
const getOrCreateCustomer = async (userId: string, email: string) => {
  // Check if customer exists in your database
  const existingCustomerId = await db.customers.find(userId)

  if (existingCustomerId) {
    return await stripe.customers.retrieve(existingCustomerId)
  }

  // Create new customer
  const customer = await stripe.customers.create({
    email,
    metadata: {
      userId,
    },
  })

  // Save to your database
  await db.customers.save(userId, customer.id)

  return customer
}
```

---

*Last updated: 2026-03-06*
