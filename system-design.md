# Digital Wallet — System Design

---

## 1. What is a Digital Wallet?

A digital wallet lets users:

- **Top up** money from a bank card
- **Pay** at e-commerce sites using wallet balance
- **Transfer** money to another user's wallet

---

## 2. User Registration & Login

Before doing anything, the user signs up and logs in.

```mermaid
flowchart TD
    A[User opens App / Web] --> B[Enter email + password]
    B --> C{New user?}
    C -- Yes --> D[Register account]
    D --> E[Verify email / phone OTP]
    E --> F[Account created]
    C -- No --> G[Login]
    G --> H[Issue JWT token]
    F --> H
    H --> I[Access wallet dashboard]
```



**Key points:**

- OTP verifies the user owns the phone/email
- A JWT token is issued after login — every API call carries this token
- Token expires (e.g. 15 min) and is refreshed silently

---

## 3. Top-Up — Adding Money to the Wallet

The user adds money from their bank card into the wallet.

```mermaid
flowchart LR
    A[User enters card details] --> B[App sends top-up request]
    B --> C[API Gateway validates token]
    C --> D[Top-Up Service]
    D --> E[Risk check — daily limit?]
    E -- Passed --> F[Send to Card Processor / PSP]
    F --> G{Payment success?}
    G -- Yes --> H[Ledger credits wallet balance]
    H --> I[Notify user — Top-up successful]
    G -- No --> J[Notify user — Payment failed]
```



**Key points:**

- The card processor (e.g. Stripe) charges the card
- Only after a success webhook from PSP does the balance update
- The ledger records a debit from "bank" and credit to "wallet" (double-entry)

---

## 4. Making a Payment

The user pays for something on an e-commerce site using wallet balance.

```mermaid
flowchart LR
    A[User clicks Pay] --> B[Merchant sends payment intent to API]
    B --> C[API Gateway]
    C --> D[Payment Service]
    D --> E[Check wallet balance]
    E -- Sufficient --> F[Risk & fraud check]
    F -- Cleared --> G[Debit wallet balance]
    G --> H[Credit merchant account]
    H --> I[Notify user and merchant]
    E -- Insufficient --> J[Return error to user]
```



**Key points:**

- Balance check and debit happen atomically (no partial updates)
- Both debit and credit are written together in the ledger
- Merchant gets notified via webhook

---

## 5. Wallet-to-Wallet Transfer

Sending money to another user on the same platform (like PayPal P2P).

```mermaid
flowchart LR
    A[Sender enters recipient + amount] --> B[App calls Transfer API]
    B --> C[Validate both accounts exist]
    C --> D[Check sender balance]
    D -- OK --> E[Debit sender wallet]
    E --> F[Credit recipient wallet]
    F --> G[Notify both users]
    D -- Insufficient --> H[Reject transfer]
```



**Key points:**

- This is faster than bank transfers — happens instantly within the platform
- No external PSP needed
- Both debit and credit must succeed together (atomic transaction)

---

## 6. High-Level System Overview

How all the pieces fit together on the backend.

```mermaid
flowchart TD
    subgraph Clients
        APP[Mobile App]
        WEB[Web Browser]
        MER[Merchant Site]
    end

    subgraph Backend
        GW[API Gateway]
        AUTH[Auth Service]
        WAL[Wallet Service]
        TOP[Top-Up Service]
        PAY[Payment Service]
        LED[Ledger Service]
        NOTIF[Notification Service]
    end

    subgraph Storage
        DB[(Database)]
        CACHE[(Redis Cache)]
    end

    PSP[Card Processor]

    APP --> GW
    WEB --> GW
    MER --> GW
    GW --> AUTH
    GW --> WAL
    GW --> TOP
    GW --> PAY
    TOP --> PSP
    PSP --> GW
    PAY --> LED
    TOP --> LED
    LED --> DB
    WAL --> CACHE
    LED --> NOTIF
    NOTIF --> APP
```



---

## 7. Key Design Decisions


| Concern             | Approach                                             |
| ------------------- | ---------------------------------------------------- |
| **Consistency**     | ACID database for all money movements                |
| **No double spend** | Atomic debit + credit in one transaction             |
| **Idempotency**     | Each request has a unique ID — retries are safe      |
| **Security**        | JWT auth on every request, OTP for sensitive actions |
| **Speed**           | Redis caches wallet balance for fast reads           |
| **Notifications**   | Async via event bus — doesn't block the transaction  |


