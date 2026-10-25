# Local input schema 1

Prepared JSON for The Treasury is an optional input format, not a requirement for importing bank CSVs. CSV users match their columns and check an editable preview in Import. A local script or LLM may prepare this format, but check its output against the source. Online AI conversion shares statements with the provider.

UTF-8 JSON. Every top-level field below is required except holding_history, income_history, mortgage_history, orders and import_sources; unknown fields are rejected. Currency is GBP only. Labels are nonempty strings of at most 120 characters without control characters. Dates are real calendar dates in YYYY-MM-DD. Amounts are integer pennies, never floating currency values. Amount magnitude is limited to one trillion pennies. The browser JSON file limit is 32 MiB; at most 25,000 transactions are accepted.

| Field | Type / meaning |
| :--- | :--- |
| schema_version | Exactly 1 |
| name | Local display label |
| currency | Exactly GBP |
| as_of | Coverage date; transactions cannot be later |
| fictional | Boolean; false for your own data |
| opening_cash_minor | Signed opening cash balance for the imported ledger, not a snapshot or income |
| transactions | Array of the rows below |
| baseline | null, or monthly income and commitment object below |
| holdings | Array of dated value snapshots; may be empty |
| holding_history | Optional array of dated holding values; omit or use an empty array when unavailable |
| income_history | Optional monthly gross and take-home records, described below |
| mortgage | null, or mortgage object below |
| mortgage_history | Optional exact dated loan observations, described below |
| actions | Array of locally supplied action dates; may be empty |
| orders | Optional separate shopping order records, described below |
| import_sources | Optional private CSV evidence and reconciliation history, described below |

Each transaction requires these fields, with an optional income_source label for incoming cash:

| Field | Type / meaning |
| :--- | :--- |
| id | Unique local identifier; use new local IDs rather than provider identifiers |
| date | Transaction date |
| merchant | Local display label |
| amount_minor | Nonzero signed pennies: positive is incoming cash |
| category | User-chosen grouping; no private or provider-specific classification rules |
| kind | income, spending, refund, investment_deposit, investment_withdrawal, transfer_in, or transfer_out |
| control | inside, mixed or outside; a user-supplied lens |
| recurring | Boolean; explicitly supplied, never inferred from frequency |
| notable | Boolean; marks the payment without adding it again |
| account_id | Optional local import-account identity; distinct from bank reference and holding account labels |
| external_id | Optional bank transaction reference, at most 255 characters; meaningful only within its source account |

income, refund, investment_withdrawal and transfer_in must be positive. spending, investment_deposit and transfer_out must be negative. Refunds must use the spending category they offset. Separate internal transfer legs both belong in the ledger if both accounts are in scope; do not import only one leg and mistake it for earnings or spending. Opening cash starts the cash-reserve history. Adding every signed ledger movement once gives the closing cash balance. It does not infer wealth or investment gains.

baseline is null or `{ "income_minor": 304600, "commitments": [...] }`. Monthly income must be positive. Each commitment is `{ "label": "Example annual cover", "amount_minor": 18370, "frequency": "annual" }`, with positive amount and frequency monthly or annual. At most 100 commitments. Annual amounts become monthly equivalents by dividing by 12; sub-penny equivalents are displayed rounded, but retained in the calculation.

Each holding is `{ "label": "Example reserve", "value_minor": 547300, "date": "2026-06-30" }`. Investment values are nonnegative; cash values may be negative for an overdraft. All are dated no later than coverage. At most 100 holdings. Snapshots are not added to cash flows or used to infer returns.

holding_history uses the same row shape as holdings, with at most 1,200 snapshots. Each label/date pair must be unique. Investment values must be nonnegative; cash values may be negative. Dates must be no later than coverage. Use separate entries for each holding and date. The chart joins those recorded values; it does not fabricate missing valuations or infer returns. Latest holdings are supplied separately, so keep those snapshots consistent with your history.

