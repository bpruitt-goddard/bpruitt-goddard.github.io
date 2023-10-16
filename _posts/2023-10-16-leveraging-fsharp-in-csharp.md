---
layout: post
title: Leveraging F# In C#
category: DotNet
tags: [c#, f#]
---

Although C# has made great strides towards being _more_ functional, there are still a few places where the truly functional F# has advantages. To this end, [this simple github project](https://github.com/bpruitt-goddard/payment-fs-cs-comparison) seeks to highlight one such use case and demonstrate the ease of integrating an F# layer into a C# solution.

# Project Summary

The application is a very simple payment processing service that logs payments made by either cash or credit card with basic validation. The service is written in both C# and F# to demonstrate the difference in solutions.

# Interoperability

The interoperability between C# and F# is demonstrated in the C# test project where it calls both services. As long as the F# interface restricts its usage to features that are available in C#, the usage is as seamless as calling a C# project.

However if an unsupported F# feature is used, then it exposes the abstraction on the C# side. For example, given this F# `Discriminated Union`:

```fsharp
type Payment =
    | Cash of decimal
    | CreditCard of (string * decimal) /// (ccNum * amount)
```

To create a new `Cash` object via C#, the code becomes an unintuitive instantiation method that is dynamically created:

```csharp
Payment.NewCash(15m);
```

Compared to the regular object instantiation in F#:

```fsharp
Payment.Cash 15m
```

# Result Type

In our payment processor, there are a few cases where validation can fail:

1. The payment itself is `null`
2. The amount to pay is negative
3. For credit card payments, the credit card number does not match the expected (regex) format

In C#, it can be difficult to track these different options:

1. We can leverage a `null` result, but that can only handle one of these validation issues, or it can handle all of them without a way to distinguish between them
2. We can throw exceptions, but that leads to exception driven code flow, which is difficult to read and places the onus on the caller to understand the inner processing to know what exceptions it needs to handle
3. We can overload the response object (used here), but this can lead to a complex response object

F# allows for a better way: [Results](https://learn.microsoft.com/en-us/dotnet/fsharp/language-reference/results). These provide a simple way to declare the list of possible return types up front, and indicate this to any caller for them to handle. In this simple service, the simplest use case has been used (default `Ok` and `Error`), but the processor can be extended to more explicitly create separate concrete response types. Consider the validation of a cash payment:

```fsharp
let createCashPayment amount =
    if amount >= 0m
    then Ok (Cash amount)
    else Error "Cash must be a positive amount"
```

This clarifies that the response can either return a successful response or an error state with an error message. In traditional C#, this can be replicated with an extra property on the response object, but then the caller must be aware of this property to check. As this processor gets more complex and the number of failure states grows, it brings us to the other feature that builds upon this.

# Discriminated Unions

The payment service supports two simple types (cash and credit card) and one simple method on it - processing a payment. This makes it an ideal candidate for leveraging F#'s [discriminated unions](https://learn.microsoft.com/en-us/dotnet/fsharp/language-reference/discriminated-unions). There are two places where this feature becomes useful:

1. Validation for each type of payment
2. The generic payment processing

For validation, C# almost requires that it live at the object level via inheritance, which causes the model to both define its properties and its validation and can cause the object to grow substantially in size. If validation were instead done inside of the processing, there is no compile-time guarantee that the processing has a validation step for each model.

Inside the payment processing, note that there is implicit code to check the different types of payment responses. There is tight coupling here between the payment processing and the state of the `PaymentBase` model:

```csharp
public static string ProcessPayment(PaymentBase payment) =>
    payment switch
    {
        _ when payment is null => "Invalid payment",
        _ when payment.IsValid() => $"Payment processed: {payment.GetPaymentSummary()}",
        _ => $"Invalid payment: {payment.Error}",
    };
```

F# can work around both of these problems by defining a set of values that the response can be and ensuring that the response is exactly one of these types. This means that when we define the `Payment` type, we can ensure that the corresponding process method must handle all of these types at compile time:

```fsharp
type Payment =
    | Cash of decimal
    | CreditCard of (string * decimal) /// (ccNum * amount)
// ...
let processPayment (payment: Payment) =
        // This will fail if all Payment types aren't matched
        match payment with
        | Cash amt ->
            sprintf $"Cash payment of {amt} received"
        | CreditCard (ccNum, amt) ->
            sprintf $"Credit card payment of {amt} received via credit card {ccNum}"
```

In the explicit processing functions (created for ease of use in C#), we can enforce validation and handle the different response types:

```fsharp
let processPaymentCash amount =
    let payment = createCashPayment amount
    match payment with
    | Ok p ->
        let result = processPayment p
        sprintf $"Payment processed: {result}"
    | Error msg ->
        sprintf $"Invalid payment: {msg}"
```

The result is more functional code that keeps the simple models separate from the processing logic, while enforcing that each model is appropriately processed.
