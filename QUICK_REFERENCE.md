# QUICK REFERENCE - Jupyter Notebook Cheat Sheet

## Start Jupyter
```bash
jupyter notebook
```
Browser opens at http://localhost:8888

---

## Essential Shortcuts

### Run Code
- **Run & Next:** `Shift + Enter`
- **Run & Stay:** `Ctrl + Enter`

### Add Markdown (for notes)
1. Press `B` (new cell below)
2. Press `M` (change to markdown)
3. Type your notes
4. `Shift + Enter` (to render)

### Cell Management
- **Delete cell:** `DD` (press D twice)
- **Undo delete:** `Z`
- **Copy cell:** `C`
- **Paste cell:** `V`

### Mode Switching
- **Edit mode:** Press `Enter` (green border)
- **Command mode:** Press `Esc` (blue border)

---

## Markdown Cheat Sheet

```markdown
# Heading 1
## Heading 2
### Heading 3

**Bold**
*Italic*

- Bullet list
1. Numbered list

`inline code`

⚠️ Warning
✅ Success
❌ Error
📝 Note
🔍 Investigation
```

---

## Your Testing Template

Add this before each major step:

```markdown
---
### [Step Name] - [Time]

**Pre-checks:**
- [ ] Checklist item

**Expected outcome:**
[What should happen]

**Actual results:**
[What actually happened]

**Issues:**
[Any problems]
---
```

---

## Common Tasks

### Save Notebook
`Ctrl + S` (or `Cmd + S` on Mac)

### Restart Kernel (if stuck)
Menu → Kernel → Restart

### Export Results
File → Download as → HTML

---

## Testing Workflow

1. ✅ Run code cell
2. 📝 Add markdown note below
3. 💾 Save (Ctrl + S)
4. ➡️ Move to next step

---

## Emergency Commands

```bash
# If Jupyter crashes
jupyter notebook stop
jupyter notebook

# If packages missing
!pip install requests
```

---

## Your Notebook Variables

All IDs stored in `test_data`:
- `connected_account_id`
- `financial_account_id`
- `card_id`
- etc.

Print anytime:
```python
print(test_data)
```

---

Print this page and keep it next to you! 🚀
