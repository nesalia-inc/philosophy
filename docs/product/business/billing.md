# Billing

Invoicing, tax calculation, and payment processing.

---

## Features

```
CAPABILITIES:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

* Invoice generation
* Tax calculation (VAT, GST, sales tax)
* Multi-currency support
* Payment method management
* Automatic invoices
* Invoice templates
* Payment reminders
* Credit notes
* Tax reporting
```

---

## Implementation

```typescript
import { Billing } from '@nesalia/business';

const billing = new Billing({
  currency: 'USD',
  taxProvider: 'stripe', // or 'taxjar', 'avatax'
  invoicePrefix: 'INV-2024',
});

// Create invoice
const invoice = await billing.createInvoice({
  customerId: 'cust_123',
  items: [
    { description: 'Pro Plan - Monthly', quantity: 1, unitPrice: 29 },
    { description: 'Additional Users', quantity: 5, unitPrice: 10 },
  ],
  taxBehavior: 'exclusive', // or 'inclusive'
});

console.log({
  id: invoice.id,
  number: invoice.number, // INV-2024-0001
  status: invoice.status, // draft, open, paid, void
  subtotal: invoice.subtotal,
  tax: invoice.tax,
  total: invoice.total,
  dueDate: invoice.dueDate,
});
```

---

## Tax Calculation

```typescript
// Calculate tax for order
const tax = await billing.calculateTax({
  customerId: 'cust_123',
  lineItems: [
    { amount: 2900, taxCode: 'srvc_subscription' }, // $29.00
  ],
  address: {
    country: 'US',
    state: 'CA',
    postalCode: '94102',
  },
});

console.log({
  taxableAmount: tax.taxableAmount,
  taxAmount: tax.taxAmount,
  taxRate: tax.taxRate,
  breakdown: tax.breakdown,
  // { state: 'CA', county: 0, city: 0, special: 0 }
});

// Tax calculation with VAT
const vat = await billing.calculateVAT({
  customerId: 'cust_eu',
  amount: 100,
  country: 'FR',
  vatNumber: 'FR12345678901', // Validate EU VAT number
});

console.log({
  baseAmount: vat.baseAmount,
  vatRate: vat.vatRate, // 20% for France
  vatAmount: vat.vatAmount,
  total: vat.total,
  validVatNumber: vat.validVatNumber,
});
```

---

## Invoice Management

```typescript
// Send invoice
await billing.sendInvoice({
  invoiceId: invoice.id,
  sendTo: 'customer@example.com',
  sendBcc: 'billing@company.com',
});

// Send reminder
await billing.sendReminder({
  invoiceId: invoice.id,
  type: 'payment_due', // or 'payment_overdue'
});

// Cancel void invoice
await billing.voidInvoice({
  invoiceId: invoice.id,
  reason: 'Duplicate invoice',
});

// Create credit note
const creditNote = await billing.createCreditNote({
  invoiceId: invoice.id,
  amount: 50,
  reason: 'Partial refund',
});
```

---

## Subscriptions & Recurring

```typescript
// Generate invoice from subscription
const subInvoice = await billing.generateFromSubscription({
  subscriptionId: 'sub_123',
  billingPeriod: {
    start: '2024-01-01',
    end: '2024-01-31',
  },
});

// Prorated invoice for upgrade
const prorated = await billing.calculateProration({
  subscriptionId: 'sub_123',
  newPlanId: 'plan_enterprise',
  prorationDate: '2024-01-15',
});

console.log({
  credit: prorated.credit,
  charge: prorated.charge,
  netAmount: prorated.netAmount,
});
```

---

## Payment Processing

```typescript
// Collect payment
const payment = await billing.collectPayment({
  invoiceId: invoice.id,
  paymentMethod: {
    type: 'card',
    cardId: 'pm_123',
  },
});

// Handle failed payment
await billing.handleFailedPayment({
  invoiceId: invoice.id,
  retry: true,
  retryCount: 1,
});

// Partial payment
await billing.applyPayment({
  invoiceId: invoice.id,
  amount: 50,
  method: 'bank_transfer',
  reference: 'BT-12345',
});
```

---

## Tax Reporting

``````typescript
// Generate tax report
const taxReport = await billing.generateTaxReport({
  from: '2024-01-01',
  to: '2024-03-31',
  jurisdiction: 'all', // or specific country/state
});

console.log({
  totalCollected: taxReport.totalCollected,
  totalRemitted: taxReport.totalRemitted,

  byJurisdiction: taxReport.byJurisdiction,
  // [
  //   { jurisdiction: 'US-CA', rate: 8.5, collected: 5000, remitted: 5000 },
  //   { jurisdiction: 'US-NY', rate: 8.0, collected: 3000, remitted: 3000 },
  // ]

  transactions: taxReport.transactions,
});

// Export for accountant
const export = await billing.exportTaxReport({
  format: 'xlsx',
  from: '2024-01-01',
  to: '2024-03-31',
});
```

---

## Webhooks

```typescript
// Handle billing events
await billing.handleWebhook({
  event: 'invoice.paid',
  data: { invoiceId: 'inv_123' },
});

await billing.handleWebhook({
  event: 'invoice.payment_failed',
  data: { invoiceId: 'inv_123', error: 'card_declined' },
});

await billing.handleWebhook({
  event: 'customer.tax_id_invalid',
  data: { customerId: 'cust_123', taxId: 'EU123...' },
});
```

---

*Document version: 1.0*
*Last updated: 2026-03-05*
