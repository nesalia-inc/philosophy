# Stripe Examples

Copy-paste ready code examples.

---

## Initialize Stripe

```typescript
import Stripe from 'stripe'

export const stripe = new Stripe(process.env.STRIPE_SECRET_KEY!, {
  apiVersion: '2023-10-16',
})
```

---

## Create Payment Intent

```typescript
import { stripe } from './stripe'

export const createPaymentIntent = async (
  amount: number,
  currency: string = 'usd'
) => {
  return await stripe.paymentIntents.create({
    amount, // Amount in cents
    currency,
    automatic_payment_methods: {
      enabled: true,
    },
  })
}
```

---

## Handle Webhook

```typescript
import { stripe } from './stripe'
import { handlePaymentSucceeded } from './orders'

export const handleWebhook = async (
  payload: string,
  signature: string
) => {
  const webhookSecret = process.env.STRIPE_WEBHOOK_SECRET!

  const event = stripe.webhooks.constructEvent(
    payload,
    signature,
    webhookSecret
  )

  switch (event.type) {
    case 'payment_intent.succeeded':
      await handlePaymentSucceeded(event.data.object)
      break

    case 'payment_intent.payment_failed':
      await handlePaymentFailed(event.data.object)
      break
  }

  return { received: true }
}
```

---

## Create Customer

```typescript
import { stripe } from './stripe'

export const createCustomer = async (
  email: string,
  name?: string
) => {
  return await stripe.customers.create({
    email,
    name,
  })
}
```

---

## Get or Create Customer

```typescript
import { stripe } from './stripe'

export const getOrCreateCustomer = async (
  userId: string,
  email: string
) => {
  // Check if customer exists in your database
  const existingCustomerId = await db.users.getCustomerId(userId)

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
  await db.users.setCustomerId(userId, customer.id)

  return customer
}
```

---

## List Customer Payments

```typescript
import { stripe } from './stripe'

export const getCustomerPayments = async (
  customerId: string,
  limit: number = 10
) => {
  return await stripe.paymentIntents.list({
    customer: customerId,
    limit,
  })
}
```

---

## Refund Payment

```typescript
import { stripe } from './stripe'

export const refundPayment = async (
  paymentIntentId: string,
  amount?: number // Optional: partial refund
) => {
  return await stripe.refunds.create({
    payment_intent: paymentIntentId,
    ...(amount && { amount }), // Amount in cents
  })
}
```

---

*Last updated: 2026-03-06*
