# DNSSEC Using Route 53

## Standard DNS Resolution Flow (Without DNSSEC)

- The resolution path for `animals4life.org` typically follows:

  1. Client (e.g., laptop)
  2. DNS resolver
  3. Root name servers (for TLD info)
  4. TLD name servers (e.g., `.org`)
  5. Route 53 name servers for `animals4life.org`

- In a VPC, EC2 instances use the `.2` address (Route 53 Resolver) to resolve DNS queries.

---

## Enabling DNSSEC in Route 53

### 1. Creating a Key Signing Key (KSK)

- Done via the Route 53 console or CLI.
- Involves creating an **asymmetric KMS key** (public/private pair) in **US East (N. Virginia)**.
- Conceptually, this forms the **KSK**, although the KSK is derived from these keys internally.

### 2. Zone Signing Keys (ZSK)

- Internally generated and managed by Route 53.
- **Not handled by KMS**.

### 3. Publishing Public Keys

- Route 53 publishes the public parts of the KSK and ZSK in a **DNSKEY** record.
- These keys allow DNSSEC resolvers to validate other records.

### 4. Signing the DNSKEY Record

- Route 53 uses the **private KSK** to sign the DNSKEY record.
- This results in an **RRSIG DNSKEY** record.

---

## Establishing the Chain of Trust

### 5. Delegated Signer (DS) Record

- A **DS record** (hash of the KSK public key) must be added to the parent zone (e.g., `.org`).
- If the domain was registered with Route 53:

  - The DS record is added via the **Registered Domains** section.

- If registered elsewhere:

  - The DS record must be added **manually**.

---

## Monitoring and Validation

### 6. Monitoring with CloudWatch

- Create alarms for:

  - `DNSSECInternalFailure`
  - `DNSSECKeySigningKeysNeedingAction`

### 7. Optional: DNSSEC Validation in VPC

- Ensures Route 53 resolvers in VPCs **fail DNSSEC-invalid records**.
- Does **not** affect non-DNSSEC zones.

---

## Practical Demo Steps

### Enabling DNSSEC in Console

1. Open Route 53 → Hosted Zones.
2. Select the zone `animals4life.org`.
3. Go to **DNSSEC Signing** → Enable.
4. Name the KSK (e.g., `A4L-KSK`).
5. Create a new **Customer Managed KMS key** (e.g., `A4L-KSK-KMS`).
6. Click **Create KSK and Enable Signing**.

### Validating DNSSEC Records

- Initially, running:

  ```bash
  dig animalsforlife.org DNSKEY +dnssec
  ```

  returns nothing.

- After propagation, it returns:

  - `256` = ZSK public
  - `257` = KSK public
  - `RRSIG` = signed DNSKEY record

### Creating the DS Record

1. Go to **Registered Domains**.
2. Select `animals4life.org`.
3. Under **DNSSEC Status**, click **Manage Keys**.
4. Select algorithm `ECDSAP256SHA256`.
5. Copy public key from hosted zone DNSSEC info.
6. Paste it and **Add** the record.

### Verifying DS Record in TLD

- Get `.org` name servers:

  ```bash
  dig org NS +short
  ```

- Pick one, then query:

  ```bash
  dig animalsforlife.org DS @<org-ns>
  ```

- Should return a DS record.

---

## Testing End-to-End

### Add a Test Record

```bash
Name: test.animals4life.org
Type: A
Value: 1.1.1.1
TTL: 60
```

### Validate with DNSSEC

```bash
# Normal DNS lookup
 dig test.animals4life.org A

# DNSSEC-enabled lookup
 dig test.animals4life.org A +dnssec
```

- Expect to see `RRSIG` in the second command output.

---

## Summary

- DNSSEC in Route 53 involves both KSK and ZSK.
- KSK uses KMS (must be in `us-east-1`), ZSK managed by Route 53.
- DS record in the parent zone is key to trust chain.
- Validation ensures DNS responses are authentic and unaltered.
- Monitoring and optional VPC-level validation help maintain integrity.

---

✅ DNSSEC is now enabled and fully configured for `animals4life.org` using Route 53.
