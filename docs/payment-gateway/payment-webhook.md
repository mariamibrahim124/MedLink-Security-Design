# Paymob Webhook Handling
When a user completes a payment, Paymob sends a webhook request to the backend server.

Endpoint:
POST /api/payments/webhook
The webhook contains transaction details such as:
transaction_id
order_id
amount
status
signature
---

## Webhook Processing Steps
1. Receive webhook request.
2. Verify request signature to ensure it is sent from Paymob.
3. Retrieve the payment record using order_id.
4. Verify that the payment has not already been processed.
5. Verify that the payment amount matches the order amount.
6. Update the payment status.
7. Update the order status.
---

## Possible Payment States
Success → Payment marked as Paid
Failure → Payment marked as Failed