mortgage is null or `{ "balance_minor": 15370000, "annual_rate": 0.0387, "payment_minor": 94600, "date": "2026-06-30" }`. Balance and payment are positive; rate is a finite fraction between 0 and 1; date is no later than coverage. The illustration starts at this date and does not assert principal repayment from transaction history.

Each action is `{ "label": "Compare example cover", "date": "2026-08-23", "status": "estimated" }`. Status is confirmed, estimated or unknown. Unknown requires date null; the other statuses require a day-precise date. At most 100 actions. A payment date does not establish a renewal date. Supply action dates independently.

Use the blank HTML app for your own data. For a complete fictional example, load Steve’s made-up data from the app and export JSON. If you construct JSON from that example, remove all invented rows and snapshots, set fictional to false, and keep it private.

## Optional chart detail

An incoming transaction may include `income_source`, a non-empty label such as Salary or Freelance. Outgoing stacks use the transaction kind and `recurring`: investment contributions, transfers, regular commitments or other spending.

`income_history` is an optional list of records with `date`, `gross_minor` and `take_home_minor`. Use at most one record per calendar month. Values must be non-negative integer pennies, gross must be at least take-home, and dates must not exceed `as_of`. These recorded values supply the two income lines. Without them, the chart shows earned income from the ledger alone.

`holding_history` records portfolio valuations rather than calculating market returns. Cash transfers and these valuations are separate views of the same finances; never add transfers to the portfolio valuation again.

## Spending and breakdowns

Optional transaction fields: `work_expense` (boolean), `note` (short text) and `reimbursement_id` (the id of a positive `transfer_in` payment). A spending row can link one incoming repayment, no larger than the original payment. Each repayment can be linked only once. The link reduces household spending, while cash flow still includes both signed payments once. A refund cannot also be linked as a repayment. Work expenses are excluded from the spending view until selected. CSV imports do not infer these fields.

Each baseline commitment may include `group`: Mortgage, Bills or Recurring, plus a short `basis` description. Older commitments without a group appear under Recurring. Annual commitments divide by 12 within their group. Available is take-home less every commitment; it may be negative. This view is independent of the period filter.

Optional top-level `orders` records use `id`, `date`, `label`, `category`, `amount_minor` and `recurring`. IDs are unique, amounts positive integer pennies, dates no later than coverage, and recurring is boolean. At most 5,000 records. These supply the separate Shopping / Amazon order detail only and never change ledger totals. Do not add them as bank transactions again.

Mortgage may include `equity` with positive integer `purchase_price_minor`, `deposit_minor` and `current_value_minor`. Deposit cannot exceed purchase price; the current loan cannot exceed the original purchase price less deposit. Principal repaid is that original loan less current balance. Valuation change is assumed current value less purchase price; equity is assumed current value less current loan. Omit these fields when unavailable. All projections remain illustrations.


## Dated value precision and saved views

Holdings and holding_history may include kind (investment or cash) and basis (measured or estimated). Investment and measured are the legacy defaults. Cash is displayed separately from investment totals. A total portfolio observation requires every recorded investment account to have a measured value on that exact date; no earlier balance is carried forward to invent a total.

An estimated action may use YYYY-MM when only its month is known. Confirmed actions require YYYY-MM-DD. A month-only action never acquires an invented day or days-left countdown.

A saved HTML may also contain view-state JSON for folds, the selected period, markers, one header selection and up to 20 goals. This is a separate, strictly validated preference format, not a bank ledger. Goals require id, label, a supported metric, signed start_minor, target_minor, start_date and mode (history, goal or both). Legacy goals retain their target-above-start rule. New goals may specify direction at_least or at_most, including targets already met, and an optional deadline on or after start_date. Amount metrics include recorded savings, ledger cash, investments, giving, principal repaid, loan balance, monthly household spending and income left/take-home. Rate goals use metric rate:not_spent, rate:savings or rate:savings_investments, plus basis income or after_outside. For rate goals the integer minor-unit fields are hundredths of a percentage point, not GBP pennies. Ratios are compared at exact integer boundaries; unknown coverage remains unknown. Deadlines are plans, not forecasts. Imported HTML supplies data only; its scripts are not executed. Imports replace the view, and preferences from a different ledger cannot silently attach its old goals to the new one.

