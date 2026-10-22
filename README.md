# FinSentry AI

An analyst workspace for investigating simulated USD card payments. Open **Overview** to see priority cases, **Review queue** to search/filter work, or **Investigate payment** to assess a new payment.

## Run locally

Cloning from GitHub: follow [data and model setup](docs/DATA_AND_MODEL_SETUP.md) to install dependencies and recreate the excluded local dataset/model/database assets. Team handoff order and review records are documented in [GITHUB_HANDOFF.md](docs/GITHUB_HANDOFF.md).

To start just the backend and its database, run root `start_backend.py` using VS Code's **Run Python File**, or `python start_backend.py`. It uses the project's virtual environment automatically and keeps the backend running in your terminal, showing request logs such as `200 OK` when you open the website. Press **Ctrl+C** to stop the backend; MySQL remains available. Running this file replaces an existing project backend once its investigations have finished. After backend code changes, stop and run the file again.

From E:\FinSentry in PowerShell:

```powershell
& .\scripts\start_project.ps1
```

Open http://127.0.0.1:8000. To rebuild changed frontend sources, add `-Rebuild`.

For VS Code development, use two terminals:

```powershell
# Terminal 1 — backend; reuse the running backend if port 8000 is already occupied.
cd E:\FinSentry
.\.venv\Scripts\python.exe scripts\local_mysql.py start
.\.venv\Scripts\python.exe -m uvicorn backend.main:app --reload --host 127.0.0.1 --port 8000
```

```powershell
# Terminal 2 — frontend
cd E:\FinSentry\frontend
npm run dev
```

Open http://127.0.0.1:5173 for frontend hot reload. The developer API reference remains at `/docs` and is not linked from the analyst interface.

## What an analyst sees

- A priority queue, search and filters for cases needing attention, escalations and recorded reviews.
- A recommendation supported by spending, time, device, location and prior-case evidence.
- A readable customer-history comparison and clear missing-information messages.
- Separate reviewer decisions and notes. A follow-up or escalation stays outstanding until cleared by a reviewer.
- Automatic case-list refresh every 20 seconds while the tab is visible; up to 100 most recent cases are loaded. This is not a live bank feed.

Risk indication is the model estimate, not certainty. Evidence coverage is a different concept. No payment is blocked or moved.

## Data and model

The active source is Sparkov: `dataset/sparkov/fraudTrain.csv` and `fraudTest.csv`, 1,852,394 transactions. Source history is indexed read-only in `runtime/sparkov/history.sqlite`. Submitted payments, investigations and reviews are saved in local MySQL. Model artifacts are in `models/sparkov_v2`; model and source-store manifests must match.

The classifier's later-test F1 is **0.7990**, precision **0.8323**, recall **0.7683**, and AP **0.8686**. These are simulation classifier results, not complete-policy or real-world banking accuracy. Calibration and threshold selection use separate chronological development slices. History contains only strictly earlier, label-free payments. Prior fraud intelligence uses development outcomes with a simulated 24-hour confirmation delay.

The system supports USD card payments in a simulated 2020 history. Dates outside the evaluated period, missing recent history and unsupported categories/merchants cause uncertainty. Devices and payment-method context are simulated. Human reviews do not automatically become fraud labels.

## Files and checks

- `frontend/src/pages`, `components`, `hooks`, `services`: analyst interface.
- `backend/api`, `database`, `schemas`: payment input, saved cases/reviews and source history.
- `backend/agents`, `graph`: five coordinating responsibilities, evidence-dependent policy and bounded follow-up.
- `backend/ml`: shared causal features, training, calibration and evaluation.
- `reports/architecture.md`, `SPARKOV_DEMO_GUIDE.md`, `TIME_LOCATION_WALKTHROUGH.md`: current design and walkthroughs.

```powershell
$env:FINSENTRY_TEST_MYSQL='1'
.\.venv\Scripts\python.exe -m pytest -q
node --test frontend/tests/analyst.test.mjs
npm --prefix frontend run build
```

Fresh setup: Python 3.12 and Node 20+, project `.venv`, dependencies from `backend/requirements-lock.txt` and `frontend/package-lock.json`, then `python -m backend.ml.sparkov_train` before startup. Training should run with the backend stopped. The local MySQL helper uses a project-owned instance on 127.0.0.1:3307. Groq is the selected explanation provider; keep `GROQ_API_KEY` only in private `.env`, never in frontend files. Explanations use a labeled factual fallback on provider/validation failure.

## Cleanup and limitations

In **Review queue**, choose **Delete** beside a completed or interrupted case and confirm the payment details. This permanently removes the submitted payment, all its investigations and review notes from MySQL and the queue in one transaction. Queued/running investigations cannot be deleted. The removed payment will no longer contribute to later customer-history lookups; assessments already saved for other payments remain historical snapshots. The source Sparkov dataset and trained model are unchanged.

Superseded dataset/code assets have been removed from active folders and retained recoverably in `runtime/retired-backup-20261022`. Its manifest lists every moved path. Obsolete database tables were removed after verifying all Sparkov records were preserved. Current startup has no retired-model dependency.

This is a local demonstration without production authentication or durable background job resume. Docker source has been retargeted to the active model and a read-only historical-store mount, but container execution has not been verified. Browser automation is unavailable in this session; see the verification report for the exact QA performed.
