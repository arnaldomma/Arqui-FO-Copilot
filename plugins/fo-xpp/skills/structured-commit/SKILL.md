---
name: structured-commit
description: >
  Guide for writing structured commit messages for D365 F&O / X++ development.
  Use when the developer asks to write, format, or review a commit message.
---

## Instructions for Copilot

When generating or reviewing a commit message for D365 F&O development, strictly follow the structure below.

## Commit Message Structure

### Line 1 — Subject (plain text, no Markdown)

- Must be plain text — it is **not** rendered as Markdown; formatting like `**bold**` or backticks will appear as raw characters
- Use **imperative mood**: `Add`, `Fix`, `Refactor`, `Remove`, `Update`, `Implement`
- Be concise but descriptive — include the **what** and the **scope** (object/form affected)
- Do not end with a period
- Maximum ~72 characters

```
Add filtered lookup for ARQ_PaymentJournalName on CustParameters form
```

### Line 2 — Blank line (mandatory)

Always leave a blank line with a single whitespace between the subject and the body. This separates the title from the body in Git tooling.

### Body — Sections with `###` headers

Each `###` header must be preceded by a single whitespace character (` ### ...`) so it is **not** interpreted as a Git comment.

Use the following four sections:

---

 ### What was done
- Use bullet points (`-`)
- **Bold** keywords, class names, method names, field names, and enum values
- Wrap object paths and file paths in backticks: `Metadata/ARQ_Core/.../ClassName.xml`
- Describe the technical changes made (relations added, classes created, event handlers registered, etc.)

 ### Why it was done
- Explain the **business reason** or the problem being solved
- Reference field names and objects with backticks or bold as appropriate
- Keep it concise — one or two bullets

 ### Where it was done
- List every file/object created or modified
- Always bold the label (e.g., **Table extension modified**, **Class added**) and use backticks for the path
- Format: `- **<Object type> <action>**: \`<path>\``

 ### WI
- Reference the Azure Boards Work Item with a space before `#` to avoid Git comment interpretation
- Format: ` - #<id>`

---

## Full Example

```
Add filtered lookup for ARQ_PaymentJournalName on CustParameters form

 ### What was done
- Added relation `ARQ_LedgerJournalName` to `CustParameters.ARQ_Core` table extension, linking `ARQ_PaymentJournalName` to `LedgerJournalName.JournalName`
- Created event handler class `CustParameters_ARQCore_FrmCtrl_ARQ_PaymentJournalName_Handler` with a `Lookup` event subscriber on `formControlStr(CustParameters, CustParameters_ARQ_PaymentJournalName)` that calls `LedgerJournalName::lookupJournalNameByJournalType` filtered to `LedgerJournalType::CustPayment` and cancels the default super lookup

 ### Why it was done
- The `ARQ_PaymentJournalName` field on `CustParameters` previously showed all journal names with no type filter; it must only allow **Customer payment** journals to prevent misconfiguration

 ### Where it was done
- **Table extension modified**: `Metadata/ARQ_Core/ARQ_Core/AxTableExtension/CustParameters.ARQ_Core.xml`
- **Class added**: `Metadata/ARQ_Core/ARQ_Core/AxClass/CustParameters_ARQCore_FrmCtrl_ARQ_PaymentJournalName_Handler.xml`

 ### WI
 - #7244
```

---

## Forbidden Words

The following vague words are **not allowed** in commit messages. Replace them with precise, descriptive alternatives:

| ❌ Forbidden | ✅ Use instead | Notes |
|---|---|---|
| `fix` | `Correct`, `Resolve`, `Repair` | Reserve "fix" only when paired with a specific bug noun: `Fix null reference in...` |
| `issue` | describe the actual problem | e.g., `missing filter`, `incorrect calculation`, `null reference` |
