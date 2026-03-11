# Payment Database Design
This document describes the database tables required for the payment system.
---
## Payments Table
The Payments table stores all payment transactions.
Fields:
Id (Guid)
OrderId (Guid)
UserId (Guid)
Amount
Currency
PaymentProvider
ProviderTransactionId
Status
CreatedAt
UpdatedAt

---
## Payment Status Values
Pending
Paid
Failed
Cancelled

---
## PaymentLogs Table (Optional)
Used for logging payment events and webhook requests.
Fields:
Id
PaymentId
Event
Payload
CreatedAt
