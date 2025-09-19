
# STX-Zyvento - Event Attendance & Rewards Smart Contract (Clarity)

This Clarity smart contract provides a decentralized system for managing events, tracking attendance, verifying participant presence, and rewarding attendees based on their engagement.

---

## 🧩 Features

* ✅ **Event Creation** by a designated contract owner
* ⏱ **Attendance Tracking** (check-in/check-out with block-height duration)
* 🔒 **Verifier-based Verification** system
* 🎁 **Reward System** with tiers (base + bonus)
* 🔐 **Authorization Controls** for admin-only functions
* 📚 **Read-only views** for external interfaces (e.g., frontends or indexers)

---

## 📦 Data Structures

### 📌 Event (`events`)

Each event has the following attributes:

```clojure
{
  name: (string-ascii 50),
  description: (string-ascii 200),
  start-height: uint,
  end-height: uint,
  base-reward: uint,
  bonus-reward: uint,
  min-attendance-duration: uint,
  organizer: principal,
  is-active: bool
}
```

### ⏳ Attendance (`event-attendance`)

Tracks the attendance details for each attendee:

```clojure
{
  check-in-height: uint,
  check-out-height: uint,
  duration: uint,
  verified: bool
}
```

### ✅ Verification (`verification-details`)

Stores who verified an attendee and when:

```clojure
{
  verified-by: principal,
  verified-at: uint
}
```

### 🏆 Reward Claim Status (`rewards-claimed`)

Indicates whether a reward has been claimed and its tier:

```clojure
{
  amount: uint,
  claimed-at: uint,
  reward-tier: uint
}
```

---

## 🧪 Constants

### 🔐 Authorization Errors

```clojure
ERR-NOT-AUTHORIZED           ;; u100
ERR-ALREADY-CLAIMED          ;; u101
ERR-EVENT-NOT-ENDED          ;; u102
ERR-EVENT-ENDED              ;; u103
ERR-NO-REWARD                ;; u104
ERR-EVENT-NOT-FOUND          ;; u105
ERR-INSUFFICIENT-FUNDS       ;; u106
ERR-INVALID-DURATION         ;; u107
ERR-ALREADY-REGISTERED       ;; u108
```

### ⛔ Input Validation Errors

```clojure
ERR-INVALID-START-HEIGHT     ;; u110
ERR-INVALID-REWARD           ;; u111
ERR-INVALID-MIN-ATTENDANCE   ;; u112
ERR-INVALID-NAME             ;; u2000
ERR-INVALID-DESCRIPTION      ;; u2001
ERR-CONTAINS-INVALID-CHARS   ;; u2002
```

### ⏳ Duration Constants

```clojure
MAX-DURATION     ;; u52560 (~1 year in 10-min blocks)
MIN-DURATION     ;; u144 (~1 day)
```

### 💰 Reward Cap

```clojure
MAX-REWARD       ;; u1000000000000 (1000 STX)
```

---

## 🧑‍💼 Contract Owner

Only the contract owner (`contract-owner`) can:

* Create events
* Potentially manage verifiers (in future extensions)

---

## 🛠️ Public Functions

### 📍 `create-event`

Creates a new event with parameters:

```clojure
(name (string-ascii 50),
 description (string-ascii 200),
 start-height uint,
 duration uint,
 base-reward uint,
 bonus-reward uint,
 min-attendance uint)
```

#### 🧾 Validations:

* Name and description must be ASCII, proper length, no leading/trailing spaces
* `start-height` must be in the future
* `duration` must be within allowed bounds
* `reward` must be within max limits and non-zero
* `min-attendance` must be > 0 and ≤ duration

Returns the `event-id` if successful.

---

## 📖 Read-Only Functions

These functions do not alter state and can be used by front-ends:

### 🔎 `get-owner` → `principal`

Returns the current contract owner.

### 🔎 `get-event (event-id uint)` → `(optional ...)`

Returns the event details.

### 🔎 `get-attendance-record (event-id uint) (attendee principal)` → `(optional ...)`

Returns the attendance data for a user.

### 🔎 `get-reward-claim (event-id uint) (attendee principal)` → `(optional ...)`

Returns details of any claimed reward.

### 🔎 `is-verifier (address principal)` → `bool`

Checks if a principal is a registered verifier.

---

## 🔒 Maps

| Map Name               | Key Format                                | Value Format        |
| ---------------------- | ----------------------------------------- | ------------------- |
| `events`               | `uint`                                    | `event-struct`      |
| `event-attendance`     | `{ event-id: uint, attendee: principal }` | `attendance-struct` |
| `verification-details` | `{ event-id: uint, attendee: principal }` | `verification-data` |
| `rewards-claimed`      | `{ event-id: uint, attendee: principal }` | `claim-record`      |
| `verifiers`            | `principal`                               | `bool`              |

---

## 🧰 Helper Functions

### 🔍 `is-valid-ascii (s (string-ascii 200))`

Validates that:

* The string is not empty
* First and last characters are not spaces

Used in name/description validation.

---

## ✅ Future Extensions

This contract is modular and can be extended with:

* Attendance check-in/check-out mechanisms
* Reward claiming functionality
* Admin interface for managing verifiers
* Blacklist/whitelist features for event access
* Integration with NFT badges for event participation

---

## 🚀 Deployment Notes

* Requires STX funding for the contract for reward payouts (if implemented)
* Should be deployed by a trusted contract owner
* Audit before use in production

---

## 📄 License

MIT License (or insert your preferred license here)

---
