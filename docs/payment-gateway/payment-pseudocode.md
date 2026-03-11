# Payment System Pseudocode

This document describes the logical flow of the payment system.

---

## Initiate Payment

function initiatePayment(orderId):

    order = getOrder(orderId)

    if order == null
        return error "Order not found"

    if order.status != "Pending"
        return error "Order already processed"

    payment = createPayment(
        orderId,
        order.amount,
        status = "Pending"
    )

    paymobToken = requestPaymobAuthToken()

    paymentUrl = createPaymobPayment(
        token = paymobToken,
        amount = order.amount,
        orderId = order.id
    )

    return paymentUrl

---

## Webhook Handler

function handlePaymentWebhook(payload):

    if verifySignature(payload) == false
        reject request

    payment = findPayment(payload.order_id)

    if payment.status == "Paid"
        return

    if payload.amount != payment.amount
        reject request

    if payload.status == "success"
        payment.status = "Paid"
        order.status = "Confirmed"

    else
        payment.status = "Failed"

    saveChanges()