Combined absolute ledger amounts plus opening cash must fit exact JavaScript integer-penny precision. Individual limits still apply.

Income history may include an optional `events` array on a dated pay record. Each event has only `kind` (`job_switch`, `pay_rise` or `promotion`) and a short `label`. It is shown at that record's date; it does not change income. At most six per record and 240 across the history. Duplicate kind/label pairs within a record are refused. Older records without events remain valid. Labels are displayed as text, never code. Do not infer events from amounts alone.

Financial history, including transactions, holdings, holding history and income history, may span at most 1,200 calendar months through as_of. Longer ranges are rejected with an explanation rather than silently dropping months from charts. Check suspicious dates or import a shorter range.

## Account sources and savings

A holding or historical observation may include a nonempty `account_id` to keep account identity stable when its display label changes. Without it, the label is the legacy account identifier. One account cannot change between cash and investment kind. Duplicate historical observations use account identity plus date.

Cash observations may include `purpose`: `savings`, `current` or `other`. Missing purpose is unspecified; a cash account is never assumed to be savings from its name or transactions. The fictional cash reserve is explicitly marked as savings. Investment rows cannot carry a cash purpose.

The account-value chart uses supplied observations only. The user can choose identified savings, all cash accounts as separate lines, or an individual account. Estimates stay labelled. A measured aggregate or goal requires a measured observation for every selected account on that exact date; missing observations are unknown. Header income left means earned income less household spending for each income period, excluding investment/internal transfers from spending. It never establishes an accumulated savings balance.

Account balance goals have metric `savings` and may name an `account` source, using `account:` followed by an account ID, `savings` or `all_cash`. The header and account graph have separate source controls. Legacy `cash` goals remain ledger calculations. Adding observations through the local editor preserves current goals and view choices; replacing the whole imported ledger retains its existing replacement behavior.

## Manual records and backups

Optional `mortgage_history` holds at most 1,200 earlier measured observations for the mortgage/property represented by the current `mortgage`. Every observation has exactly `date`, `balance_minor`, `annual_rate`, `payment_minor` and `source`. Dates are exact `YYYY-MM-DD`, unique and strictly earlier than the current mortgage date. A nonempty history requires a current mortgage. It covers fewer than 1,200 months back from `as_of`. Balances are nonnegative integer pennies; payments are positive integer pennies; the standard absolute amount limit is 10^12 pennies. Rates are finite fractions from 0 to 1. `source` is nonblank plain text within the existing 120 UTF-16-unit limit. Rows may arrive in any order. These are independent dated observations, not a reconstructed amortisation schedule: the chart draws separate points and leaves intermediate dates unknown. The current mortgage alone still supplies the future illustration. Rate/payment changes appear in the exact history table and point labels. The field survives saved HTML, JSON backup and payment replacement; older documents may omit it. Records for an unrelated previous home should not be put into this collection. An annotation alone never creates an observation.

Optional `mortgage_events` is a maximum of 200 user-authored house/mortgage annotations. Every record has exactly `id`, `date`, `kind`, `label` and `status`. IDs must be unique within the collection. IDs and short labels use the normal 120-character plain-text limit and cannot be blank. Kinds are `deposit`, `purchase`, `mortgage_change`, `house_move` or `other`. An exact `YYYY-MM-DD` date is `confirmed` or `estimated`; a month-only `YYYY-MM` date must be `estimated`; a missing date is `null` with `unknown` status. Historical and future dates are supported independently of financial coverage. Unknown dates remain in the editable list without a graph line. These are annotations only: they never infer a transaction, deposit amount, house value, loan reset, new rate, payment or balance, and never extend `as_of` or alter payoff calculations. Older ledgers can omit this collection. Payment merge/replacement preserves current annotations; whole-data or backup restore uses its own annotations. Add, edit and remove use the existing saved-draft and Undo flow. Empty downloaded ledgers contain no invented events.

