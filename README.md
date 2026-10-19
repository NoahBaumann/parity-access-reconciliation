Parity is a browser-based access certification reconciliation tool for comparing authoritative source-system access against an IGA certification population.

It is designed to help IAM and GRC teams validate completeness and accuracy before activating access reviews.

Try it:
1. Open index.html locally in Chrome, or use the hosted GitHub Pages version.
2. Use the files in /sample-data/.
3. Upload source_acl.csv as the Source file.
4. Upload iga_staging_report.csv as the IGA file.
5. Map:
   - Source Identity → Employee ID
   - Source Entitlement → Access Code
   - Source Account → Account
   - IGA Identity → Identity ID
   - IGA Entitlement → Entitlement
   - IGA Account → Account Name
6. Run the parity check.
7. Compare your results with TEST_DATA_GUIDE.md.

Privacy:

Raw CSV files are processed locally in your browser and are not uploaded by Parity.

Reconciliation history and exception records are stored locally in your browser.
