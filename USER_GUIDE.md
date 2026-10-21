# No slop user guide

[Open the browser tool](https://www.zuzlan.name/ai-anti-detect/?lang=en) | [Download the CLI](https://www.zuzlan.name/ai-anti-detect/downloads/bez-slopu.mjs) | [Back to README](README.md)

## Clean and inspect text

Paste text into **Original text**, or open a UTF-8 file. Cleaning starts automatically. The original remains available for comparison; highlights mark changed or removed characters. Vertical markers show changes to invisible characters.

Review **Clean text** and the **Character report** before copying or downloading. The report identifies the original line and column, character, reason, and action. You can also download it as JSON.

| Action | Meaning |
| --- | --- |
| Removed | The cleaner deleted the character. |
| Replaced | The cleaner converted the character. |
| Preserved | The character was checked and retained. |
| Review | The character needs a decision before strict export. |

In **Strict Unicode**, unresolved findings disable copying and text download. Read the report, correct any unwanted character in the original, and review the result again. A meaningful character may require a manual decision; changing modes does not resolve the finding.

## Modes and writing systems

**Keep language** and **Strict Unicode** preserve Unicode letters and apply NFC normalization. Long dashes, typographic quotes, special spaces, and supported invisible characters follow the same cleaning rules in both modes. Ordinary spaces, indentation, tabs, and blank lines remain in place.

**ASCII only** checks the original before conversion. It transliterates supported Cyrillic letters, removes Latin accents, and replaces characters without an equivalent with `?`. Check the warnings: emoji and many writing systems cannot be preserved in ASCII.

The **Writing systems** setting is independent of interface language. Available profiles are `all`, `latin`, `cyrillic`, `japanese`, and `arabic`. The last three also allow Latin. Mixed lookalike letters within a word can still require review.

**Simplify decorative letters** is optional. It can change mathematical notation, so leave it off unless that conversion is intended.

## Use the CLI

Save the standalone `bez-slopu.mjs` download in a local folder and install Node.js 22 or later. Run commands from the folder containing the file.

```sh
# Clean a UTF-8 file into a separate output file
node bez-slopu.mjs draft.txt -o clean.txt

# Audit stdin and return JSON, blocking unresolved findings
node bez-slopu.mjs --strict --json < draft.txt

# Check a file without writing plain text
node bez-slopu.mjs --check --json draft.txt

# Limit expected writing systems
node bez-slopu.mjs --strict --profile cyrillic draft.txt -o clean.txt

# List options
node bez-slopu.mjs --help
```

`-o` overwrites the named output file. The input and output must be different files. A blocked run leaves output files untouched. Plain stdout contains only the result; diagnostics go to stderr. With `--json`, diagnostics are included in JSON.

| Exit code | Meaning |
| --- | --- |
| `0` | Success; with `--check`, no changes found. |
| `1` | `--check` found changes. |
| `2` | Invalid arguments, invalid UTF-8, or a file error. |
| `3` | Export blocked by unresolved findings; takes priority over code `1`. |

For scripts and agents, pass text through stdin as data. In a strict workflow, use the JSON `text` only when the exit code is `0`, `blocked` is `false`, `reportCounts.review` is `0`, and warnings have been checked. Exit `3` returns diagnostic JSON, even with `-o`; its `text` is not approved output. Ordinary `text` mode can exit successfully while reviews remain.

## Reports and limits

Report positions refer to the original input. `index` counts UTF-16 units from zero; `line` and `column` start at one, with columns counting code points. Report details are capped at 300 records, prioritizing reviews. `reportCounts` still covers all findings when `reportTruncated` is true.

The browser accepts files up to 5 MiB and text up to two million UTF-16 units. The CLI has no corresponding fixed limit but reads the complete input into memory.

Browser processing stays on your device. Loading the page requires a connection; the downloaded CLI works offline. The browser keeps text in the open page's memory, so save your result before closing it.

The cleaner targets prose, not code formatting or authorship analysis. It does not guarantee AI-detector results or detection of every visual character substitution.

For more help, see the [browser FAQ](https://www.zuzlan.name/ai-anti-detect/?lang=en#faq) or the [author's website](https://www.zuzlan.name).
