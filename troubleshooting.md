---
layout: default
title: Troubleshooting
---

# Troubleshooting

Common issues when launching Jupyter through Morehouse Tapis, and how to fix them.

---

## The job is stuck in STAGING_INPUTS and never starts

**This is the most common problem.** The job sits in `STAGING_INPUTS` and never reaches `RUNNING`.

**Cause:** Tapis cannot copy the setup script (`tap-ilogin.sh`) from the `cloud.data` system because you do not have credentials there. You may have working credentials on `vista` (so Vista file browsing works fine) but still be missing them on `cloud.data`.

If you try to browse `cloud.data` in the Files browser, you will see the underlying error:

```
SSH_POOL_MISSING_CREDENTIALS  Host: cloud.data.tacc.utexas.edu  AuthnMethod: TMS_KEYS
```

**Fix:** Verify your **TMS keys** for `cloud.data`. Once your keys are verified, you will be able to browse `cloud.data`, and a resubmitted job will stage cleanly through to `RUNNING`. No administrator action is needed.

**Confirm the fix before resubmitting:** in the Files browser, open
`cloud.data` : `/corral/tacc/aci/CEP/applications/v3/interactive-template/tap`
and confirm you can see `tap-ilogin.sh`. If you can see it, staging will work.

---

## "required key [name] not found" when I submit

**Cause:** Your submission JSON is missing the `name` field, or you pasted the full app definition instead of a job request.

**Fix:** Submit exactly this:

```json
{ "name": "jupyter", "appId": "jupyter-hpc-native", "appVersion": "vista" }
```

`name` is the job name and is required.

---

## I clicked the output folder and got "path not found"

**Cause:** You opened the job's output or files view before the job started running. The output folder does not exist until the job runs.

**Fix:** Ignore it. This error is harmless. Just watch the `status` line instead, and only open `tapisjob.out` once the status reads `RUNNING`.

---

## The job is RUNNING but I don't see a URL

**Cause:** The URL is not published the instant the job hits `RUNNING`. The compute node needs a minute or two to start the Jupyter server.

**Fix:** Wait one to two minutes, then open the **`tapisjob.out`** file. Look for the line that begins `TACC: JUPYTER_URL is ...` and copy that full URL, including the `?token=...`.

---

## I have a URL but the page won't load

**Cause:** You may have copied one of the internal URLs from the log instead of the public one.

**Fix:** Use only the URL on the `TACC: JUPYTER_URL is ...` line, which points to `vista.tacc.utexas.edu:<port>`. The URLs ending in `...vista.tacc.utexas.edu:8888` and `127.0.0.1:8888` are internal to the compute node and will not load from your laptop.

---

## My session ended unexpectedly

**Cause:** You hit the `maxMinutes` time limit (default 120 minutes).

**Fix:** Resubmit the job. To get a longer session next time, add a higher `maxMinutes` value to the JSON. Files saved in `$WORK` persist between sessions, so save there often.

---

## Getting Help

- **TACC Documentation:** [docs.tacc.utexas.edu](https://docs.tacc.utexas.edu/)
- **TACC Support Ticket:** [portal.tacc.utexas.edu/tacc-consulting](https://portal.tacc.utexas.edu/tacc-consulting)
- **MSCF Questions:** [ashley.scruse@morehouse.edu](mailto:ashley.scruse@morehouse.edu)
