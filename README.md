# Finance Dashboard

Static HTML spending dashboard. Fetches data from a secret GitHub Gist, no backend needed.

---

## Setup (one-time)

### 1. Install Python deps

```bash
pip3 install requests
```

### 2. Generate a GitHub token

Go to [github.com/settings/tokens/new](https://github.com/settings/tokens/new).
- Note: `finance-dashboard`
- Scopes: `gist` only
- No expiry (or 1 year, your call)

Save the token somewhere — you'll need it in step 3 and 4.

### 3. Run the export script

From the repo root:

```bash
GITHUB_TOKEN=ghp_xxx python3 finances/sync_to_gist.py
```

The script will:
- Read `finances/finance_data.json` (transactions, categories, insights)
- Create/update a secret Gist with the dashboard payload
- Save the Gist ID to `finances/.gist_id` (not committed — add to `.gitignore`)
- Print the Gist ID at the end

### 4. Hardcode the Gist ID in index.html

Open `dashboard/index.html` and replace:

```js
const GIST_ID = 'REPLACE_WITH_YOUR_GIST_ID';
```

with the ID printed by the script, e.g.:

```js
const GIST_ID = 'a1b2c3d4e5f6...';
```

### 5. Host on GitHub Pages

1. Create a new private GitHub repo named `finance-dashboard`
2. Push just the `dashboard/` contents to the root of `main`:
   ```bash
   cd dashboard
   git init
   git remote add origin git@github.com:your-username/finance-dashboard.git
   git add .
   git commit -m "init"
   git push -u origin main
   ```
3. Go to repo Settings → Pages → Source: `main` / `/(root)`
4. Your dashboard will be live at `https://your-username.github.io/finance-dashboard/`

### 6. First visit

Open the URL, enter your GitHub token when prompted. It's saved in `localStorage` — you won't need to enter it again unless you clear browser data or the token expires.

---

## Updating data

After running `/spending-categorisation` and `/spending-savings` on new statements:

```bash
GITHUB_TOKEN=ghp_xxx python3 finances/sync_to_gist.py
```

Then hit **↻ Atualizar** in the dashboard to pull the latest Gist content.

---

## Options

```
python3 finances/sync_to_gist.py --help

--token    GitHub PAT (or set GITHUB_TOKEN env var)
--dry-run  Build JSON and write to finances/finance_data_gist_preview.json without pushing
```

Dry run is useful to inspect the JSON before publishing:

```bash
python3 finances/sync_to_gist.py --dry-run
```

---

## Security

- The Gist is **secret** (unlisted), not public. Anyone with the Gist URL can read it.
- The GitHub token lives in `localStorage`. This is acceptable for a single-user private page with no third-party scripts.
- Never commit `finances/.gist_id` — it acts as the access URL for your financial data.
