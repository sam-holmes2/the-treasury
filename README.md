# The Treasury

See where your money comes from, where it goes, and how your savings grow, with clear, interactive visualisations.

[Explore Steve’s demo](https://the-treasury.samuelsmanuals.com/) · [Download the blank app](https://github.com/sam-holmes2/the-treasury/raw/refs/heads/main/Treasury.html)

For people who want to understand GBP finances from bank exports or manual records, without connecting their bank. Download the HTML file and open it in your browser.

The hosted demo uses **Steve**, an invented UK household. Every payment, account, salary and habit is made up.

## Philosophy

“What gets measured gets managed” motivates The Treasury: make money visible, understand the records behind each figure, and choose what to change. Measurement supports your judgment; it does not decide for you.

## Make it yours

1. Choose **Download blank app (.html)**, then open **Treasury.html** in your browser. Nothing to install: no Python, server or account is needed to use it.
2. Choose **Start manually** to add guidance, actions, plans, dated balances or income with the section controls. Or choose **Import** to read a bank CSV or supported OFX, prepared data JSON or a saved copy of The Treasury.
3. For CSV, match date, description and amount columns, or separate money-in and money-out columns. Choose the source account, check payment dates and types, then review overlaps before applying the merge. Replace all bank payments is an explicit alternative.
4. Use **Save copy** to keep your records, goals, unfinished drafts and view choices in an HTML app or JSON backup. Review before restoring a backup; Undo can recover the view it replaced. Keep your original exports too.

**Explore the demo** dismisses the welcome. **Help** is a separate short guide with section links. Hosted pages offer a download instead of personal import. In your downloaded file, **Load Steve’s made-up data** shows a replacement preview first; it does not silently clear your records or Undo.

The Treasury does not connect to your bank or change your original file. CSV defaults to merging payments after source-account and overlap review. Known matches within that account are counted as already recorded; similar payments require an explicit decision. An optional bank transaction ID must identify an individual payment, not a reusable customer/payment reference. Repeated IDs within a new file require review. Separate payments with identical descriptions and amounts can be retained. A changed bank reference requires review before keeping or replacing a record. The explicit Replace all bank payments action remains available. Manual records, goals, drafts and settings are kept, and Undo restores the previous app state. Prepared JSON replaces records; a whole backup restores records and the saved view.

Bank payments remain in the open page until you save a copy. Browser recovery may retain goals, appearance, view choices and editor drafts, but it is not a complete bank-data backup.

## Different banks and formats

CSV headers can differ: use the column selectors. English month names with four-digit years and complete ISO timestamps are accepted; timestamps keep their written calendar date without time-zone conversion. Comma, semicolon and tab separators, signed amounts or separate positive money-in/out columns, day-first/month-first dates and dot/comma decimals are supported. Amounts use integer pennies after conversion. Currency is GBP only. Convert spreadsheets or PDFs to CSV first; check the result against your statement. UTF-8 CSV supports up to 8 MiB/25,000 rows per file, JSON 32 MiB and saved HTML 64 MiB. Retained CSV evidence supports at most 64 sources and 8 MiB of serialized source history. Exact repeated imports reuse unchanged evidence. Invalid UTF-8 is refused rather than silently losing original bytes.

Original CSV text, including unused columns, source-account labels and reconciliation decisions are retained in saved HTML/JSON. Keep populated copies private. Merged first/last transaction dates describe observed records, not proof of complete coverage; gaps do not become complete savings-rate periods. Merging keeps opening cash and does not infer an account balance. Use dated balance records for account values.

Positive incoming cash is not always pay. Review refunds, reimbursements, savings withdrawals and investment movements. The preview includes every payment across pages. A saved HTML import reads its data without executing its code.

Prepared JSON for The Treasury is optional and described in [SCHEMA.md](SCHEMA.md). Help downloads generic conversion instructions and an invented example. A local tool or an LLM can convert another export, but you must check dates, signs and totals. Sharing a statement with an online AI service sends it to that service.

### Bank layout evidence

The importer suggests columns, then asks you to check them. It recognises Monzo-style names, Starling-style Counter Party / Amount (GBP), and Post Date / Transaction Detail. Bank Type or Payment Type is not assumed to be a Treasury classification. GBP currency columns and explicit amount-currency headings are checked even when their optional mapping is unused; local-currency purchase detail is retained without replacing the GBP account amount.

Compatibility is bounded. [Monzo currently offers CSV](https://monzo.com/help/account-and-profile/bank-statement-how-to); its [published March 2020 header list](https://community.monzo.com/t/csv-exports/89487/69) is historical. Starling CSV availability is bank-documented, but the eight-column historical layout tested here comes from a filed organisational statement, not a claim about current consumer exports. [Lloyds' documented CSV table](https://resources.lloydsbank.com/uploadedFiles/CBOnline_upgrade/Pages/Activity_checklist/Exporting_data/col-exports-csv-balance-transaction.pdf) covers Commercial Banking Online only. Other debit/credit layouts are synthetic mapping cases, not certified bank compatibility.

Committed regression records are invented. Additional local tests used author-anonymised historical Starling CSV (2023) and Lloyds statement (2024) attachments, checking all 127 rows against an independent reference and through merge, exact reimport, JSON/HTML recovery and Python validation. Those files are not distributed here. Their historical provenance does not certify current bank exports. Separately, recent contributor-declared anonymised Lloyds, NatWest and Revolut fixtures were tested; their direct lineage from native bank files remains unproven. Tests cover signed/split amounts, refunds, references, overseas purchases charged in GBP, repeated payments and overlap review. A GBP payment file does not establish present balances, complete history, savings contributions or investment gains. Review and manually classify transfers and refunds; add dated account balances and missing facts through section controls. Statement preambles/footers and non-GBP accounts still need preparation or another supported export. Generic bank status columns are not silently interpreted.

### Revolut statement profile

The detected ten-column CSV profile requires one source product, signed dot-decimal GBP, Completed Date, Description, Amount and Currency. Completed payments from the selected product are imported; fees are deducted in exact pennies. The preview counts excluded pending/non-completed and other-product rows and shows the deducted fees. Original rows remain in saved evidence. Unknown states, malformed fees and foreign currencies refuse the file. Use a separate Treasury account for each product. Previously saved sources without this profile keep their original interpretation.

## OFX bank and card statements

Choose a `.ofx` file in Import, name its source account, then preview the payments. Use the same account for later exports. The existing overlap review, payment-type edits, merge, explicit replacement and Undo also apply to OFX. Check the incoming payment types: a deposit or card repayment is not automatically salary.

This reader accepts a limited, checked subset of OFX 1 SGML and OFX 2 XML: one GBP checking, savings or credit-card account per file. It keeps exact pennies and the written posting day without converting time zones. Statement balances stay in the retained evidence; add a dated balance separately. Zero-amount rows are reported and excluded from the charts.

Use UTF-8 files. An ASCII-only legacy file declaring USASCII/NONE or USASCII/1252 also works. Non-UTF-8 files, multiple accounts, foreign-currency detail, pending payments, correction/delete records, investments, loans and unrecognised extensions are rejected before any changes. Use a supported CSV export for those cases; do not rename another format to OFX. This is a statement-file reader, not an online bank connection or a complete OFX implementation.

Saved copies retain the original file text, including account numbers and unused fields, alongside the review decisions. Keep those copies private. The demo still refuses personal imports; use your downloaded blank HTML.

The implementation follows the [FDX OFX specifications](https://financialdataexchange.org/about-fdx/ofx-work-group/), including the [banking specification](https://financialdataexchange.org/wp-content/uploads/2025/12/OFX-Banking-Specification-v2.3.pdf) and its [bank/card schemas](https://financialdataexchange.org/wp-content/uploads/2025/12/OFX.v2.3.zip). The supplied OFX examples contain invented data only.

## Rates and account balances

Header settings keep the measure and income basis separate:

- **Left after spending:** earned income less net household spending.
- **Savings:** net explicitly labelled cash-savings contributions and withdrawals.
- **Savings and investments:** the cash-savings amount plus net investment contributions.

The basis is either total earned income, or earned income less actual net spending marked outside your control. Mixed costs and planned bills are not deducted. Rates use recorded cash movements, not balance growth or investment returns.

For cash savings, classify a transfer in the payment editor or import preview. Label the funding-side payment once as a contribution or withdrawal; mark its counterpart as internal/already counted. Other transfers can be marked not savings. An optional CSV Savings flow column accepts deposit, withdrawal, internal, other or unknown. Existing corrections survive a uniquely matched reimport.

Unclassified potential transfers or missing payment coverage make savings rates incomplete. A zero, negative or unknown income basis is N/A. Supported negative and above100% rates are shown without clamping. The range figure divides total amounts rather than averaging monthly percentages. A best-fit line uses complete supported periods and actual dates; it is not a forecast.

Recorded balances remain separate from contributions. Use **Add a recorded balance** for a dated statement value and identify savings accounts explicitly. Missing values stay unknown. Existing money/goal header selections and penny-based goals retain their original units. New views use All time, autoplay, the percentage not spent with the after-outside-control basis, and open main sections; saved choices remain authoritative. Raw figures and record lists start collapsed. Use Show more and Show less to reveal or close them; all records remain available. Long category and merchant lists keep their existing Show more controls.

Steve’s salary steps, bills and habits are authored examples, not national averages or tax calculations. Freelance receipts are separate from the stable salary records. Strategic and Tactical guidance is general; editing a suggestion preserves your own note. See [MoneyHelper](https://www.moneyhelper.org.uk/en/everyday-money/budgeting/beginners-guide-to-managing-your-money) and [FCA](https://www.fca.org.uk/investsmart/should-you-invest) for the primary guidance.

## Goals and manual records

Use **Add a goal** in the expanded header or the related section. Choose a minimum target or maximum limit, a recorded amount or savings percentage, and an optional target date. Common uses include an emergency fund, savings rate, monthly spending cap, income target and lower loan balance. A dotted line marks the target; green means met and red means not met. Unknown records stay gaps. Percentage goals name their income basis; target dates do not create forecasts.

**Spending** offers Add payment beside its heading. **Records** offers Confirm coverage and shopping detail. Click a payment to correct its date, name, amount, classification, income source or reimbursement link. Original imported text remains retained; corrections and manual payments survive supported reimports. Use Undo to recover an applied change. Close keeps an unfinished editor draft; Cancel explicitly discards it.

**Mortgage** offers the current loan facts and dated loan observations. Enter dates, balances and terms from your own statements. Events are annotations and never change loan facts. Dated account balances, pay records, plans and guidance also have manual editors. Shopping details are separate from bank totals, so adding them does not count the spending twice.

Bank exports often omit classifications, savings purpose, account valuations and loan terms. Confirm coverage only when you know the chosen date range contains all required payments. Observed first/last dates alone do not prove complete coverage. CSV layouts may need explicit column mapping; spreadsheet/PDF exports need conversion and review. Supported OFX is the documented GBP bank/card subset above, not every bank or account type.

## Keep your files private

The downloaded app reads only selected files and makes no network requests. The example rejects personal imports. External help/support links open only when clicked; there are no remote assets or analytics.

Populated HTML/JSON copies and remembered drafts are unencrypted. Keep them in a protected private folder and browser profile on a locked, encrypted device, away from shared folders, public repositories and attachments. For extra privacy, open the file offline and avoid extensions that can read page content. Only open trusted Treasury HTML or your own copies: a modified HTML can contain different code. For support, reproduce the problem with the made-up example instead of sharing your finances.

## This release repository

This repository distributes the approved standalone blank app and its documentation. It does not include the development source tree, build scripts, personal financial records or earlier Git history. The HTML contains the generic code it needs to run offline and the optional invented Steve example.

The blank download is the same Treasury 1.5.0 file verified by the public three-browser QA workflow. [View its 12 passing browser checks](https://github.com/sholmes222/treasury-release-qa/actions/runs/40237607938). Those checks use invented records. Historical author-anonymised statement testing was performed locally; those files are not distributed here.

See [ARTWORK.md](ARTWORK.md), [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md) and [LICENSE](LICENSE) for artwork and software terms. [SHA256SUMS](SHA256SUMS) identifies the exact files in this release.

## Release notes

### 1.5.0 · 25 October 2026

Bank CSV imports accept strict English named-month dates and complete ISO timestamps while preserving the written calendar day. The supported Revolut profile requires one source product, imports completed payments only, deducts fees in exact pennies, and shows excluded-row counts. Unknown states, malformed fees and non-GBP accounts are refused before changes. Existing retained imports keep their original interpretation. Profile choices survive HTML and JSON recovery.

### 1.0.1 · 25 October 2026

Shorter aligned header controls: Track: and As a % of:. Header and expanded history axes use whole-number ticks while points and tooltips retain their original precision. Main totals now share each section heading row with consistent typography and responsive wrapping. Dates, coverage and source notes remain available, and folded sections keep their headline figures visible.

### 1.0.0 · 24 October 2026

First stable release, following the public beta. Includes the interactive fictional demo, offline blank HTML, bank-file import and overlap review, manual records and guidance, dated account histories, flexible goals, and HTML/JSON save and recovery. Section colours consistently identify each money view.

The independent public `sholmes222/treasury-release-qa` runner verified the downloaded HTML in Chromium, Firefox and WebKit on Ubuntu. Each test opened the exact public blank file offline, imported invented payments, added a manual payment, guidance and a goal, reimported without duplicates, saved and reopened HTML in a fresh context, then exported and restored JSON into a fresh blank file. Exact amounts and records survived, with no page errors or HTTP/S requests from the local-file contexts. The earlier beta test gap is closed for this scope.

Windows, macOS Safari and mobile operating systems remain untested. This is not a guarantee that every bank export is supported: GBP-only formats, mapping, coverage and source limits above still apply. No bank connection is made, and no personal data was used in the public QA runner.

### 1.1.0 · 25 October 2026

Practical compact help replaces the opening release-note wall. An optional keyboard-accessible walkthrough highlights real controls without automatically changing records or saved view settings. Updates, import/privacy details and section shortcuts remain available on demand. A lightweight chest tab icon is declared in the initial HTML; the app then uses the painted chest with its correct image type.

### 1.1.1 · 25 October 2026

The welcome prioritises the green walkthrough, with a blue demo and gold blank download. Walkthrough steps dim unrelated content while keeping the active section usable; the final action opens the context-appropriate own-sheet setup instructions.

### 1.1.2 · 25 October 2026

A centred full chest welcomes visitors, with consistent option descriptions. Setup-first Help leads into download/import/manual entry, saving and restoration, with practical guidance on private files, backups and safe sharing. Technical versions and test reports remain in this source documentation and QA repository, rather than the app interface. The public site serves a conventional PNG favicon and favicon fallback using the existing full-gold chest artwork; local copies retain embedded offline icons.


### 1.2.0

Appearance now opens as a compact non-modal panel so skins and light/dark choices can be judged against the app immediately. Common cash-flow and mortgage display controls and Data view options are visible in compact groups. Help shares Guidance purple. Blank Treasury app downloads share gold styling and accessible hover, focus and tap explanations; local Import and populated HTML/JSON saves retain their distinct meanings. Saved records, drafts, choices and financial calculations are unchanged.


### 1.4.1

Notable-payment date lines reach the bottom of the cash-flow plot. Explicit Play remains available under reduced motion from the header, toolbar and expanded history controls, with consistent opt-in explanations; automatic animation still respects reduced-motion and editing/background safeguards.

### 1.4.0

Hosted Steve visits open Guidance, autoplay supported history and select three notable outgoings. Personal saved choices, reduced-motion, background and editor safeguards remain. Selecting a notable payment directly shows its date marker. Cash-flow stacks use distinct green incoming and red outgoing shades with labelled keys; the optional Gold & white palette remains available. Repeated chart captions move to accessible help.

### 1.3.0

The welcome puts the blank app before the Steve demo with clearer descriptions. The dock has one Help control. Appearance offers a saved Gold & white only option, including chart series and status presentation, while retaining labels, signs and exact values. This optional palette survives HTML/JSON save and restore; existing copies retain their saved appearance.

### Expanded bank-format verification

Six constructed, three-payment CSV profiles are checked through parsing, suggested mapping, exact pennies, merge, repeat import, changed-file overlap review, retained original text and HTML/JSON restore: historical Monzo2020, historical Starling eight-column, documented Lloyds Commercial Banking Online, and three RBS Bankline statement/search/supplementary layouts. Every record and identifier is invented. These tests establish those documented or historical shapes, not certification of current customer exports or consumer-bank compatibility.

The [RBS Bankline January2025 guide](https://www.rbs.co.uk/content/dam/rbs_co_uk/Business_and_Content/PDFs/Bankline/Export-file-layout-guide-Bankline-CSV-format_RBoS.pdf) supports the new field mappings. Explicit account currency remains checked even when its mapping is cleared. For a new file, known account-number/sort-code columns must be complete and consistent; prepare one account per file. Previously saved raw source evidence keeps its original parsing contract during recovery, so the new preview checks do not invalidate old copies. Bank payment schemes and references are not guessed as income or stable transaction IDs. GBP-only and observed-coverage limits remain.

[Co-operative business exports](https://www.co-operativebank.co.uk/business/faqs/ways-to-bank/online-banking/statements-and-transactions/can-I-export-transaction-information/prod-11451) can contain leading statement rows and a footer. That structural case is explicitly rejected here: prepare a clean header-and-payment CSV and check it before import. No silent preamble/footer stripping is applied. Bank availability documentation is separate from tested exact layouts. Public tests contain no real customer statements.
