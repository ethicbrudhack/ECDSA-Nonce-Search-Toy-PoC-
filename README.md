# ECDSA Nonce-Search Toy PoC (English README)

**Purpose:**  
This repository is a small *proof-of-concept* (PoC) demonstrating a toy approach to exploring how candidate nonces (`k`) and private keys (`d`) might be enumerated and tested against example signature parameters. It is intended **only** for educational, research and defensive testing of cryptographic implementations (e.g., to show why predictable or low-entropy nonces are dangerous). Do **not** use this code against keys or systems you do not own or do not have explicit permission to test.

---

## Overview

This project contains a self-contained Python script that:

- Defines a compact, illustrative environment for ECDSA-like signature generation and checking.
- Repeatedly generates candidate nonces `k` and private keys `d`, derives a synthetic signature `(r, s)` and compares it to target signature parameters.
- Maintains short in-memory history windows of recent `k`, `r`, `s` values and can reset its strategy after a configurable number of attempts.
- Supports a simple parallel execution wrapper to run tests for multiple target signatures concurrently.

This implementation is intentionally simplified and uses placeholder math for elliptic curve operations (e.g., `G = (1,2)` and a tiny example modulus `n`) for educational demonstration only.

---

## High-level design (non-actionable)

1. **Configuration constants**
   - `n`, `G`: small illustrative modulus and generator tuple used by the toy routines. In real ECDSA these are curve-specific constants (e.g., SECP256k1) and must not be substituted carelessly.
   - `MAX_HISTORY`, `CHANGE_STRATEGY_AFTER`: control how many historical values are kept and when the script resets its internal state.
   - `TARGET_ADDRESS`: example target address to demonstrate a different stopping condition (toy address format).

2. **Core functions (toy versions)**
   - `mod_inv(k, n)`: modular inverse helper using Python’s `pow`.
   - `generate_public_key(d)`: placeholder function that maps a private scalar to a public point (toy multiplication).
   - `public_key_to_address(pub_key)`: toy hashing to produce a string used as an address-like check.
   - `generate_signature(z, k, priv_key)`: produces a toy `(r, s)` pair from `z`, `k`, and `d`. This is **not** a full, standards-compliant signature implementation — it is simplified for demonstration.
   - `predict_k(...)`: stub for a nonce prediction strategy; returns a random `k` by default and is designed to be replaced with defensive-only experiments.
   - `find_matching_dk(...)`: main loop that generates candidate `(k, d)`, computes a toy signature and checks whether it matches a target `(r, s)` or target address. It logs progress and resets history after a configurable number of unsuccessful attempts.
   - `parallel_test(transactions)`: runs `find_matching_dk` for a list of target transactions using `multiprocessing.Pool`.

3. **Execution**
   - The `__main__` block demonstrates running the PoC against a small list of example transactions in parallel. It uses `freeze_support()` to be Windows-compatible for multiprocessing.

---

## Intended use & ethics

- Intended for research, education and defensive testing only: e.g., to test how poorly randomized nonces might enable key-recovery in a *controlled* lab environment.
- **Do not** run this against third-party wallets, live systems, or keys you do not control. Attempting unauthorized key recovery is illegal and unethical.
- If you discover an implementation weakness in a third-party system, follow responsible disclosure procedures and report it to the project maintainers.

---

## Limitations

- This is a toy PoC with simplified math and **not** cryptographically valid elliptic curve arithmetic. It cannot and should not be used as a drop-in attack tool.
- The `predict_k` function is a simple stub that returns random values. Any attempt to make it a practical attack requires domain expertise and would cross ethical/legal boundaries if used against unauthorized targets.
- Real-world ECDSA uses large curve parameters (e.g., SECP256k1), secure nonce generation (CSPRNGs or RFC 6979), and hardened implementations that resist such enumeration.

---

## Files & structure

- `poC_toy_nonce_search.py` — the main toy PoC script (example dataset + search loop + parallel runner).
- `README.md` — this document.
- (Optional) add a `LICENSE` if you want to permit reuse; absent a license, assume “All rights reserved”.

---

## Defensive recommendations

- Use deterministic, standards-based nonces (RFC 6979) or a cryptographically secure RNG for nonce generation.
- Never derive nonces from low-entropy or predictable sources (timestamps, weak RNGs, user input).
- Add unit tests and entropy checks in cryptographic code paths and audit implementations regularly.

---

## Final notes

This repository is an educational tool to illustrate concepts around nonce predictability and why strong randomness matters in signature systems. The code intentionally avoids providing practical attack instructions, and it should only be used in safe, authorized environments to improve implementation security.

BTC donation address: bc1q4nyq7kr4nwq6zw35pg0zl0k9jmdmtmadlfvqhr
