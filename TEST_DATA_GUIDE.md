# Parity Large Regression Test Dataset

These files are synthetic and contain no real identity or access data.

## Files

- `source_acl.csv`
- `iga_staging_report.csv`

## Dataset size

- Source rows: 24,320
- IGA rows: 22,850

## Issues intentionally represented

This dataset is designed to exercise a wide range of reconciliation scenarios that may appear in regulated IAM / IGA certification workflows.

1. Large clean exact-match population
2. Source-only relationships
3. IGA-only relationships
4. Leading and trailing whitespace differences
5. Case differences
6. Repeated internal spaces
7. Contractor / employee identity prefix conversions such as `C######` vs `E######`
8. Entitlement naming differences involving prefixes, dashes, and underscores
9. Alternate accounts for the same identity + entitlement
10. Source-side duplicate relationships
11. IGA-side duplicate relationships
12. A large 650-row missing targeted-group cluster using `AD-GRP-FIN-PRIV`
13. Role-vs-entitlement structural mismatches where one IGA role corresponds to several source entitlements
14. Blank identity or entitlement rows that should be ignored when Ignore Blank is enabled
15. Different source and IGA column names / schemas

## Mapping to use in Parity

### Source file: `source_acl.csv`

- Identity → `Employee ID`
- Account → `Account`
- Entitlement → `Access Code`
- Application → `Application`
- Display name → `User Name`

### IGA file: `iga_staging_report.csv`

- Identity → `Identity ID`
- Account → `Account Name`
- Entitlement → `Entitlement`
- Application → `Source App`
- Display name → `Display Name`

## Default normalization settings

Use Parity's standard default normalization settings:

- Trim whitespace: ON
- Case insensitive comparison: ON
- Collapse repeated spaces: ON
- Ignore blank rows: ON
- Ignore duplicate relationships for matching: ON

Do not add custom normalization rules for the baseline test.

## Expected baseline results

Comparison key:

`Identity + Entitlement`

Expected results:

- Source relationships: **23,790**
- IGA relationships: **22,640**
- Exact matches: **21,240**
- Missing from IGA: **2,550**
- Missing from Source: **1,400**
- Duplicates: **640**
- Source Completeness: **89.3%**
- IGA Population Alignment: **93.8%**

## Expected results with Account included

Enable:

`Include Account in comparison key`

Comparison key:

`Identity + Account + Entitlement`

Expected results:

- Source relationships: **24,090**
- IGA relationships: **22,640**
- Exact matches: **21,240**
- Missing from IGA: **2,850**
- Missing from Source: **1,400**
- Duplicates: **340**
- Source Completeness: **88.2%**
- IGA Population Alignment: **93.8%**

## Important expected behaviors

### Identity conversion mismatches

Values such as:

`C400001`

and:

`E400001`

must remain separate relationships unless an explicit custom normalization rule is created.

Parity should not automatically assume that different identity prefixes refer to the same identity.

### Large missing-group cluster

The dataset contains 650 source-only relationships using:

`AD-GRP-FIN-PRIV`

These should appear as Missing from IGA relationships.

This scenario represents a targeted certification where a new or existing group is present in the authoritative source population but is missing from the certification definition.

### Alternate accounts

The dataset contains identities with the same entitlement assigned through more than one account.

With the default comparison key:

`Identity + Entitlement`

these relationships collapse onto the same logical relationship and contribute to duplicate detection.

When Account is included:

`Identity + Account + Entitlement`

the alternate accounts become distinct relationships.

### Basic formatting differences

Whitespace, casing, and repeated internal spaces should reconcile under the default normalization options.

These differences may be listed as formatting / normalized matches where applicable, but they should not create true population mismatches.

### Custom-normalization examples

Some entitlement values intentionally differ through prefixes, dashes, and underscores.

These should remain mismatches during the baseline run.

They are included so testers can experiment with Parity's Advanced Normalization Rules and confirm that explicit transformations can resolve them.

Parity should never silently apply those transformations.

### Role-vs-entitlement structural mismatches

Some source records contain multiple individual entitlements while the IGA file contains one bundled role.

These are intentionally unresolved by the current Parity comparison engine.

Parity should flag the differences rather than assume that a role is equivalent to a specific collection of entitlements.

This represents a real-world scenario where an analyst may need additional application evidence, such as a current role-to-entitlement mapping or screenshot.

### Duplicate behavior

Duplicate relationships must remain visible for review even when duplicates are collapsed for matching.

Duplicates alone should not determine whether the population is In parity or Out of parity.

Parity status should be based on true Missing from IGA and Missing from Source relationships.

### Blank rows

Rows with a blank Identity or Entitlement should be skipped when Ignore Blank is enabled.

## Suggested tester workflow

1. Open Parity.
2. Start a new reconciliation.
3. Upload `source_acl.csv` as the Source file.
4. Upload `iga_staging_report.csv` as the IGA file.
5. Apply the mappings listed above.
6. Leave Account out of the comparison key.
7. Leave the default normalization options enabled.
8. Run the parity check.
9. Confirm the baseline metrics match the expected results in this guide.
10. Review each exception filter:
    - Missing from IGA
    - Missing from Source
    - Duplicate
    - Formatting / normalized match
    - Custom-rule normalized match
11. Search for `AD-GRP-FIN-PRIV` and confirm the large missing-group scenario is visible.
12. Search for `C400` and `E400` identities and confirm prefix conversions remain distinct.
13. Run the same files again with Account included in the comparison key.
14. Confirm the Account-enabled metrics match the expected results above.
15. Export the exception CSV.
16. Review the Evidence Summary and print / save it as PDF.
17. Optionally test custom normalization rules against the prefix / dash / underscore examples.

## What this dataset is for

This dataset is intended for:

- regression testing
- performance testing
- practitioner demos
- validating comparison-key behavior
- validating normalization behavior
- validating duplicate handling
- validating exception filtering
- validating evidence output
- testing future Parity releases for unintended changes

It is not intended to represent one specific real-world application or organization.

## Privacy

All identities, accounts, entitlements, applications, and access relationships in these files are synthetic.

No production, customer, employee, or confidential access data is included.
