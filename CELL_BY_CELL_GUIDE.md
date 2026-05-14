# Complete Step-by-Step Guide for Stripe v2 FA & Issuing Live Testing

This document explains exactly what each cell in your Jupyter notebook does. Use this as a reference while running your tests.

---

## 📋 Pre-Session Checklist

Before running Cell 1:
- [ ] Jupyter notebook is running
- [ ] You have your LIVE secret key ready
- [ ] Platform account has been onboarded (excelsior tasks complete)
- [ ] Team is ready on call/zoom
- [ ] You have access to company bank account (for funding step)

---

## Cell 1: Setup and Configuration

### 🎯 Purpose
Initialize the testing environment with required libraries, API configuration, and helper functions.

### 🔍 What This Cell Does

**Imports:**
- `requests`: Makes HTTP API calls to Stripe
- `json`: Formats API responses for readability
- `datetime`: Timestamps for logging
- `Dict, Any`: Type hints for better code clarity

**Configuration Variables:**
- `SECRET_KEY`: Your LIVE Stripe secret key (⚠️ UPDATE THIS!)
- `API_VERSION`: "2025-09-30.preview" - the v2 API version
- `test_data`: Dictionary to store all IDs created during testing

**Helper Functions:**
1. `print_section(title)`: Prints formatted headers to separate test steps
2. `print_result(response, show_full)`: Displays API responses cleanly
   - Shows status code (200 = success, 4xx = error)
   - Extracts key fields (id, status, balance)
   - Can show full JSON if needed
3. `v2_headers()`: Generates correct headers for v2 API calls
   - Includes Authorization, Stripe-Version, Content-Type
   - Optionally adds Stripe-Account or Stripe-Context
4. `v1_auth()`: Returns auth tuple for v1 API calls

### ✅ Expected Output
```
✅ Setup complete!
API Version: 2025-09-30.preview
Secret Key: sk_live_51Abc...
```

### ⚠️ ACTION REQUIRED
**Before running:** Change `SECRET_KEY = "sk_live_YOUR_SECRET_KEY_HERE"` to your actual LIVE key

### 📝 Document
- Confirm setup completed without errors
- Note the API version being used
- Take screenshot if helpful

---

## Cell 2: Step 1 - Platform Setup Verification

### 🎯 Purpose
Verify your platform has been properly onboarded for Issuing by listing available card programs.

### 🔍 What This Cell Does

**API Call:**
```
GET /v1/issuing/programs
Headers: 
  - Authorization: Bearer {your_secret_key}
  - Stripe-Version: 2025-09-30.preview; issuing_program_beta=v2
```

**Process:**
1. Calls Issuing Programs API
2. Lists all card programs your platform can access
3. Stores the first program ID in `test_data['card_program_id']`
4. Displays program ID and name

### ✅ Expected Output
```
Status: 200
✅ SUCCESS
📋 Card Program ID: iprg_1SLow67HINfPfCx4SOxnW562

Available Programs:
  - iprg_xxx: UK Program Name
```

### ❌ Possible Errors

**No programs found:**
- **Cause**: Excelsior task "Add merchant to Issuing Program" not completed
- **Fix**: Complete excelsior task, wait 5-10 minutes, retry

**401 Unauthorized:**
- **Cause**: Invalid SECRET_KEY
- **Fix**: Check your secret key is correct and for the right account

