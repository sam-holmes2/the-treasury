# The Treasury

See what you earn, what you spend and how your savings change.

**[Try Steve’s demo](https://the-treasury.samuelsmanuals.com/) · [Download your blank Treasury](https://github.com/sam-holmes2/the-treasury/raw/refs/heads/main/Treasury.html)**

- Explore your money in interactive charts.
- Track savings, investments and your mortgage.
- Set goals and add your own notes.

Steve’s figures are made up. Download a blank Treasury to use your own data.

## Get started

1. Download **Treasury.html** and open it in your browser. Nothing to install.
2. Choose your currency in **Currency settings** before adding records. Import a matching-currency bank CSV or supported OFX statement, or add records yourself. Check the preview before importing.
3. Use **Save copy** to keep your changes. Open that saved file next time.

Each Treasury uses one currency. Saved files remember it; choosing another currency after adding records downloads a separate blank Treasury and leaves your current records in place. There is no currency conversion. Bank exports vary, so some files may need adjusting first. Help inside the app covers importing and saving.

## Your data

The downloaded app works offline. No account, bank connection or analytics. It doesn’t send your records anywhere.

Saved files and remembered drafts aren’t encrypted. Keep them private, and keep your original bank exports as backups.

[Code: MIT. Artwork and third-party terms](LICENSE)

<details>
<summary>Working on the app</summary>

These commands need the full source checkout. The public download repository contains released files only.

- `app/` contains the HTML, CSS and JavaScript. Header styling is in `app/progress.css`.
- `tests/` contains the existing checks. `examples/` contains invented data only.
- Use Python 3.11 or newer and Node.js. No extra packages are needed.

From the source root, check header/history behaviour and the build/preview safeguards:

```sh
node tests/test_progress.cjs
python3 -B -m unittest discover -s tests -p 'test_*.py'
```

After source review and updating the changed file hashes in `release-manifest.json`, build into a new directory:

```sh
python3 -B scripts/build_release.py ../treasury-review
python3 -B scripts/preview.py ../treasury-review
```

Open `http://127.0.0.1:8767/app/` for Steve's demo. Stop the preview with Ctrl+C. Check changed desktop, phone and keyboard journeys; automated checks do not prove the layout looks right. Shared UI fixes also belong in the personal variant through its separate private workflow.

Keep real statements and personal records out of the source, builds and test reports. A passing build is a local candidate: publishing, download updates and hosted browser QA remain separate release steps requiring the owner's approval.

</details>
