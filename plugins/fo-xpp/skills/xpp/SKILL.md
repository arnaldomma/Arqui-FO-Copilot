---
name: xpp
description: >
  X++ patterns guide for D365 F&O. Use when requests are related to
  X++ development, classes, queries, data contracts, etc.
---

## Development Assumptions

1. Each developer has a virtual machine configured in their local machine
2. Each developer has access to the network project to register timesheets
3. Each developer is part of the `Development Team` user group

## Development Guidelines

- When a new development is initiated, guarantee that no differences exist between the Dev branch and the Dev Task branch by doing a **Rebase**. Perform this regularly to minimize merge conflicts.

- Generic development guidelines not related to F&O or X++ will not be described in work items. It is the developer's responsibility to follow them (e.g., index or primary key creation on tables).

- **Developers must be responsible for their developments.** Therefore, it is absolutely necessary that developments are tested by the developer before submitting a pull request.

- Each and every new object must have the prefix `ARQ_`.

- Every addition to a standard object (e.g., fields in a table, tabs in a form) must also be prefixed with `ARQ_`.

- Each and every customized/extended object must have the suffix `<model name>` without underscores. If it is a code extension, add the correspondent identification of the object type.

- Every created field or developed code must be controlled by the global activation parameter and module-specific parameter.
  Use the field name `ARQ_EnabledFeature` for feature flag parameters.
  Use the global method `ARQ_FeatureSetupControl::getEnabledFeature()` to retrieve the value.

- Labels must be created in the `ARQCore` label file of the `ARQ_Core` model. Label description should contain only the model name.

- New models must be created in the `ARQ_Extensions` package and must reference the `ARQ_Core` model.

## Instructions for Copilot

When developing in X++ for D365 F&O, always follow these core rules:

1. Always use `ttsbegin`/`ttscommit` in write operations
2. Prefer `select firstonly` when you only need 1 record
3. Do not use LINQ — use native X++ query syntax
4. Always follow the `SysOperation` pattern for batch operations
5. Use `RecordInsertList` for bulk inserts instead of inserting record by record
6. Declare variables at the beginning of the method (classic X++ style)
7. Use `changeCompany` with caution and always within a `try/finally` block
8. Prefer `Map` and `Set` to arrays when the collection can grow dynamically
9. Document methods with XML comments (`/// <summary>`)
10. Follow the naming conventions defined below (prefix `ARQ_` for new objects, appropriate suffixes for extensions)

## Naming Conventions

### New Objects (Prefix Pattern)

All new objects must use the prefix `ARQ_`:

```
ARQ_SalesHelper
ARQ_InvoiceService
ARQ_ParametersTable
```

### Extended/Customized Objects (Suffix Pattern)

Pattern: `<Standard Object Name>.<Model Name>` or `<Standard Object Name>_<Model Name>_<Type>_Extension`

| Object Type | Suffix Code | Example |
|---|---|---|
| Table Extension | (no suffix or `.ModelName`) | `CustTable.ARQCore` or `CustTable_ARQCore_Extension` |
| Table Code Extension | `Tbl` | `CustTable_ARQCore_Tbl_Extension` |
| Form Extension | `.ModelName` | `CustTable.ARQCore` |
| Form Code Extension | `Frm` | `CustTable_ARQCore_Frm_Extension` |
| Form DataSource Extension | `<datasource>DS` | `CustTable_ARQCore_Frm_CustTableDS_Extension` |
| Form Control Extension | `FrmCtrl_<ControlName>` | `CustTable_ARQCore_FrmCtrl_ProjId_Extension` |
| Class Extension | `Cls` | `SalesHelper_ARQCore_Cls_Extension` |

### Feature Flags

```xpp
ARQ_EnabledFeature   ← field name for activation parameters
```

Usage:
```xpp
if (ARQ_FeatureSetupControl::getEnabledFeature(SalesParameters::find()))
{
    // Feature logic here
}
```

### Labels

Create in `ARQCore` label file (ARQ_Core model):

```
ARQCoreLabel=Label description
;ARQ_Core
```

## Common Patterns

### Reading Records

```xpp
SalesTable salesTable;
select firstonly salesTable
    where salesTable.SalesId == salesId;
```

### Write with Transaction

```xpp
ttsbegin;
salesTable.SalesName = "New Name";
salesTable.update();
ttscommit;
```

### Data Contract for SysOperation

```xpp
[DataContractAttribute]
class MyDataContract
{
    TransDate   fromDate;

    [DataMemberAttribute]
    public TransDate parmFromDate(TransDate _fromDate = fromDate)
    {
        fromDate = _fromDate;
        return fromDate;
    }
}
```

### Feature Control Pattern

```xpp
public void processData()
{
    if (!ARQ_FeatureSetupControl::getEnabledFeature(SalesParameters::find()))
    {
        return;
    }
    
    // Feature implementation
}
```
