# QuizSpin – Ultra Master Prompt (Android Java + Firebase + Admin)

Use this prompt in Replit AI (or any code generator) to produce a production-ready baseline that you can import into Android Studio.

## Prompt

Create a complete production-ready Android application in **Java** using Android Studio project structure.

**App Name:** QuizSpin

### 1) Authentication & Device Security
- Phone OTP login using Firebase Authentication.
- Save `ANDROID_ID` as `deviceId` on first login.
- Block access if `deviceId` mismatches on later logins.
- Enable Firebase App Check using Play Integrity.

### 2) Firestore Data Model
Create collections and fields:

- `users/{uid}`
  - mobile
  - deviceId
  - walletChips
  - withdrawBalance
  - spinCount
  - lastSpinDate
  - lastScratchDate
  - weeklyScore
  - referralCode
  - referredBy
  - totalReferrals
  - referralBonusGiven
  - quizSessionsCompleted
  - totalSpins
  - lastWithdrawAt
  - isBlocked
  - createdAt

- `referrals/{referralId}`
  - referrerId
  - referredUserId
  - referredMobile
  - referredDeviceId
  - status (`pending`, `approved`, `rejected`)
  - reason
  - timestamp

- `withdrawRequests/{requestId}`
  - userId
  - amount
  - upiId
  - status (`pending`, `approved`, `rejected`)
  - reviewedBy
  - reviewedAt
  - createdAt

- `transactions/{txId}`
  - userId
  - type (`spin`, `scratch`, `quiz`, `referral_bonus`, `chips_convert`, `withdraw_request`, `admin_adjustment`)
  - amount
  - meta
  - timestamp

- `leaderboard/{uid}`
  - weeklyScore
  - updatedAt

- `fraudLogs/{logId}`
  - userId
  - eventType
  - severity
  - details
  - createdAt

### 3) Spin & Win
- Max **15 spins/day**.
- Reward generated **only** in Firebase Cloud Function.
- Probability distribution:
  - 40% → 5 chips
  - 25% → 10 chips
  - 15% → 20 chips
  - 10% → 50 chips
  - 5% → 100 chips
  - 5% → 500 chips
- Use Firestore transactions for atomic updates.
- Log every reward in `transactions`.
- Return spins left in response.

### 4) Scratch Card
- 1 free scratch/day.
- Extra scratch allowed only after rewarded ad verification.
- Reward must be server-generated.
- Enforce daily rule in Cloud Function.

### 5) GK & IT Quiz
- 10 questions/session.
- +10 chips per correct answer.
- 1 revive via rewarded ad.
- On completion call `quizComplete` Cloud Function.
- Function validates score, updates `weeklyScore`, updates `leaderboard`, and logs transaction.

### 6) Referral System (Organic + Anti-Fraud)
- Each user gets unique `referralCode`.
- No self-referral.
- No same-device referral (`deviceId` must differ).
- One referral count per referred mobile number.
- Referral reward (example 100 chips) only when referred user is eligible:
  - completed at least 3 quiz sessions **or** 5 spins,
  - active for at least 24 hours from signup,
  - App Check passes,
  - account not blocked/fraud-flagged.
- Flow: `pending` → `approved` after eligibility check.
- Log referral decisions.

### 7) Chips → Money Conversion
- Conversion: **1000 chips = ₹10**.
- Conversion only through Cloud Function (`convertChips`).
- Atomic chips deduction + withdraw balance increment.
- Log transaction.

### 8) Withdraw System
- Minimum withdraw: **₹100**.
- Max 1 withdraw request per 24 hours.
- Create `withdrawRequests` with `pending` status.
- Deduct `withdrawBalance` atomically when request is created.
- Admin approval/rejection handled separately (`adminApproveWithdraw`).
- No auto-payout implementation.

### 9) Fraud Protection
- Reject requests on device mismatch.
- Add API rate limiting in functions.
- Add abnormal wallet growth checks and fraud logs.
- Block direct wallet/spin field updates from client.
- Enforce App Check.
- Add admin override endpoint with audit logging.

### 10) Ads (AdMob)
- Integrate rewarded ads.
- Reward only after ad completion signal.
- Never credit wallet on client.
- Add ad frequency caps.

### 11) Weekly Leaderboard
- Top 100 users by `weeklyScore` descending.
- Read-only from client.

### 12) Admin Dashboard (Web)
Build Firebase Hosting-ready dashboard (HTML/CSS/JS):
- Admin login.
- View users, wallets, referrals, transactions, withdraw requests.
- Approve/reject withdrawals.
- Block/unblock user.
- Fraud indicator column.
- Responsive UI.

### 13) Android Architecture
Use MVVM with folder structure:
- `data/`
- `ui/`
- `ads/`
- `security/`
- `utils/`
- `services/`

Include:
- Java classes
- XML layouts
- Repositories/ViewModels
- Cloud Function client wrappers
- Error/loading state handling

### 14) Production Configuration
Include:
- **Gradle Kotlin DSL files** (`settings.gradle.kts`, project/module `build.gradle.kts`)
- `AndroidManifest.xml`
- `Application` class
- ProGuard/R8 rules
- Crashlytics + Analytics events
- Network security config
- Release signing/build setup

### 15) Cloud Functions to Implement (Node.js)
Generate full production-style code for:
- `spinReward`
- `scratchReward`
- `quizComplete`
- `applyReferral`
- `approveReferral`
- `convertChips`
- `requestWithdraw`
- `adminApproveWithdraw`
- `deviceValidation`
- `fraudDetectionLogger`

### 16) Firestore Security Rules
Write strict rules:
- Auth required for all user reads.
- Users can read only their own `users/{uid}`.
- No client writes to `walletChips`, `withdrawBalance`, `spinCount`, `weeklyScore`.
- `leaderboard` read-only for clients.
- `transactions` read-only for clients.
- Only admin roles/functions can perform privileged writes.

### Mandatory Constraints
- Never generate rewards on client side.
- All wallet mutations must be server-controlled.
- Use Firestore transactions for monetary/chips integrity.
- Enforce App Check and anti-abuse checks.
- Keep payout logic sustainable: payout must not exceed ad revenue target threshold.
- Follow Google Play policy; avoid misleading “easy cash” claims.

## Required Output Format
Return code in clear sections and keep files complete (not partial snippets):
1. Android app files (Java + XML + manifests + Gradle Kotlin DSL)
2. Firebase Cloud Functions (Node.js)
3. Firestore Security Rules
4. Admin dashboard (HTML/CSS/JS)
5. Setup steps and run instructions

## Recommended Generator Workflow
1. Generate backend (Cloud Functions + security rules) first.
2. Generate Android app (MVVM + screens + function calls).
3. Generate admin panel.
4. Fix build/runtime errors iteratively.

## Reality Check
- Replit can generate structure and code quickly.
- Final Android compile/signing/AAB should be done in Android Studio with Android SDK + Gradle Kotlin DSL (`.kts`) project files.