### 📝 Document
- How many programs were returned?
- What is the program ID? (You'll need this in Step 2B)
- Does the program name match what you expected?

**Add a markdown cell below with your notes:**
```markdown
### Step 1 Results - [TIME]
- Programs found: X
- Selected program: iprg_xxx
- Program name: [name]
- Issues: [none/describe]
```

---

## Cell 3: Step 2A - Create v2 Connected Account

### 🎯 Purpose
Create a new Connected Account (representing your customer/merchant) with full v2 FA and Issuing capabilities.

### 🔍 What This Cell Does

**API Call:**
```
POST /v2/core/accounts
Body: Complete account configuration (see notebook for full payload)
```

**Configurations Being Set:**

1. **Storer (v2 Financial Accounts)**
   - `holds_currencies.gbp`: Can store GBP
   - `financial_addresses.bank_accounts`: Can receive bank transfers
   - `inbound_transfers.bank_accounts`: Can pull money from banks
   - `outbound_transfers`: Can send money to own FAs or external banks
   - `outbound_payments`: Can pay recipients, cards, other FAs

2. **Merchant (Payment Acceptance)**
   - `card_payments`: Accept card payments
   - `bacs_debit_payments`: Accept BACS Direct Debit
   - `gb_bank_transfer_payments`: Accept GB bank transfers
   - MCC: 5734 (Computer Software Stores)
   - Statement descriptor: "LIVE_TEST"

3. **Card Creator (Issuing)**
   - `commercial.stripe.prepaid_card`: Can issue commercial prepaid cards

4. **Recipient (Receiving Payouts)**
   - `stripe_balance.stripe_transfers`: Can receive transfers to Stripe balance

**Other Settings:**
- Country: GB (United Kingdom)
- Entity type: Company
- Currency: GBP
- Dashboard: none (no Express Dashboard access)
- Responsibilities: Platform collects fees and losses

### ✅ Expected Output
```
Status: 200
✅ SUCCESS
ID: acct_1SRtXB7IkPsLCet4
Object: account

📝 Connected Account ID: acct_1SRtXB7IkPsLCet4
Status: pending
```

### 📋 Understanding Account Status

**Status: "pending"**
- Normal for new accounts
- Means account created but needs onboarding
- Can still create FAs and cards
- Cannot process real transactions until active

**Status: "requires_onboarding"**
- Similar to pending
- May need account link to complete info

**Status: "active"**
- Fully onboarded
- Can process real transactions

### ⚠️ Important Notes

- This account is in LIVE mode - it's a real account
- The ID `acct_xxx` will be used in all subsequent steps
- Email notifications may be sent to `live-dogfooding@test.com`
- Dashboard access is disabled (`"dashboard": "none"`)

### ❌ Possible Errors

**400 Bad Request - "Country not supported":**
- **Cause**: Trying to create in unsupported country
- **Fix**: Ensure `country: "GB"` for UK testing

**400 Bad Request - "Configuration not available":**
- **Cause**: Platform not onboarded for v2 or Issuing
- **Fix**: Complete excelsior tasks

### 📝 Document
```markdown
### Step 2A Results - [TIME]
- Account ID: acct_xxx
- Status: [pending/active/etc]
- Response time: [X]s
- All 4 configs present: ✅ Storer ✅ Merchant ✅ Card Creator ✅ Recipient
- Issues: [none/describe]
- Next: [Create account link / Skip to Step 2B]
```

---

## Cell 4: Optional - Create Account Link

### 🎯 Purpose
Generate a Stripe-hosted onboarding URL if the Connected Account needs to complete requirements.

### 🔍 What This Cell Does

**API Call:**
```
POST /v1/account_links
Body:
  - account: The Connected Account ID from Step 2A
  - refresh_url: Where to redirect if link expires
  - return_url: Where to redirect after completion
  - type: account_onboarding
```

**Process:**
1. Checks if Connected Account ID exists
2. Creates onboarding link using v1 API
3. Returns a URL valid for ~1 hour
4. URL allows the account owner to complete onboarding

### ✅ Expected Output
```
Status: 200
✅ SUCCESS

🔗 Onboarding URL: https://connect.stripe.com/setup/s/acct_xxx/token_xxx

Open this URL to complete onboarding.
```

### 🤔 When to Use This

**Use account link if:**
- Account status is "requires_onboarding"
- Account has items in `requirements.currently_due`
- You need to collect banking details, business info, or identity verification

**Skip if:**
- Account status is "active" or "pending" with no requirements
- You provided all required info in the account creation payload
- You're just testing and don't need full onboarding

### ⚠️ Important Notes

- Link expires after ~1 hour
- Can create new links if expired
- Using v1 account links (not v2) because v2 account links are for recipients
- The refresh_url and return_url should be real URLs in production

### 📝 Document
```markdown
### Account Link Results - [TIME]
- Link created: ✅ / Skipped
- Requirements needed: [list if any]
- Completed onboarding: ✅ / ❌
- Time to complete: [X] minutes
- What info was required: [business details / bank account / etc]
```

---

## Cell 5: Step 2B - Add Issuing Capability

### 🎯 Purpose
Attach the platform's card program to the Connected Account, enabling card issuance.

### 🔍 What This Cell Does

**Pre-checks:**
- Verifies Connected Account ID exists (from Step 2A)
- Verifies Card Program ID exists (from Step 1)

**API Call:**
```
POST /v1/issuing/programs
Headers:
  - Stripe-Version: 2025-09-30.preview; issuing_program_beta=v2
  - Stripe-Account: {connected_account_id}
Body:
  - platform_program: {card_program_id}
```

**What's Happening:**
1. Takes the platform-level card program (from Step 1)
2. Creates a connection between that program and the Connected Account
3. This gives the CA permission to issue cards under that program
4. Different from v1 where you'd just enable a capability

### 📋 v1 vs v2 Issuing Onboarding

**OLD Way (v1):**
```python
POST /v1/accounts/{id}
{
  "capabilities": {
    "card_issuing": {"requested": true}
  }
}
```

**NEW Way (v2 with programs):**
```python
POST /v1/issuing/programs
Headers: Stripe-Account: {ca_id}
{
  "platform_program": "{program_id}"
}
```

**Why the change?**
- Programs allow different card configurations per CA
- Better isolation and program management
- Supports multiple card programs per platform

### ✅ Expected Output
```
Status: 200
✅ SUCCESS

✅ Issuing capability added!
Program ID: iprg_xxx
```

### ❌ Possible Errors

**404 "No such program":**
- **Cause**: Wrong platform_program ID
- **Fix**: Check Step 1 output, use correct program ID

**403 "Not authorized":**
- **Cause**: Platform not enrolled in card program
- **Fix**: Complete excelsior task "Add merchant to Issuing Program"

**400 "Account already has program":**
- **Cause**: Program already attached (maybe from previous test)
- **Fix**: This is actually fine, you can proceed!

### 📝 Document
```markdown
### Step 2B Results - [TIME]
- Program attached: ✅
- Program ID used: iprg_xxx
- Connected Account: acct_xxx
- Any errors: [none/describe]
- Retry needed: ✅ / ❌
```

---

## Cell 6: Verify Account Configuration

### 🎯 Purpose
Confirm all four configurations (storer, merchant, card_creator, recipient) are active on the Connected Account.

### 🔍 What This Cell Does

**API Call:**
```
GET /v2/core/accounts/{connected_account_id}
Query params:
  - include[0]: configuration.storer
  - include[1]: configuration.merchant
  - include[2]: configuration.card_creator
  - include[3]: requirements
```

**Process:**
1. Retrieves full account object
2. Includes all configuration details
3. Displays a checkbox summary of which configs are present
4. Shows account status and requirements

### ✅ Expected Output
```
Status: 200
✅ SUCCESS

📋 Configuration Summary:
  Storer: ✅
  Merchant: ✅
  Card Creator: ✅
  Recipient: ✅
```

### 🔍 What to Look For

**In configuration.storer:**
- capabilities.holds_currencies.gbp.status should be "active" or "pending"
- capabilities.outbound_payments should be present

**In configuration.card_creator:**
- capabilities.commercial.stripe.prepaid_card should be present
- This confirms Issuing was added in Step 2B

**In requirements:**
- `currently_due`: Should be empty array `[]` if fully onboarded
- `pending_verification`: Items being verified
- `errors`: Any issues that need fixing

### 📝 Document
```markdown
### Configuration Verification - [TIME]
- All configs present: ✅ / ❌
- Storer status: [active/pending]
- Card Creator present: ✅ (confirms Step 2B worked)
- Requirements currently due: [none / list them]
- Account ready for next step: ✅ / ❌
```

---

## Cell 7: Step 3A - Create Financial Account

### 🎯 Purpose
Create a v2 Financial Account to store money and back card spend.

### 🔍 What This Cell Does

**API Call:**
```
POST /v2/money_management/financial_accounts
Headers:
  - Stripe-Account: {connected_account_id}
Body:
  - type: "storage"
  - storage.holds_currencies: ["gbp"]
  - display_name: "Main Account"
```

**What's a Financial Account?**
- Like a bank account within Stripe
- Stores money in one or more currencies
- Can send/receive transfers and payments
- Can back Issuing card spend
- Shows up in the Money section of Connect Dashboard (if enabled)

**Storage Type:**
- There are different FA types (storage, payment, etc.)
- "storage" is for holding money long-term
- Can have multiple storage FAs per Connected Account

### ✅ Expected Output
```
Status: 200
✅ SUCCESS
ID: fa_test_65TbHw7DfmXAWmdLid116TbHUX8xE9SZq6wjBP6SI7U9Ps
Object: financial_account

💰 Financial Account ID: fa_test_xxx
```

### 📋 Initial Balance

**Expected: £0.00**
- New FAs start with zero balance
- Balance structure:
  ```json
  {
    "gbp": {
      "available": 0,
      "pending": 0
    }
  }
  ```

### ❌ Possible Errors

**403 "Not authorized":**
- **Cause**: Storer configuration not active
- **Fix**: Check Step 6 verification, ensure storer is active

**400 "Currency not supported":**
- **Cause**: Requested currency not enabled in storer configuration
- **Fix**: Check that GBP was requested in Step 2A

### 📝 Document
```markdown
### Step 3A Results - [TIME]
- FA created: ✅
- FA ID: fa_test_xxx
- Currency: GBP
- Initial balance: £0.00 ✅
- Creation time: [X]s
- Issues: [none/describe]
```

---

## Cell 8: Step 3B - Create Financial Address

### 🎯 Purpose
Generate UK bank account details (sort code & account number) so the FA can receive bank transfers.

### 🔍 What This Cell Does

**API Call:**
```
POST /v2/money_management/financial_addresses
Headers:
  - Stripe-Account: {connected_account_id}
Body: {} (empty - Stripe generates details automatically)
```

**What's a Financial Address?**
- Provides routing details for receiving money
- For UK: Generates sort code + account number
- For US: Would generate routing number + account number
- For EU: Would generate IBAN
- Money sent to these details arrives in the FA

### ✅ Expected Output
```
Status: 200
✅ SUCCESS

🏦 Financial Address ID: faddr_test_xxx

Account Details:
  Sort Code: 108800
  Account Number: 00012345
```

### 📋 How to Use These Details

**To fund the FA:**
1. Copy the sort code and account number
2. Use your company bank account to send a transfer
3. Use reference "TOPUP" or "TEST"
4. Money arrives in FA within 1-2 hours (FPS)

**In production:**
- These details would be shown to end users
- Users send money from their bank to these details
- Creates an `inbound_transfer` in the FA

### ⚠️ Important Notes

- These are REAL bank details in live mode
- Money sent here goes into the FA
- Cannot be changed once created
- One FA can have multiple financial addresses

### 📝 Document
```markdown
### Step 3B Results - [TIME]
- Financial Address created: ✅
- FA Address ID: faddr_xxx
- Sort Code: [xxxxx]
- Account Number: [xxxxxxxx]
- Ready to receive transfers: ✅
```

---

## Cell 9: Step 3C - Fund the Financial Account (Manual)

### 🎯 Purpose
Add money to the Financial Account to enable card spend and payments.

### 🔍 What This Cell Does

**In TEST mode** (not applicable here):
- Would use `/v2/test_helpers/financial_addresses/{id}/credit`
- Instantly adds test money

**In LIVE mode** (your situation):
- ⚠️ **No test helpers available**
- Must use REAL bank transfer
- Takes 1-2 hours to arrive

### 📋 Your Options

**Option A: Send Real Bank Transfer (Recommended)**
1. Use company bank account
2. Send to sort code & account number from Step 3B
3. Amount: Suggest £1,000 for testing
4. Reference: "LIVE_TEST" or similar
5. Wait 1-2 hours for arrival

**Option B: Continue Without Funding**
- Skip funding for now
- Continue with setup steps
- Come back when transfer arrives
- Note: Can't issue cards or make payments without funds

**Option C: Use Alternative Funding (if available)**
- Some platforms have internal funding mechanisms
- Check if your platform has this set up

### ⏱️ Timeline

**Using FPS (Faster Payments):**
- Usually arrives: 15-30 minutes
- Can take up to: 2 hours
- Business hours: Faster
- Weekends/holidays: May be slower

### ✅ What to Do While Waiting

1. Continue to Step 4-5 (setup steps that don't need funds)
2. Take a break ☕
3. Document your progress so far
4. Review next steps in this guide

### 📝 Document
```markdown
### Step 3C - Funding Decision - [TIME]
- Funding method: [Real bank transfer / Skip for now / Alternative]
- Amount sent: £[X]
- Time sent: [XX:XX]
- Expected arrival: [XX:XX]
- Plan while waiting: [Continue to Step X / Break / etc]

[Come back to update when funds arrive]
- Funds arrived: [TIME]
- Amount received: £[X]
- Time taken: [X] minutes
```

---

## Cell 10: Check Financial Account Balance

### 🎯 Purpose
Verify the current balance in the Financial Account.

### 🔍 What This Cell Does

**API Call:**
```
GET /v2/money_management/financial_accounts/{fa_id}
Headers:
  - Stripe-Account: {connected_account_id}
```

**Process:**
1. Retrieves FA object
2. Extracts balance information
3. Displays current balance by currency
4. Shows available, pending, and any other balance states

### 📋 Understanding Balance States

**Available:**
- Money you can use right now
- Can be spent on cards
- Can be transferred out

**Pending:**
- Money in transit
- Example: Bank transfer initiated but not yet arrived
- Example: Card authorization pending settlement

**Outbound_pending:**
- Money reserved for outgoing transfers/payments
- Will be deducted once transfer completes

### ✅ Expected Output

**If unfunded:**
```
💰 Current Balance:
  GBP available: £0.00
```

**After funding with £1,000:**
```
💰 Current Balance:
  GBP available: £1,000.00
  GBP pending: £0.00
```

### 🔍 What to Look For

- Available balance matches what you sent
- No unexpected pending amounts
- Currency is correct (GBP)

### 📝 Document
```markdown
### Balance Check - [TIME]
- Available: £[X]
- Pending: £[X]
- Matches expected: ✅ / ❌
- Ready for card issuance: ✅ (if > £0) / ❌
```

---

## Cells 11-12: Step 4A & 4B - Issue FA-Backed Card

### 🎯 Purpose
Create a cardholder and issue a virtual card that spends from the Financial Account.

### 🔍 Step 4A: Create Cardholder

**API Call:**
```
POST /v1/issuing/cardholders
Headers:
  - Stripe-Version: 2025-09-30.preview; issuing_program_beta=v2
  - Stripe-Account: {connected_account_id}
Body:
  - Individual details (name, address, phone)
  - Billing address (for card usage)
  - Type: individual (or company)
```

**What's a Cardholder?**
- Represents the person/entity who will use the card
- Required before creating cards
- Stores KYC information
- Can have multiple cards per cardholder

**UK Requirements:**
- Full name (first + last)
- UK address
- UK phone number
- Must match actual end user in production

### ✅ Expected Output (4A)
```
Status: 200
✅ SUCCESS

👤 Cardholder ID: ich_xxx
```

### 🔍 Step 4B: Issue Card Backed by v2 FA

**API Call:**
```
POST /v1/issuing/cards
Headers:
  - Stripe-Version: 2025-09-30.preview; issuing_program_beta=v2
  - Stripe-Account: {connected_account_id}
Body:
  - financial_account_v2: {fa_id}  ← KEY DIFFERENCE!
  - currency: gbp
  - type: virtual
  - cardholder: {cardholder_id}
  - spending_controls.spending_limits
  - status: active
```

**🔑 Critical Parameter:**
- `financial_account_v2`: This is NEW for v2 FA
- OLD way: Cards spent from Issuing balance (v1)
- NEW way: Cards spend from specific v2 FA
- Balance deducted from FA, not from Issuing balance

### ✅ Expected Output (4B)
```
Status: 200
✅ SUCCESS

💳 Card ID: ic_xxx
Last 4: 4242
Brand: Visa
```

### 📋 Card Details

**Card Type: Virtual**
- No physical card
- Can be used online immediately
- Number, CVC, expiry available via API
- Can add to Apple/Google Pay

**Spending Limits:**
- £500 all-time limit set in example
- Can set per-transaction, daily, weekly, monthly, yearly, all-time
- Can restrict by merchant category (MCC)

### ❌ Possible Errors

**400 "Financial account has insufficient funds":**
- **Cause**: FA balance is £0 or less than requested
- **Fix**: Wait for funding from Step 3C

**400 "Cardholder not found":**
- **Cause**: Cardholder ID incorrect or from different account
- **Fix**: Check cardholder ID from Step 4A

### 📝 Document
```markdown
### Step 4A-4B Results - [TIME]
- Cardholder created: ✅
- Cardholder ID: ich_xxx
- Card issued: ✅
- Card ID: ic_xxx
- Card type: Virtual
- Last 4: [xxxx]
- Brand: [Visa/Mastercard]
- Spending limit: £500
- Status: Active
- Issues: [none/describe]
```

---

## Cells 13-15: Card Transaction Testing

### 🎯 Purpose
Test that card spend correctly debits from the v2 Financial Account.

### 🔍 Cell 13: Card Transaction (Manual in LIVE)

**In TEST mode:**
- Would use `/v1/test_helpers/issuing/authorizations`
- Simulates card spend

**In LIVE mode:**
- ⚠️ No test helpers
- Must make REAL card transaction
- Options:
  - Small online purchase (recommended: £5-£10)
  - Physical terminal if available
  - Apple/Google Pay if card added to wallet

### 📋 How to Make a Test Transaction

**Option A: Online Purchase**
1. Go to a test-friendly website (Stripe test shop, etc.)
2. Use card details (get from Dashboard or API)
3. Make small purchase
4. Watch for authorization webhook

**Option B: Physical Terminal**
1. Add card to Apple/Google Wallet
2. Tap at any contactless terminal
3. Use small amount

### 🔍 Cell 14: Check Card Authorizations

**API Call:**
```
GET /v1/issuing/authorizations
Headers:
  - Stripe-Account: {connected_account_id}
```

**What to Look For:**
- Authorization with your card ID
- Status: approved (if successful)
- Amount matches your transaction
- Merchant data shows where card was used

### ✅ Expected Output
```
📋 Found X authorization(s)

  ID: iauth_xxx
  Amount: £10.00
  Status: approved
  Merchant: Test Shop
```

### 🔍 Cell 15: Check Received Debits

**API Call:**
```
GET /v2/money_management/received_debits
Headers:
  - Stripe-Account: {connected_account_id}
```

**What This Shows:**
- Card spend as debits from FA
- Links authorization to FA transaction
- Shows impact on FA balance

### ⚠️ Important Note

Based on your dogfooding doc, received_debits might only show card **captures**, not authorizations. An authorization holds the money, but the debit appears when it's captured (settled).

### 📝 Document
```markdown
### Card Transaction Testing - [TIME]

**Transaction Made:**
- Amount: £[X]
- Merchant: [Name]
- Time: [XX:XX]
- Method: [Online/Terminal/Apple Pay]

**Authorization:**
- ID: iauth_xxx
- Status: [approved/declined]
- Amount: £[X]

**FA Impact:**
- Received debit visible: ✅ / ❌
- FA balance reduced: ✅ (by £[X])
- Expected behavior: ✅ / ❌

**Issues:**
- [None / Describe]

**Learning:**
- [Note anything unexpected]
```

---

## Cells 16-20: Step 5 - Outbound Payments to Recipient

### 🎯 Purpose
Create a recipient account and send them a payment from your Financial Account.

### 🔍 Step 5A: Create Recipient Account

**API Call:**
```
POST /v2/core/accounts
Headers:
  - Stripe-Account: {connected_account_id}
Body:
  - Recipient configuration
  - Individual identity (UK)
  - Bank account capability requested
```

**What's a Recipient?**
- Another v2 account type (not a full Connected Account)
- Can receive payouts
- Provides bank account for receiving money
- Simpler onboarding than full CA

**Configuration:**
- `recipient.capabilities.bank_accounts.local`: Can add UK bank account
- Country: GB
- Entity: Individual (or company)

### ✅ Expected Output (5A)
```
Status: 200
✅ SUCCESS

👤 Recipient Account ID: acct_xxx
```

### 🔍 Step 5B: Create Account Link for Recipient

**API Call:**
```
POST /v2/core/account_links
Headers:
  - Stripe-Account: {connected_account_id}
Body:
  - account: {recipient_account_id}
  - use_case.type: account_onboarding
  - configurations: ["recipient"]
```

**What This Does:**
- Creates onboarding URL for recipient
- Recipient completes bank details
- Uses v2 account link (not v1!)

### ✅ Expected Output (5B)
```
🔗 Recipient Onboarding URL: https://connect.stripe.com/setup/...

⚠️ Complete the onboarding at the URL above before proceeding.
```

**In Testing:**
- Open the URL yourself
- Add test bank details
- Complete the flow
- Return to notebook

### 🔍 Step 5C: Get Recipient Payout Method

**API Call:**
```
GET /v2/money_management/payout_methods
Headers:
  - Stripe-Context: {connected_account_id}/{recipient_account_id}
```

**⚠️ Note the Header:**
- Uses `Stripe-Context` (not `Stripe-Account`!)
- Format: `platform_account/recipient_account` (path notation)
- This is unique to recipient flows

**What's Returned:**
- Payout method object (usually bank account)
- Method ID needed for payment
- Bank details visible

### ✅ Expected Output (5C)
```
💳 Payout Method ID: gbba_test_xxx
Type: gb_bank_account
```

### 🔍 Step 5D: Make Outbound Payment

**API Call:**
```
POST /v2/money_management/outbound_payments
Headers:
  - Stripe-Account: {connected_account_id}
Body:
  - amount: £100 (10000 pence)
  - from.financial_account: {fa_id}
  - to.payout_method: {payout_method_id}
  - to.recipient: {recipient_account_id}
  - description
```

**What Happens:**
1. Money reserved from your FA (shows as outbound_pending)
2. Payment initiated to recipient's bank
3. Usually arrives same day (FPS)
4. Recipient receives real money

### ✅ Expected Output (5D)
```
✅ Outbound Payment Created!
Payment ID: obp_xxx
Status: pending
```

### 📋 Payment Lifecycle

**Status: pending**
- Payment created
- Money reserved from FA
- Bank transfer initiated

**Status: processing**
- In transit through banking network

**Status: succeeded**
- Arrived in recipient's bank account
- Money deducted from FA

**Status: failed**
- Could not complete
- Money returned to FA

### ❌ Possible Errors

**400 "Recipient requirements not met":**
- **Cause**: Recipient onboarding incomplete
- **Fix**: Check recipient account, complete onboarding (Step 5B)

**400 "Insufficient funds":**
- **Cause**: FA doesn't have enough balance
- **Fix**: Check FA balance, ensure funds arrived

### 📝 Document
```markdown
### Step 5 Results - [TIME]

**5A - Recipient Creation:**
- Recipient ID: acct_xxx
- Created: ✅

**5B - Onboarding:**
- Link created: ✅
- Onboarding completed: ✅
- Bank details added: ✅
- Issues: [none/describe]

**5C - Payout Method:**
- Method ID: gbba_xxx
- Type: gb_bank_account
- Retrieved: ✅

**5D - Outbound Payment:**
- Payment ID: obp_xxx
- Amount: £100.00
- Status: [pending/processing/succeeded]
- FA balance after: £[X]
- Issues: [none/describe]

**Overall:**
- End-to-end flow worked: ✅ / ❌
- Time taken: [X] minutes
- Key learnings: [note anything]
```

---

## Cells 21-26: Step 6 - Outbound Transfers (Me-to-Me)

### 🎯 Purpose
Test transferring money between your own Financial Accounts and to external bank accounts.

### 🔍 Step 6A: Create Second Financial Account

**Why?**
- To test FA-to-FA transfers
- Simulates moving money between "accounts" (like checking → savings)

**API Call:** Same as Step 3A, just different display name

### ✅ Expected Output
```
💰 Second FA ID: fa_test_xxx
```

### 🔍 Step 6B: Transfer Between Own FAs (Instant)

**API Call:**
```
POST /v2/money_management/outbound_transfers
Body:
  - from.financial_account: {fa_1}
  - to.payout_method: {fa_2}  ← Yes, FA can be payout method!
```

**What's Different from Outbound Payment?**
- `outbound_transfers`: Me-to-me (own FAs, own banks)
- `outbound_payments`: Me-to-others (recipients, other CAs)

**Speed:**
- FA-to-FA: **Instant**
- No banking network involved
- Balance updates immediately

### ✅ Expected Output (6B)
```
✅ Transfer Created!
Transfer ID: obt_xxx
Status: succeeded
```

### 🔍 Step 6C: Create External Bank Account (UK)

**API Call:**
```
POST /v2/core/vault/gb_bank_accounts
Body:
  - account_number: "00012345"
  - sort_code: "108800"
  - confirmation_of_payee.initiate: true
```

**What's Confirmation of Payee (CoP)?**
- UK-specific verification
- Confirms account name matches bank records
- Reduces fraud and misdirected payments
- Required for first-time UK payouts

**CoP Statuses:**
- `matched`: Name matches perfectly ✅
- `partially_matched`: Close match ⚠️
- `not_matched`: Name doesn't match ❌
- `not_available`: Bank doesn't support CoP

### ✅ Expected Output (6C)
```
🏦 Bank Account ID: gbba_xxx
```

### 🔍 Step 6D: Acknowledge Confirmation of Payee

**API Call:**
```
POST /v2/core/vault/gb_bank_accounts/{id}/acknowledge_confirmation_of_payee
```

**Why This Step?**
- Required before using bank account for payouts
- You're acknowledging you've seen the CoP result
- One-time requirement per bank account

**In EU:** Use Outbound Setup Intent instead (different flow)

### ✅ Expected Output (6D)
```
✅ CoP Acknowledged!
```

### 🔍 Step 6E: Transfer to External Bank Account

**API Call:**
```
POST /v2/money_management/outbound_transfers
Body:
  - from.financial_account: {fa_id}
  - to.payout_method: {bank_account_id}
```

**What Happens:**
1. Money reserved from FA
2. Bank transfer initiated (FPS)
3. Usually arrives within 2 hours
4. Real money sent to real bank account

### ⚠️ Important

This sends REAL MONEY to the bank account specified in Step 6C. Make sure you use a bank account you control!

### ✅ Expected Output (6E)
```
✅ Transfer to External Bank Created!
Transfer ID: obt_xxx
Status: pending
```

### 📝 Document
```markdown
### Step 6 Results - [TIME]

**6A - Second FA:**
- Created: ✅
- FA ID: fa_test_xxx

**6B - FA-to-FA Transfer:**
- Transfer ID: obt_xxx
- Amount: £50.00
- Speed: Instant ✅
- Both balances updated: ✅

**6C - External Bank Account:**
- Bank ID: gbba_xxx
- Sort code: xxxxxx
- Account: xxxxxxxx
- CoP status: [matched/etc]

**6D - CoP Acknowledgment:**
- Acknowledged: ✅

**6E - Transfer to Bank:**
- Transfer ID: obt_xxx
- Amount: £20.00
- Status: [pending/processing/succeeded]
- Expected arrival: [time]

**Overall:**
- All transfer types tested: ✅
- Issues: [none/describe]
- UK-specific CoP flow: ✅ Documented
```

---

## Cells 27-32: Step 7 - Cross-Border Payment (GBP → USD)

### 🎯 Purpose
Test sending money from GBP Financial Account to USD recipient in the US, with FX conversion.

### 🔍 Step 7A: Create US Recipient Account

**API Call:** Similar to Step 5A but:
- Country: "US" (not "GB")
- Different bank account requirements

### ✅ Expected Output
```
👤 US Recipient Account ID: acct_xxx
```

### 🔍 Step 7B: Onboard US Recipient

**API Call:** Same as Step 5B

**US Bank Details Required:**
- Routing number (9 digits)
- Account number
- Account holder name

### 🔍 Step 7C: Get US Recipient Payout Method

**API Call:** Same as Step 5C, different Stripe-Context

### ✅ Expected Output
```
💳 US Payout Method ID: usba_test_xxx
```

### 🔍 Step 7D: Setup Outbound Payment Intent

**API Call:**
```
POST /v2/money_management/outbound_setup_intents
Body:
  - payout_method: {us_payout_method_id}
  - usage_intent: "payment"
```

**Why This Step?**
- Verifies payout method can be used
- Required before first cross-border payment
- One-time setup per payout method

### ✅ Expected Output
```
✅ Outbound Setup Intent Created!
```

### 🔍 Step 7E: Get FX Quote

**API Call:**
```
POST /v2/money_management/outbound_payment_quotes
Body:
  - amount.value: 10000 (£100)
  - amount.currency: "gbp"
  - from.financial_account: {gbp_fa_id}
  - to.currency: "usd"
  - to.payout_method: {us_payout_method_id}
  - to.recipient: {us_recipient_id}
```

**What's an FX Quote?**
- Shows exchange rate GBP → USD
- Shows exactly how much USD will be received
- Shows any fees
- Quote valid for ~30 seconds to 1 minute
- Must use quote ID in payment

### ✅ Expected Output
```
💱 FX Quote Obtained!
Quote ID: obpq_xxx
Exchange Rate: 1.27 (example)

You send: £100.00
They receive: $127.00 (minus fees)
```

### 📋 Understanding the Quote

```
{
  "id": "obpq_xxx",
  "amount": {
    "value": 10000,  // £100
    "currency": "gbp"
  },
  "destination_amount": {
    "value": 12700,  // $127
    "currency": "usd"
  },
  "exchange_rate": 1.27,
  "fees": {
    "fx_fee": 100  // £1 FX fee
  }
}
```

### 🔍 Step 7F: Execute Cross-Border Payment

**Note:** The cell shows example structure but doesn't execute automatically.

**Why Not Auto-Execute?**
- Quotes expire quickly
- Real money involved
- You should review the quote first

**To Execute:**
1. Review quote from Step 7E
2. Check exchange rate is acceptable
3. Copy quote ID
4. Create outbound payment with quote parameter

**API Call:**
```
POST /v2/money_management/outbound_payments
Body:
  - amount, from, to (same as quote)
  - quote: {quote_id_from_7E}
```

### ⚠️ Important

- Quote ID must be from recent quote (< 1 minute old)
- If quote expires, get new one before payment
- Real money will be sent to US recipient
- FX rate locked when quote created

### 📝 Document
```markdown
### Step 7 Results - [TIME]

**7A - US Recipient:**
- Created: ✅
- Recipient ID: acct_xxx

**7B - US Onboarding:**
- Completed: ✅
- Bank details: Routing + Account added

**7C - US Payout Method:**
- Method ID: usba_xxx

**7D - Setup Intent:**
- Created: ✅

**7E - FX Quote:**
- Quote ID: obpq_xxx
- You send: £100.00
- They receive: $[X]
- Exchange rate: [X]
- Fees: £[X]
- Rate acceptable: ✅ / ❌

**7F - Payment Execution:**
- Executed: ✅ / Skipped
- Payment ID: obp_xxx (if executed)
- Status: [if executed]

**Overall:**
- Cross-border flow complete: ✅ / Partial
- FX quote worked: ✅
- Learning: [note FX rates, fees, timing]
```

---

## Cells 33-35: Step 8 - Review & Monitor

### 🎯 Purpose
Final review of balances, transactions, and collect all test data.

### 🔍 Cell 33: Final Balance Check

**API Call:** Same as balance check in Step 3

**What to Review:**
- How much money is left?
- Do all debits/credits match your actions?
- Any unexpected balances?

### ✅ Expected Output
```
💰 Final Balance Summary:
  GBP available: £[X]
  GBP pending: £[X]
  GBP outbound_pending: £[X]
```

### 📋 Balance Audit

Starting balance: £1,000 (example)
- Card spend: -£10
- Payment to recipient: -£100
- Transfer to 2nd FA: -£50
- Transfer to bank: -£20
- Cross-border payment: -£100
**Expected final:** £720

Does it match? ✅ / ❌

### 🔍 Cell 34: Transaction History

**API Call:**
```
GET /v2/money_management/transactions
Query params:
  - financial_account: {fa_id}
```

**What This Shows:**
- All transactions on the FA
- Received credits (top-ups)
- Received debits (card spend)
- Outbound payments
- Outbound transfers
- FX conversions

### ✅ Expected Output
```
📋 Recent Transactions (10):

  trxn_xxx
    Amount: -£100.00
    Type: outbound_payment
    
  trxn_xxx
    Amount: -£10.00
    Type: received_debit
```

### 📋 What to Check

- [ ] Top-up received credit visible
- [ ] Card spend as received debit
- [ ] Each payment/transfer has transaction
- [ ] Amounts match what you sent
- [ ] No unexpected transactions

### 🔍 Cell 35: Test Data Summary

**What This Does:**
- Displays all IDs created during testing
- Shows which steps were completed
- Provides final checklist

### ✅ Expected Output
```
📝 IDs Created During Testing:

connected_account_id         : acct_xxx
financial_account_id         : fa_test_xxx
financial_address_id         : faddr_test_xxx
card_program_id              : iprg_xxx
cardholder_id                : ich_xxx
card_id                      : ic_xxx
recipient_account_id         : acct_xxx
payout_method_id             : gbba_test_xxx
bank_account_id              : gbba_test_xxx

✅ Save these IDs for reference and further testing.
```

### 📋 Final Checklist

- [ ] Platform verification complete
- [ ] Connected Account created
- [ ] Issuing capability added
- [ ] Financial Account created & funded
- [ ] Card issued & tested
- [ ] Recipient payment successful
- [ ] FA-to-FA transfer successful
- [ ] External bank transfer initiated
- [ ] Cross-border payment tested (or attempted)
- [ ] All balances reconciled
- [ ] All transactions reviewed
- [ ] Documentation completed

### 📝 Final Session Document
```markdown
---
# 🎯 LIVE DOGFOODING SESSION COMPLETE

**Session Summary:**
- Start time: [X]
- End time: [X]
- Duration: [X] hours
- All steps completed: ✅ / [X/8]

**Key Metrics:**
- Total API calls: ~[X]
- Errors encountered: [X]
- Issues resolved: [X]
- Average response time: [X]s

**Money Movement:**
- Total funded: £[X]
- Total spent: £[X]
- Final balance: £[X]
- Transactions: [X] total

**Issues Encountered:**
1. [Issue 1 - describe and resolution]
2. [Issue 2 - describe and resolution]

**Key Learnings:**
1. [Learning 1]
2. [Learning 2]
3. [Learning 3]

**Documentation Gaps Identified:**
1. [Gap 1]
2. [Gap 2]

**API Friction Points:**
1. [Friction 1]
2. [Friction 2]

**Positive Observations:**
1. [Positive 1]
2. [Positive 2]

**Recommendations:**
- For Product: [X]
- For Docs: [X]
- For Sales: [X]

**Next Steps:**
- [ ] Export notebook as HTML
- [ ] Share findings with team
- [ ] Create feedback tickets
- [ ] Update Bob scenarios
- [ ] Schedule follow-up session

---

**Participants:**
- [Names]

**Artifacts:**
- This notebook: stripe_v2_fa_issuing_live_testing.ipynb
- Screenshots: [folder]
- API logs: [file]

---
```

---

## 🎉 Congratulations!

You've completed the live dogfooding session for Stripe v2 Financial Accounts and Issuing!

### What You've Tested

✅ v2 Connected Account creation with all configurations  
✅ New Issuing capability attachment via card programs  
✅ v2 Financial Account and Financial Address creation  
✅ Real money funding via bank transfer  
✅ FA-backed card issuance and card spend  
✅ Outbound payments to recipients  
✅ Outbound transfers between FAs and to banks  
✅ Cross-border payments with FX  
✅ UK-specific flows (CoP, FPS)  

### Share Your Findings

Your documentation will help:
- Improve API documentation
- Identify product improvements
- Create better SSA/SA resources
- Onboard new users more smoothly

Thank you for your thorough testing! 🚀
