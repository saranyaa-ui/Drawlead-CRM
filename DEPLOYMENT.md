# DrawLead CRM – Deployment (Vercel + Supabase, both free)

You do NOT edit any code. You only click, copy and paste.

What you need: a free GitHub account, a free Vercel account, a free Supabase account, and Git installed on your computer.

---
## STEP 1 — Push project to GitHub
1. On github.com click **New repository**. Name it `drawlead-crm`. Choose **Private**. Do NOT tick "Add a README". Click **Create repository**.
2. Open a terminal inside this project folder (the folder that contains `package.json`) and run these lines one by one (replace YOUR-USERNAME):

```
git init
git add .
git commit -m "DrawLead CRM"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/drawlead-crm.git
git push -u origin main
```

## STEP 2 — Create database (Supabase)
1. Go to supabase.com → **New project**. Name `drawlead`, set a database password (save it), choose the region nearest you (e.g. Mumbai). Wait ~2 minutes.
2. Left menu → **SQL Editor** → **New query**. Open the file `supabase/schema.sql` from this project, copy everything, paste, click **Run**. It must say "Success".
3. Left menu → **Project Settings → API**. Keep this page open; you need three values:
   - **Project URL**
   - **anon public** key
   - **service_role** key (secret – never share, never put in code)

## STEP 3 — Connect GitHub to Vercel
1. Go to vercel.com → **Add New… → Project** → **Import** your `drawlead-crm` repository (allow GitHub access if asked).
2. Use exactly these settings:

| Setting | Value |
|---|---|
| Framework Preset | **Other** |
| Root Directory | `./` (leave as is) |
| Build Command | leave default (it is `npm run build`, which only prints a message) |
| Output Directory | **public** |
| Install Command | leave default |

## STEP 4 — Add environment variables
Still on the same screen, open **Environment Variables** and add these four (Name → Value). Copy the names exactly.

| Name | Value |
|---|---|
| `SUPABASE_URL` | Project URL from Step 2 (looks like `https://abcd1234.supabase.co`) |
| `SUPABASE_ANON_KEY` | the **anon public** key |
| `SUPABASE_SERVICE_ROLE_KEY` | the **service_role** key |
| `SETUP_KEY` | any long password you invent, e.g. `Drawlead-Setup-92xK-temple-77` (used once in Step 6) |

## STEP 5 — Deploy
Click **Deploy**. After ~1 minute you get a link like `https://drawlead-crm.vercel.app`. From now on, every `git push` to `main` redeploys automatically.
(If you add or change a variable later: Vercel → Settings → Environment Variables, then Deployments → ⋯ → **Redeploy**.)

## STEP 6 — Create admin account
1. Open your Vercel link. On the login box open **First-time setup**.
2. Type your **email**, a **password** (8+ characters) in the main boxes, your **name**, and the **SETUP_KEY** from Step 4. Click **Create Admin account**. You are logged in.
3. This only works once. Later, delete or change `SETUP_KEY` in Vercel if you like.
4. Open the **Users** tab and create your people. **Name must match exactly** how that person is written in the Setter / Closer columns (e.g. `Asha`). Roles: Setter, Closer, Manager, Admin.

## STEP 7 — Test Excel import
1. Log in as Admin → **Import** → **Choose file** → pick `DrawLead_50_Chennai_ERP_Cold_Call_Prospects.xlsx`.
2. Check the column mapping and preview, press **Import**. Expect: New 49, Errors 1 (the JGC India EPC row has no real phone number).
3. Press F5 (refresh). The leads must still be there (they are in the database now).
4. Import the same file again → New 0, nothing duplicated.

## STEP 8 — Test Setter / Closer / Manager login
1. In **Users**, create a Setter (name = a name used in your leads' Setter column), a Closer and a Manager.
2. Assign a few leads to them (open a lead → Setter / Closer fields → Save).
3. Open a private/incognito window, log in as each:
   - **Setter**: sees only own leads, no money fields, no Forecast/Import/Users tabs.
   - **Closer**: sees only leads where they are Closer, with money.
   - **Manager**: sees all leads and dashboards, can edit leads, no Import/Users/Delete.
   - **Admin**: everything.

---
## Production test checklist
- [ ] Site opens on the Vercel link and shows the login box
- [ ] Admin created in Step 6; wrong password is rejected
- [ ] Import of 50-row file = 49 new, 1 error; re-import = 0 new
- [ ] Refresh the page → data still there; open the link on your phone → same data
- [ ] Move a lead on the Board, refresh → stage kept
- [ ] Setter/Closer/Manager restrictions as in Step 8
- [ ] Log activity, add proposal and payment on a lead → Dashboard and Forecast change
- [ ] Mark a lead Lost without a reason → refused

## Troubleshooting
- "Server is missing SUPABASE_URL…": a variable is missing/misspelled → fix in Vercel, then Redeploy.
- Page not found after deploy: Output Directory must be `public`.
- "Not saved … (retrying)" at the top: check internet; if it persists, look at Vercel → Logs.
- Forgot a user's password: Supabase → Authentication → Users → ⋯ → Send password recovery / delete user and re-create in the Users tab.

## Good to know
- Free Supabase projects **pause after ~7 days with no use**; open the Supabase dashboard and click Restore. Data is kept.
- **Backup**: every week, Leads tab → **Export Excel**, and keep the file. Supabase free plan has no downloadable backups.
- If two people edit the same lead at the same second, the last save wins. The page refreshes data every 60 seconds.