Optional `guidance_notes` holds stable IDs, authored text, an optional exact/month-only date and manual/edited_suggestion origin. `career_events` holds stable IDs, labels, event kinds and exact, month-only or unknown dates with explicit status. Gross monthly pay may be null when unknown. A baseline income may be null while its commitments remain recorded. Actions may have stable IDs.

`transaction_coverage` records the covered bank interval separately from the latest dated balance. `transaction_edits` retains explicit classification corrections by an exact date/merchant/amount tuple. A new CSV applies a correction only to a unique match; ambiguous or absent matches stay retained for review. Explicit date, merchant and amount_minor corrections are allowed while original imported text and exact identity anchors remain retained. Optional income_source and reimbursement_id corrections may be null to explicitly remove those fields from the effective payment. Effective reimbursement references must satisfy the ordinary unique, positive transfer-in and original-cost limits; self-links fail. No correction guesses payment identity.

Optional `extensions` stores inert compatible metadata, bounded to 256 KiB. Prototype keys and credential-shaped fields are rejected. The version-1 `treasury-backup` JSON envelope contains normalized data, view state including goals and drafts, appearance, provenance and compatible metadata. Unsupported versions fail without replacing the current document. Saved HTML retains the same data, view and metadata; imports read only inert JSON blocks, never executable code.

In a saved payment correction, `note: ""` explicitly clears that note. The normalized transaction itself omits the empty note.


## Cash-savings classifications and percentage pins

An optional transaction `savings_flow` is `deposit`, `withdrawal`, `internal`, `other` or `unknown`, and is valid only on transfer_in/out rows. A deposit must be a negative transfer_out and a withdrawal a positive transfer_in. Classify the funding-side record once; counterpart/internal moves are excluded. Other/unknown do not assert a contribution; missing classifications remain unknown for savings rates. Linked work/shared repayments are explicitly identified non-savings. Dedicated investment_deposit/withdrawal kinds remain separate. No amount, balance, merchant name or interest payment establishes a savings contribution. Payment corrections can retain savings_flow and are re-applied only through existing unique-identity rules. Changing a payment away from transfer removes an obsolete cash label.

The separate saved view permits pin kind `rate` with id `not_spent`, `savings` or `savings_investments`, plus independent `rateBasis` (`income` or `after_outside`). Old auto, metric and goal pins retain currency meanings; legacy currency goal fields retain their GBP meaning. New explicitly typed rate goals use hundredths of percentage points and a separately declared income basis; they are never interpreted as GBP. Legacy saved views without these fields remain loadable.

Rate periods require an explicit transaction_coverage interval covering that observed period. The second basis subtracts actual net outside-control household spending, not mixed costs, baseline plans or every recurring payment. Unclassified potential cash transfers make savings measures partial/unavailable. A linked reimbursement is not an unclassified savings transfer. Nonpositive bases yield N/A, not zero; negative and above100 rates remain possible. Whole-range rate uses summed numerators/denominators. OLS uses supported complete period points and actual elapsed dates, at least two distinct dates, and never extends beyond its observed fit dates. All UI filters remain separate from the bank ledger.

## CSV source evidence

Optional `import_sources` retains original UTF-8 CSV text and strictly data-only mapping/options, account identity and row decisions. It is private data, including unused CSV columns. Up to 64 sources and 8 MiB serialized UTF-8 source history are supported. Legacy valid documents without this field remain readable. Old app copies do not promise forward compatibility with newly added evidence fields; keep a previous backup before changing app versions.

Each source has `id`, `account_id`, `account_label`, `file_name`, `raw`, `mapping`, `options`, `active` and `rows`. Row bindings use the 1-based ordinal among nonzero imported payments, a nullable saved `transaction_id`, `action`, and optional `external_id`. Explicit replacement can retain previous records/corrections in `revisions`; an explicit keep of conflicting facts may retain `kept_core`. These are evidence, never executable instructions. Inactive sources retain history after explicit payment replacement and cannot claim identity for newly reused IDs.

