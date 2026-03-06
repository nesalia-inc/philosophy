# Stripe

Complete payment processing course.

---

## Overview

Stripe is a payment processing platform that handles:
- Credit card payments
- Webhooks for async events
- Subscriptions
- Invoices

## Quick Start

```bash
npm install stripe
```

```typescript
import Stripe from 'stripe'

const stripe = new Stripe(process.env.STRIPE_SECRET_KEY!)

// Create payment intent
const paymentIntent = await stripe.paymentIntents.create({
  amount: 2000,
  currency: 'usd',
})

// Return client secret to frontend
return { clientSecret: paymentIntent.client_secret }
```

## Version

Current: `stripe@14.x`

## Official Documentation

- Docs: https://stripe.com/docs
- API Reference: https://stripe.com/docs/api
- Best Practices: https://stripe.com/docs/best-practices

## What's Inside

- [Fundamentals](./fundamentals/)
- [Patterns](./patterns/)
- [Pitfalls](./pitfalls/)
- [Examples](./examples/)

---

*Last updated: 2026-03-06*
