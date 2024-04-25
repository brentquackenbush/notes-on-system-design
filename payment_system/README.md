# System Design for Payment System

> There are many types of payment systems. For example a digital wallet like `Apple Pay` or `Google Pay`, also could be a backend system like `Stripe`, `Square`, `Venmo`, and `PayPal`.

For this system design, we will be focusing on a payment system like the payment backend for an _e-commerce_ application. We will not be creating a PSP from scratch and will introduce a payment-service-provider like `Stripe` and `Square`.

#### Things we won't be covering in this design
- Storing credit data in our system
- Multiple currencies

#### What we'll be covering in this design
- Pay-in-flow: payment system receives money from customers on behalf of sellers
- Pay-out-flow: payment system sends money to sellers around the world 

## Functional Requirements
- Pay-in-flow
- Pay-out-flow

## Non-functional Requirements
- Failed payments need to be carefully handled, so reliability of our system and fault tolerance is of up-most importance
- A reconciliation process between internal services (payment systems, accounting systems) and external services (psp) is required. The process async verifies that the payment information across these systems is consistent

## Back-of-the-envelope estimations

Let's assume we are receiving 1 million transactions per-a-day in our backend system. 
1,000,000 / 10^5 seconds = 10 transactions per second (TPS). This is not a huge number for a typical database. 

## High-Level Design

#### How money flows in our system

> On an e-commerce website like `Etsy`, after a buyer places an order, the money flows into Etsy's bank account, which is noted as the `pay-in-flow`. Etsy does not own all the money in the bank account, a substantial amount will be provided to the sellers. When the product is delivered and the money is released, the balance after the fees then flows from Etsy's bank account into the seller's bank account, this is noted as the `pay-out-flow`. 

### Payment Service
 
The payment service is tasked with initiating the payment transaction workflow by receiving payments from users. The primary operation it performs at the onset is a comprehensive risk assessment. This pivotal step involves an intricate analysis to ensure full compliance with `Anti-Money Laundering` (AML) and `Countering the Financing of Terrorism` (CFT) regulations. It meticulously screens for any indications of illicit activities, such as money laundering or terrorism financing. To perform this complex and nuanced task, the service integrates with specialized third-party providers, leveraging their expertise to accurately assess and mitigate potential risks. Payments that successfully pass this stringent risk check are then securely processed. The strategic decision to partner with these third-party services stems from the intricacy and high degree of specialization required for such risk assessments, ensuring that our platform remains secure and trustworthy.

### PSP

- **Interface with Merchants**: PSPs offer merchants a payment gateway interface that allows them to accept various forms of electronic payments (credit cards, bank transfers, e-wallets, etc.).
- **Payment Processing**: They process payment requests, perform fraud checks, and ensure secure transmission of payment information.
- **Merchant Accounts**: PSPs can provide merchants with a merchant account, enabling them to receive payments from sales.
- **Multi-Channel Payment Solutions**: They often support multiple channels (online, mobile, in-store) and a range of payment methods.
- **Settlement**: PSPs handle the settlement of funds to the merchant's account after the transaction is completed.

### Card Schemes

- **Payment Network**: Card schemes like `Visa` or `MasterCard` operate the networks that process payments between banks (acquiring banks and issuing banks).
- **Regulation and Standards**: They establish and maintain the card network rules, security standards, and ensure compliance.
- **Interbank Clearing**: They facilitate the clearing and settlement process between the customer's bank and the merchant's bank.