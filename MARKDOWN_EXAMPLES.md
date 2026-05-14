# Example: Adding Markdown Comments to Your Testing Notebook

## Visual Guide: Before & After

---

## BEFORE (Just Code Cells)

```
┌─────────────────────────────────────────────┐
│ Cell 1: [Code]                              │
│ print_section("Step 2A: Create v2 CA")      │
│ response = requests.post(...)               │
└─────────────────────────────────────────────┘
┌─────────────────────────────────────────────┐
│ Cell 2: [Code]                              │
│ print_section("Step 2B: Add Issuing")       │
│ response = requests.post(...)               │
└─────────────────────────────────────────────┘
```

## AFTER (With Markdown Comments)

```
┌─────────────────────────────────────────────┐
│ Cell 1: [Markdown] ⭐ YOUR NOTES HERE        │
│ ### 🔍 Step 2A - Creating Connected Account │
│ **Time Started:** 9:15 AM                   │
│ **Team members present:** All 4             │
└─────────────────────────────────────────────┘
┌─────────────────────────────────────────────┐
│ Cell 2: [Code]                              │
│ print_section("Step 2A: Create v2 CA")      │
│ response = requests.post(...)               │
│ OUTPUT: ✅ SUCCESS - Account ID: acct_xxx   │
└─────────────────────────────────────────────┘
┌─────────────────────────────────────────────┐
│ Cell 3: [Markdown] ⭐ YOUR OBSERVATIONS      │
│ ### Results from Step 2A                    │
│ - Account created in 2.3 seconds            │
│ - All capabilities requested successfully   │
│ - Status: requires_onboarding (expected)    │
│ - Issue: None                               │
└─────────────────────────────────────────────┘
┌─────────────────────────────────────────────┐
│ Cell 4: [Code]                              │
│ print_section("Step 2B: Add Issuing")       │
│ response = requests.post(...)               │
└─────────────────────────────────────────────┘
```

---

## Step-by-Step: Adding Your First Markdown Comment

### Scenario: You just ran Step 2A and want to document what happened

