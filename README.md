# UPI Offline Mesh

A Spring Boot project that demonstrates how a UPI-style payment can be made **without internet** by passing it from phone to phone until one of them gets connectivity and uploads it to the server for settlement.


## How it works

1. The sender's phone encrypts the payment and creates a packet.
2. The packet is passed between nearby devices (gossip).
3. A "bridge" device with internet uploads it to the backend.
4. The backend decrypts, checks it, and settles the payment exactly once.

## Key features

- **Secure transfer:** hybrid encryption (RSA-OAEP + AES-256-GCM) so intermediate devices can't read or modify the payment.
- **No double payments:** the SHA-256 hash of the ciphertext is claimed atomically, so a packet delivered by several bridges settles only once.
- **Replay protection:** each payment has a timestamp and a unique nonce, and old packets are rejected.
- **Atomic settlement:** debit, credit, and ledger entry happen in a single transaction.

## Tech stack

Java 17, Spring Boot 3, Spring Data JPA, H2 (in-memory), Maven

## Run locally

Requires JDK 17 or newer.

```
# Windows
.\mvnw.cmd spring-boot:run

# Mac / Linux
./mvnw spring-boot:run
```

Then open http://localhost:8080 to use the dashboard.

Run the tests:

```
.\mvnw.cmd test
```

## Demo steps

1. Compose a payment and click **Inject into Mesh**.
2. Click **Run Gossip Round** twice.
3. Click **Bridges Upload to Backend** and watch the balances and ledger update.
4. Upload again to see the duplicate being dropped.

## Limitations

This is a demo, not a production payment system. The receiver can't verify the sender's funds while offline, and a sender could try to double-spend, so it's really *deferred settlement*. A real system would also need sender signatures, a production database, Redis for idempotency, and real Bluetooth networking.

