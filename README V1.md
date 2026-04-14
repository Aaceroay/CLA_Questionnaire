# CLA Self-Assessment Questionnaire

An interactive, self-scoring Critical Language Awareness (CLA) questionnaire for Spanish Heritage Language workshops. Collects participant profile data and sends all responses to a private Google Sheet only you can access.

## Features

- Participant profile section (demographics, languages, background)
- Pre-test / Post-test session selector to measure CLA change across the workshop
- All 19 validated CLA items, updated for general non-student audiences
- Automatic reverse-scoring for 16 items
- Scores across 3 factors + overall CLA score (1–6 scale)
- 5-level diagnostic scale with personalized feedback messages
- Responses sent automatically to your private Google Sheet
- Runs entirely in any browser — no logins required for participants

---

## Part 1 — Deploy on GitHub Pages (~5 minutes)

1. Go to [github.com](https://github.com) → create a free account if needed
2. Click **+** → **New repository** → name it `cla-questionnaire` → set to **Public** → **Create**
3. Click **Add file** → **Upload files** → drag in `index.html`, `LICENSE.txt`, `README.md` → **Commit changes**
4. Go to **Settings** → **Pages** → Source: **main** branch, **/ (root)** → **Save**

Your live URL will be:
```
https://YOUR-USERNAME.github.io/cla-questionnaire/
```

---

## Part 2 — Connect your private Google Sheet (~15 minutes)

### Step 1 — Create a Google Sheet
Go to [sheets.google.com](https://sheets.google.com) → **Blank spreadsheet** → name it `CLA Workshop Responses`. Leave it empty — the script writes headers automatically on first submission.

### Step 2 — Open Apps Script
In the Sheet: **Extensions** → **Apps Script** → delete any default code.

### Step 3 — Paste this script

```javascript
function doPost(e) {
  try {
    var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
    var data = JSON.parse(e.postData.contents);

    var headers = [
      "timestamp","session",
      "firstname","lastinitial","email",
      "gender","birthyear","birthcountry",
      "live_region","live_country","work_region","work_country",
      "education","field_study","sector","sector_other","job_title",
      "shl_experience","heritage_speaker","home_language",
      "spanish_contexts","generation",
      "prior_attitude","prior_workshop",
      "motivation","comments","languages",
      "score_f1","score_f2","score_f3","score_overall",
      "cla_level","cla_level_name",
      "q1_raw","q2_raw","q3_raw","q4_raw","q5_raw",
      "q6_raw","q7_raw","q8_raw","q9_raw","q10_raw",
      "q11_raw","q12_raw","q13_raw","q14_raw","q15_raw",
      "q16_raw","q17_raw","q18_raw","q19_raw",
      "q1_adj","q2_adj","q3_adj","q4_adj","q5_adj",
      "q6_adj","q7_adj","q8_adj","q9_adj","q10_adj",
      "q11_adj","q12_adj","q13_adj","q14_adj","q15_adj",
      "q16_adj","q17_adj","q18_adj","q19_adj"
    ];

    if (sheet.getLastRow() === 0) {
      sheet.appendRow(headers);
      sheet.getRange(1,1,1,headers.length)
           .setFontWeight("bold")
           .setBackground("#1a1a18")
           .setFontColor("#ffffff");
      sheet.setFrozenRows(1);
    }

    var row = headers.map(function(h) {
      return data[h] !== undefined ? data[h] : "";
    });
    sheet.appendRow(row);

    return ContentService
      .createTextOutput(JSON.stringify({status:"success"}))
      .setMimeType(ContentService.MimeType.JSON);

  } catch(err) {
    return ContentService
      .createTextOutput(JSON.stringify({status:"error",message:err.toString()}))
      .setMimeType(ContentService.MimeType.JSON);
  }
}
```

### Step 4 — Deploy as a web app
1. Click **Deploy** → **New deployment**
2. Click the gear icon next to Type → select **Web app**
3. Settings:
   - Execute as: **Me**
   - Who has access: **Anyone**
4. Click **Deploy** → **Authorize access** → allow
5. **Copy the Web app URL** (looks like `https://script.google.com/macros/s/XXXXXXX/exec`)

> The Sheet itself remains 100% private to you. "Anyone" access only means the questionnaire can POST data to it — nobody can open or view the spreadsheet without your Google login.

### Step 5 — Add the URL to index.html
Find this line in `index.html`:
```javascript
const SHEET_URL = 'YOUR_GOOGLE_APPS_SCRIPT_URL_HERE';
```
Replace it with your copied URL:
```javascript
const SHEET_URL = 'https://script.google.com/macros/s/XXXXXXX/exec';
```
Save the file and re-upload it to GitHub.

### Step 6 — Test
Complete a test submission. A new row should appear in your Sheet within seconds.

---

## Understanding your spreadsheet

Each row = one submission.

| Column | Description |
|--------|-------------|
| `session` | PRE or POST |
| `firstname` + `lastinitial` | Used to match pre/post records for each participant |
| `score_overall` | Overall CLA score (1.00 – 6.00) |
| `score_f1/f2/f3` | Factor scores |
| `cla_level` | Numeric level 1–5 |
| `q1_raw – q19_raw` | Raw answers as given |
| `q1_adj – q19_adj` | Adjusted scores (reverse-scoring already applied) |

### Measuring CLA change (pre → post)
Filter by participant name to find their two rows. Then:
```
CLA change = POST score_overall − PRE score_overall
```
Positive = increase in CLA. Negative = decrease.

---

## License

**Creative Commons Attribution 4.0 International (CC BY 4.0)** — see `LICENSE.txt`.

## How to cite

**Interactive tool:**
> Acero Ayuda, A. (2026). *CLA Self-Assessment Tool* [Interactive questionnaire]. CC BY 4.0. https://github.com/alejandro-acero-ayuda/cla-questionnaire

**Measurement instrument:**
> Beaudrie, S., Amezcua, A., & Loza, S. (2019). Critical language awareness for the heritage context: Development and validation of a measurement questionnaire. *Language Testing*. https://doi.org/10.1177/0265532219844293