A known reference or prior exact source row can identify a repeat within one account. Date, amount and description alone are only a possible match. Changes require an explicit choice; source and account identity are not guessed across banks. The import model validates evidence and incoming core fields together. A `transaction_coverage` with basis `observed` is a minimum/maximum record span, not a declaration that every payment in that interval is present. It cannot qualify a complete savings-rate period.

CSV dates also accept English named months with four-digit years and complete ISO timestamps. The written calendar date is retained without time-zone conversion. Numeric day/month order remains explicit.

The optional CSV options `profile: "revolut-v1"` and `product` identify the detected ten-column Revolut statement format and one explicitly selected product. Its Completed Date, Description, Amount and Currency columns are mandatory, using signed dot-decimal GBP. Only completed rows of the selected product become payments. Fees are nonnegative exact pennies and deducted from Amount; no separate fee payment is invented. The preview counts excluded non-completed and other-product rows and shows deducted fees. Unknown states, malformed fees, missing products, foreign currencies or contradictory mappings refuse the file atomically, including when found on an excluded row.

Full original text remains retained. Bindings retain the existing ordinal among nonzero imported payments; local parser IDs retain original raw-row positions. An account cannot claim two Revolut products, including through inactive sources. Old retained sources without profile options keep their prior interpretation rather than being silently filtered or repriced. New file previews require the detected profile.

## OFX source evidence

An OFX source uses the same retained source and row-binding structure, with `format: "ofx"`, `ofx_account`, and empty `mapping` and `options` objects. Legacy sources with no `format` remain CSV. `ofx_account` is an exact parser-derived JSON string of account type, bank ID, branch ID, account ID and optional institution organisation/ID. It is private evidence, not a display label or login. One retained account cannot claim two OFX identities; one OFX identity cannot silently acquire a second retained account.

Original UTF-8 OFX text stays in `raw`. The browser and Python validators reparse it, check the account identity and validate each binding against the nonzero transaction ordinal and original FITID. FITIDs are matched only within the selected source account; repeated IDs still require review. Raw timestamps remain in the source, while chart dates retain the written calendar day. Amounts use exact signed pennies; fractions of a penny are rejected rather than rounded. Zero rows are counted in the preview and remain in raw evidence.

Supported headers: OFX 1 versions 102/103/160 with OFXHEADER 100, uncompressed/unsecured SGML; OFX 2 versions 200/201/202/203/210/211/220/221/230 with OFXHEADER 200 and SECURITY NONE. The supported payload is a single posted checking, savings or credit-card statement in GBP. Unknown tags, attributes, namespace prefixes, general entities, DTDs, CDATA, corrections, pending lists and per-transaction foreign currencies fail explicitly. Only the five predefined XML character entities and numeric character references are decoded. No URL, external resource or file referenced by imported text is opened.

The existing 8 MiB file/evidence and 25,000-payment limits apply. The parser also bounds nesting and element count. Statement balances, unsupported types, account numbers and source timestamps never manufacture additional ledger payments or observations. See README for user-facing limits and official specification links. New source fields do not promise compatibility with older downloaded app versions; keep a prior backup before updating.

Optional `performance_history` stores independent recorded investment performance periods: `{id,start,end,label,gain_minor,source,method,return_percent?}`. Dates are exact ISO dates, start is on or before end, and end is on or before `as_of`. IDs are unique; gain is signed integer pennies. An optional finite numeric return is a recorded percentage, with null meaning unknown. Source and method are required plain text. Overlapping periods are valid and never added, joined or interpolated. No gain or cash interest is inferred from account balances or bank imports.

Optional saved appearance `colors` accepts `gold_white` or `semantic`; omitted means the existing semantic palette. This affects presentation only and does not reclassify records, change amounts, erase drafts or alter source coverage.

Hosted fictional landings open Guidance and restart supported autoplay with three example notable outgoings. Local saved copies keep explicit folds, playback and marker choices. The legacy paymentMarkers field remains readable for recovery, while each selected notable payment now directly displays its date marker; there is no separate master checkbox.