**1. Click ABOVE the next code cell** (you'll see a blue line)

**2. Press `B`** (creates new cell Below the current one)

**3. Press `M`** (converts new cell to Markdown)

**4. Type this:**

```markdown
### 📊 Step 2A Results - 9:15 AM

**API Response:**
- ✅ Status: 200 OK
- Account ID: acct_1SRtXB7IkPsLCet4
- Time: 2.3 seconds

**Configuration Status:**
- Storer: ✅ Requested
- Merchant: ✅ Requested  
- Card Creator: ✅ Requested
- Recipient: ✅ Requested

**Account Status:**
- Current: requires_onboarding
- Next: Create account link

**Issues/Questions:**
- None yet

**Team Notes:**
- Joel: Response looks good
- Bastian: Capabilities match expected
```

**5. Press `Shift + Enter`** to render it

**6. Continue to next code cell!**

---

## Real Example Templates for Each Step

### Template 1: Before Running a Step

```markdown
---
## 🚀 About to run: Step 3A - Create Financial Account

**Purpose:** Create v2 FA to hold GBP for card spend

**Prerequisites Check:**
- [x] Connected Account created: acct_xxx
- [x] Storer capability active
- [ ] Account fully onboarded ← TO DO

**What we expect:**
- New FA with £0.00 balance
- FA ID will be stored for later use
- Should complete in < 3 seconds

**Who's running this:** Kate  
**Time:** 9:30 AM
---
```

### Template 2: After Running a Step (Success)

```markdown
---
## ✅ Step 3A Completed Successfully

**Results:**
- Financial Account ID: `fa_test_65TbHw7DfmXAWmdLid116TbHUX8xE9SZq6wjBP6SI7U9Ps`
- Display Name: "Main Account"
- Balance: £0.00 (as expected)
- Created in: 1.8 seconds

**Next Steps:**
1. Create Financial Address (Step 3B)
2. Fund the account (manual step in live)

**Notes:**
- API response was clean
- No errors or warnings
- Ready to proceed
---
```

### Template 3: After Running a Step (Error)

```markdown
---
## ❌ Step 5D Failed - Outbound Payment Error

**Error Details:**
- Error Type: `invalid_request_error`
- Message: "Recipient account requirements not met"
- Time: 10:05 AM

**Investigation:**
```python
# Checked recipient requirements
curl GET /v2/core/accounts/{recipient_id}
# Found: requirements.currently_due = ["bank_account"]
```

**Root Cause:**
- Recipient onboarding incomplete
- Missing bank account details

**Resolution:**
1. Completed account link flow again
2. Added bank account info
3. Waited 30 seconds for verification
4. Retried payment → SUCCESS ✅

**Time to Resolve:** 5 minutes

**Lesson Learned:**
Always verify `requirements.currently_due` is empty before attempting payment

**Team Input:**
- Daniel: Should we add a requirements check before each payment?
- Decision: Yes, add to best practices doc
---
```

### Template 4: During Investigation

```markdown
---
## 🔍 Investigating: Card Authorization Not Showing in FA

**Symptoms:**
- Card auth created: auth_xxx
- Status: approved
- But no debit in FA transactions

**Checks Performed:**

1. ✅ Card definitely attached to v2 FA
```python
card['financial_account_v2'] == test_data['financial_account_id']  # True
```

2. ✅ FA balance shows expected amount
```python
# Balance: £999,900.00 (was £1,000,000 - £100 auth)
```

3. ❓ Received debits endpoint
```python
# GET /v2/money_management/received_debits
# Returns: Empty array
```

**Hypothesis:**
- Debits may only show card *capture*, not authorization
- Authorizations might not create received_debit objects

**Testing:**
- Capturing the authorization now...
- [Results to be added]

**Time Spent:** 10 minutes  
**Status:** In Progress
---
```

### Template 5: Decision Point Documentation

```markdown
---
## 🤔 Decision Required: How to Fund FA in Live Mode

**Context:**
- Step 3C requires funding the Financial Account
- Test helpers don't work in live mode
- Need real money movement

**Options:**

### Option A: Real Bank Transfer
**Process:**
1. Use FA account details (Sort: 108800, Account: 00012345)
2. Initiate transfer from company bank account
3. Wait 1-2 hours for settlement

**Pros:**
- Tests real user experience
- Validates actual bank integration

**Cons:**
- Time delay (1-2 hours)
- Requires access to company bank
- Real money at risk

### Option B: Skip Funding, Use Later Steps
**Process:**
1. Skip to Step 4 (cards) without funding
2. Expect errors
3. Document error handling

**Pros:**
- No delay
- Tests error cases

**Cons:**
- Can't complete full flow
- Limited testing value

### Option C: Request Test Mode Extension
**Process:**
1. Ask excelsior team for test mode with live-like behavior
2. Continue in sandbox

**Pros:**
- Fast iteration
- No real money

**Cons:**
- Not true live testing
- May miss live-specific issues

**Decision:** Going with Option A - Real Bank Transfer  
**Decided by:** Team consensus  
**Time:** 9:45 AM  
**Bank transfer initiated:** 9:50 AM  
**Expected completion:** 11:50 AM  

**Plan while waiting:**
- Continue with recipient setup (Step 5A-5C)
- Prepare Step 6 and 7 
- Coffee break ☕
---
```

---

## Practical Workflow for Tomorrow

### Morning Setup (9:00 AM)

**1. Start your notebook**
```bash
jupyter notebook
```

**2. Add session header** (new markdown cell at top):

```markdown
# 🧪 Live Dogfooding Session - February 11, 2026

**Session Details:**
- Start Time: 9:00 AM GMT
- Environment: LIVE - UK Platform
- Platform Account: acct_xxx
- Participants: Kate, Joel, Bastian, Daniel

**Objectives:**
1. Create v2 Connected Account with Issuing
2. Issue FA-backed card
3. Test all money movement flows
4. Document friction points and issues

**Success Criteria:**
- [ ] Complete all 8 test scenarios
- [ ] Document any API errors
- [ ] Identify UX friction points
- [ ] Generate recommendations for docs

---

## 📋 Session Log

*[Add your step-by-step notes below as you progress]*

---
```

### During Testing (9:00 AM - 12:00 PM)

**Before each major step:**
```markdown
### ⏰ [Step Name] - [Current Time]
**About to test:** [What you're testing]
**Expected:** [What should happen]
```

**After each step:**
```markdown
**Results:** [What happened]
**Time taken:** [Duration]
**Issues:** [Any problems]
**Notes:** [Observations]
---
```

### End of Session (12:00 PM)

**Add final summary** (new markdown cell at bottom):

```markdown
---

# 📊 Session Summary

**Completion Time:** 12:15 PM (3 hours 15 minutes)

## ✅ Completed Steps
1. ✅ Platform verification
2. ✅ Create Connected Account
3. ✅ Add Issuing capability
4. ⚠️ Create & Fund FA (delayed - bank transfer pending)
5. ✅ Issue card
6. ⏸️ Card transaction (postponed until funding complete)
7. ✅ Recipient creation
8. ✅ Outbound transfer setup

## ❌ Issues Encountered

| # | Issue | Step | Severity | Status | Time Lost |
|---|-------|------|----------|--------|-----------|
| 1 | Recipient requirements unclear | 5D | Medium | Resolved | 10 min |
| 2 | CoP acknowledgment not documented | 6D | Low | Resolved | 5 min |
| 3 | Live funding delay | 3C | High | Pending | 2 hours |

## 💡 Key Findings

### Documentation Gaps
1. Need clearer guidance on live vs test mode differences
2. CoP flow needs better docs for UK
3. Recipient requirements should be explained upfront

### API Friction Points
1. Too many steps to attach Issuing capability
2. Stripe-Context header format not obvious
3. v1/v2 API mixing is confusing

### Positive Observations
1. v2 FA creation is fast and reliable
2. Error messages are clear and actionable
3. Account link flow works well

## 🎯 Recommendations

### For Product Team
1. Simplify Issuing capability attachment (combine into account creation?)
2. Provide test helper alternative for live mode testing
3. Add v2 webhook event documentation

### For Documentation
1. Add live mode specific guide
2. Create CoP flow diagram for UK
3. Explain Stripe-Context vs Stripe-Account clearly

### For Sales/SSA
1. Warn about live funding delays in demos
2. Prepare sandbox with pre-funded accounts
3. Create "known limitations" cheat sheet

## 📁 Artifacts Generated
- [x] This notebook with full session log
- [x] Screenshot folder (15 screenshots)
- [x] API request/response samples
- [ ] Final report (TO DO)

## 👥 Team Feedback

**Kate:** "v2 APIs are cleaner than v1, but the mixing is confusing"  
**Joel:** "Recipient flow needs better docs - we got stuck twice"  
**Bastian:** "Like the FA abstraction, makes more sense than v1"  
**Daniel:** "Need to document the Issuing program attachment flow better"

## 📅 Follow-up Actions

| Action | Owner | Due Date | Priority |
|--------|-------|----------|----------|
| Create doc feedback ticket | Kate | Feb 12 | High |
| Share findings with product | Joel | Feb 13 | High |
| Update Bob scenarios | Kate | Feb 14 | Medium |
| Create SSA cheat sheet | Daniel | Feb 15 | Medium |

---

**Session completed at:** 12:15 PM  
**Next session:** TBD - After addressing key issues
```

---

## Quick Tips for Effective Note-Taking

### 1. Use Emoji for Quick Scanning
- ⏰ Time markers
- ✅ Success
- ❌ Failure
- ⚠️ Warning
- 🔍 Investigation
- 💡 Insight
- 📝 Note
- 🤔 Question
- 👥 Team decision

### 2. Use Tables for Comparisons

```markdown
| Test | Expected | Actual | Match? |
|------|----------|--------|--------|
| FA creation | < 2s | 1.8s | ✅ |
| Card issue | < 3s | 4.2s | ⚠️ |
| OBP to recipient | < 5s | 2.1s | ✅ |
```

### 3. Use Code Blocks for API Details

```markdown
**Request:**
```json
{
  "amount": {"value": 10000, "currency": "gbp"},
  "from": {"financial_account": "fa_xxx"}
}
```

**Response:**
```json
{
  "id": "obp_xxx",
  "status": "pending",
  "created": 1707649200
}
```
```

### 4. Use Checklists for Prerequisites

```markdown
**Before running Step 4:**
- [x] Cardholder created
- [x] FA has balance
- [ ] Card spending limits set ← TODO
```

### 5. Timestamp Everything

```markdown
09:15 - Started Step 2A
09:17 - Account created successfully
09:20 - Started Step 2B
09:22 - Error: missing card program
09:25 - Fixed: used correct program ID
09:26 - Completed Step 2B ✅
```

---

## Your Testing Checklist

Print this and check off as you go:

```
□ Open Jupyter notebook
□ Add session header with date/time/team
□ Update SECRET_KEY
□ Run setup cell
□ For each step:
  □ Add "About to run" markdown
  □ Run code cell
  □ Add "Results" markdown
  □ Save notebook (Ctrl+S)
  □ Take screenshot if interesting
□ Add summary at end
□ Export to HTML
□ Share with team
```

---

Good luck tomorrow! You've got this! 🚀
