# How to Run Your Jupyter Notebook

## Option 1: Using Jupyter Notebook (Recommended for Beginners)

### Installation

```bash
# Install Jupyter
pip install jupyter notebook

# Or if you have conda
conda install jupyter
```

### Running the Notebook

1. **Navigate to the folder** where you saved the notebook:
   ```bash
   cd /path/to/your/notebook
   ```

2. **Start Jupyter**:
   ```bash
   jupyter notebook
   ```

3. **Your browser will open automatically** showing a file browser

4. **Click on** `stripe_v2_fa_issuing_live_testing.ipynb`

5. **The notebook will open** in a new tab

### Using the Notebook

#### Running Cells

- **Single cell**: Click on a cell and press `Shift + Enter` (or click the ▶️ Run button)
- **All cells**: Menu → Cell → Run All
- **Run cell and stay**: Press `Ctrl + Enter` (doesn't move to next cell)

#### Cell Navigation

- **Click** on a cell to select it
- **Up/Down arrows** to move between cells
- **Enter** to edit a cell
- **Esc** to exit edit mode

#### Cell Types

- **Code cells**: Have `In [ ]:` on the left, contain Python code
- **Markdown cells**: Plain text, used for documentation

---

## Option 2: Using VS Code (Great Alternative)

### Setup

1. **Install VS Code**: https://code.visualstudio.com/
2. **Install Python extension**: 
   - Open VS Code
   - Click Extensions icon (left sidebar)
   - Search "Python"
   - Install the official Microsoft Python extension
3. **Install Jupyter extension**:
   - Search "Jupyter"
   - Install the official Microsoft Jupyter extension

### Running the Notebook

1. **Open the .ipynb file** in VS Code (File → Open File)
2. **Select Python kernel** (top right corner)
3. **Run cells** by clicking the ▶️ icon next to each cell
4. **Or use keyboard shortcuts**:
   - `Shift + Enter`: Run cell and move to next
   - `Ctrl + Enter`: Run cell and stay

---

## Option 3: Using JupyterLab (Modern Interface)

```bash
# Install JupyterLab
pip install jupyterlab

# Run it
jupyter lab
```

Then open your notebook file from the left sidebar.

---

## How to Add Markdown Comments

### Adding a New Markdown Cell

#### In Jupyter Notebook:
1. **Click between two cells** (you'll see a thin blue line)
2. **Press `A`** (adds cell Above) or **`B`** (adds cell Below)
3. **Press `M`** to convert the new cell to Markdown
4. **Type your comment**
5. **Press `Shift + Enter`** to render it

#### In VS Code:
1. **Hover between cells** until you see `+ Code` and `+ Markdown` buttons
2. **Click `+ Markdown`**
3. **Type your comment**
4. **Click ✓** or press `Shift + Enter` to render

### Markdown Quick Reference

```markdown
# Large Header (H1)
## Medium Header (H2)
### Small Header (H3)

**Bold text**
*Italic text*

- Bullet point
- Another point

1. Numbered list
2. Second item

`inline code`

```python
# Code block
print("Hello")
```

> Blockquote or note

[Link text](https://example.com)

---  (horizontal line)

⚠️ **Warning** (using emoji)
✅ **Success**
❌ **Error**
```

### Example: Adding Comments to Your Notebook

Let's say you want to add notes before Step 3A. Here's how:

**Before:**
```
[Step 3A: Create Financial Account]  <-- Code cell
```

**After adding markdown:**
```
[Your new markdown cell with notes]  <-- Markdown cell
[Step 3A: Create Financial Account]  <-- Code cell
```

**Example markdown to add:**

```markdown
### 📝 Notes Before Creating Financial Account

**Important considerations:**
- Ensure the Connected Account is fully onboarded
- Check that storer capability is active
- This will be the main account for card spend

**Expected outcome:**
- Financial Account ID will be stored in `test_data['financial_account_id']`
- Initial balance will be £0.00
- Next step will be to create a Financial Address for funding
```

---

## Practical Workflow for Tomorrow's Session

### Setup (Do this first)

1. **Open your notebook**:
   ```bash
   jupyter notebook
   # Then click on stripe_v2_fa_issuing_live_testing.ipynb
   ```

2. **Update your secret key** (first code cell):
   - Click on the cell with `SECRET_KEY = ...`
   - Replace with your actual live key
   - Press `Shift + Enter` to run it

### During Testing

1. **Add notes as you go**:
   - Before running a step, click above the cell
   - Press `B` (new cell below) then `M` (make it markdown)
   - Add your observations, questions, or reminders
   - Press `Shift + Enter` to render

2. **Run each step**:
   - Click on the code cell
   - Press `Shift + Enter`
   - Review the output
   - Add markdown notes about what happened

3. **Save frequently**:
   - Press `Ctrl + S` (or `Cmd + S` on Mac)
   - Or File → Save and Checkpoint

### Example: Adding Live Notes

```markdown
### 🔍 Step 2A Results - [Your timestamp]

**API Response:**
- Connected Account created successfully ✅
- Account ID: acct_1ABC... 
- Status: pending (expected, needs onboarding)

**Observations:**
- Response time: ~2 seconds
- All capabilities requested correctly
- Need to complete account link in next step

**Questions/Issues:**
- [None yet]

**Next:** Create account link for hosted onboarding
```

---

## Keyboard Shortcuts Cheatsheet

### Jupyter Notebook

| Action | Command Mode (Esc) | Edit Mode (Enter) |
|--------|-------------------|-------------------|
| Run cell | `Shift + Enter` | `Shift + Enter` |
| Run cell, stay | `Ctrl + Enter` | `Ctrl + Enter` |
| Add cell above | `A` | - |
| Add cell below | `B` | - |
| Delete cell | `DD` (press D twice) | - |
| Convert to Markdown | `M` | - |
| Convert to Code | `Y` | - |
| Save | `Ctrl + S` | `Ctrl + S` |
| Undo cell delete | `Z` | - |
| Cut cell | `X` | - |
| Copy cell | `C` | - |
| Paste cell | `V` | - |

**Toggle between modes:**
- **Enter Edit mode**: Press `Enter` or click in cell
- **Enter Command mode**: Press `Esc`

---

## Tips for Your Testing Session

### 1. **Create a Testing Template**

Add this markdown at the top of each major section:

```markdown
---
### ⏰ [Section Name] - Started at [time]

**Pre-checks:**
- [ ] Previous steps completed successfully
- [ ] Required IDs available: [list them]
- [ ] Expected outcome: [what you expect]

**Execution notes:**
[Space for your observations]

**Results:**
[Space for outcomes]

**Issues/Questions:**
[Space for problems]

---
```

### 2. **Use Markdown for Decision Points**

```markdown
### ⚠️ DECISION POINT

**Option A:** Use test bank account details
- Pros: [list]
- Cons: [list]

**Option B:** Use real bank transfer
- Pros: [list]
- Cons: [list]

**Decision:** [What you chose and why]
```

### 3. **Track API Call Performance**

```markdown
### 📊 API Performance Log

| Step | Endpoint | Response Time | Status | Notes |
|------|----------|---------------|--------|-------|
| 2A | POST /v2/core/accounts | 2.1s | 200 | Success |
| 2B | POST /v1/issuing/programs | 1.3s | 200 | Success |
```

### 4. **Document Errors Immediately**

```markdown
### ❌ ERROR ENCOUNTERED

**Step:** 5D - Outbound Payment
**Time:** 10:23 AM
**Error Code:** invalid_request_error
**Error Message:** "Recipient not fully onboarded"

**Investigation:**
- Checked recipient account status
- Found requirements.currently_due not empty
- Missing: bank account verification

**Resolution:**
1. Completed account link flow
2. Added bank details
3. Retried payment - SUCCESS ✅

**Learning:** Always verify recipient.requirements before payment
```

---

## Quick Start Commands

```bash
# Install everything you need
pip install jupyter notebook pandas requests

# Navigate to your notebook folder
cd ~/stripe-testing

# Start Jupyter
jupyter notebook

# Your browser opens automatically at http://localhost:8888
# Click on your notebook file to open it
```

---

## Troubleshooting

### "Kernel not found" error
```bash
# Install ipykernel
pip install ipykernel

# Add Python to Jupyter
python -m ipykernel install --user
```

### Cell won't run / stuck
- Click "Kernel → Restart" in the menu
- Then run cells again from the top

### Can't install packages
```bash
# Inside a notebook cell, you can run:
!pip install requests

# This installs packages directly
```

### Want to export your notebook?
- **File → Download as → HTML** (to share with team)
- **File → Download as → PDF** (for documentation)
- **File → Download as → Python (.py)** (for scripts)

---

## Best Practice for Tomorrow

1. **Before starting:**
   - Run `jupyter notebook`
   - Open the notebook
   - Add a markdown cell at the top with date/time/attendees

2. **During testing:**
   - Run one cell at a time
   - Add markdown notes after each major step
   - Save frequently (Ctrl + S)
   - Take screenshots of important results

3. **After testing:**
   - Add a summary markdown cell at the bottom
   - Export as HTML for sharing
   - Save the final .ipynb file with date in filename

---

## Example Session Structure

```markdown
# Live Dogfooding Session - Feb 11, 2026

**Attendees:** Kate, Joel, Bastian, Daniel  
**Start Time:** 9:00 AM  
**Environment:** LIVE - UK Platform  
**Objective:** Test v2 FA + Issuing end-to-end

---

## Session Notes

### Pre-Session Setup ✅
- [x] Platform account verified
- [x] Excelsior tasks completed
- [x] Live API keys ready
- [x] Team on call

---

## Step 1: Platform Verification
[Add your code cell]

### Results
[Add your observations]

---

## Step 2: Create Connected Account
[Add your code cell]

### Results
[Add your observations]

[Continue for each step...]

---

## Session Summary

**Completed Steps:** [X/8]  
**Time Taken:** [X hours]  
**Issues Found:** [Count]  
**Action Items:** [List]

### Key Findings
1. [Finding 1]
2. [Finding 2]

### Recommendations
1. [Recommendation 1]
2. [Recommendation 2]
```

---

## Ready to Start?

1. Open terminal
2. Run `jupyter notebook`
3. Click on your notebook file
4. Start with the first cell
5. Add markdown notes as you go!

Good luck with your dogfooding session tomorrow! 🚀
