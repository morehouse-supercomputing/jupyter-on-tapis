---
layout: default
title: Launching Jupyter via Tapis
---

# Launching Jupyter on Vista via Morehouse Tapis

This guide walks you through starting an interactive JupyterLab session on a TACC Vista GPU node using the **Morehouse Tapis web interface**. No SSH, no tunnels, no command line. Everything happens in your browser.

> **Looking for other ways to run Jupyter?** The [Launching Jupyter on HPC guide](https://morehouse-supercomputing.github.io/jupyter-on-hpc/) covers the SSH + idev method and the TACC Analysis Portal. This guide covers the Tapis method specifically.

**What you will use:**

| Item | Value |
|------|-------|
| Tapis interface | `https://morehouse.tapis.io` |
| App | `jupyter-hpc-native` (version `vista`) |
| System | Vista (GPU node) |
| Allocation | Handled automatically by the app |

> **Vista only for now.** This app runs on Vista.

---

## Before You Start

You need:

1. A **TACC account** on the Morehouse allocation. If you do not have one, start with the [MSF Getting Started guide](https://morehouse-supercomputing.github.io/mscf-getting-started/).
2. **Verified TMS keys** for the systems Tapis uses. This is the single most common reason the job fails, so it has its own check below.

### One-time check: verify your TMS keys

The Jupyter app copies a small setup script from a storage system called `cloud.data`. If you do not have credentials on `cloud.data`, the job will hang forever and never start. To confirm you are ready:

1. In the Tapis UI, open the **Files** browser.
2. Select the **cloud.data** system.
3. Browse to `/corral/tacc/aci/CEP/applications/v3/interactive-template/tap`.
4. Confirm you can see a file named **`tap-ilogin.sh`**.

If you can see that file, you are good to go. If you get an error like `SSH_POOL_MISSING_CREDENTIALS`, verify your TMS keys first (see [Troubleshooting](troubleshooting.html)), then come back.

---

## Step 1: Submit the Job

In the Tapis UI, find the `jupyter-hpc-native` app and submit it using the **JSON** form. The entire request is three lines, because the app already knows the system, queue, and setup script:

```json
{ "name": "jupyter", "appId": "jupyter-hpc-native", "appVersion": "vista" }
```

Click **Submit**.

> **Optional:** add `"maxMinutes": 60` to limit the session to 60 minutes (the default is 120). A shorter limit uses less allocation time.

> **Note:** the JSON box wants a *job request*, not the app definition. The one field people forget is `name` (the job name). If you see `required key [name] not found`, that is what is missing.

---

## Step 2: Watch the Status

The job moves through these stages:

```
STAGING_INPUTS  ->  SUBMITTING_JOB  ->  QUEUED  ->  RUNNING
```

Open the job's detail view and read the **`status`** line. On the `gh-dev` queue the wait is usually short.

> **Do not click into the output or files view yet.** It will show a "path not found" error simply because the output folder does not exist until the job runs. That error is harmless and expected.

If the job sticks on `STAGING_INPUTS` and never moves, see [Troubleshooting](troubleshooting.html). It almost always means the TMS key check above was skipped.

---

## Step 3: Get Your Jupyter URL

Once the status reads **RUNNING**, wait about one to two minutes for the compute node to boot the Jupyter server. Then open the job's output file **`tapisjob.out`**.

Near the top you will find a line like this:

```
TACC: JUPYTER_URL is https://vista.tacc.utexas.edu:60707/?token=cb137c8911dfce...
```

**Copy that entire URL** (including the `?token=...` part) into your browser. That is your JupyterLab session, running on a Vista GPU node.

> **Ignore the other URLs in the log.** Lines ending in `...vista.tacc.utexas.edu:8888` and `127.0.0.1:8888` are the node's internal addresses and will not work from your laptop. Only the `vista.tacc.utexas.edu:<port>` line with the token works. A `curl: (3)` line in the log is harmless noise.

---

## Step 4: Work in Your Notebook

You are now in JupyterLab on a real GPU node.

- Your notebook serves from your Vista **`$WORK`** directory. Anything you save there persists after the session ends.
- Confirm the GPU by running this in a notebook cell:
  ```python
  !nvidia-smi
  ```
  You should see a Grace-Hopper GPU.

---

## Step 5: When You're Done

- The session ends automatically when `maxMinutes` runs out. **Save your work to `$WORK` before then.**
- To stop early and free up allocation time, **cancel the job** in the Tapis UI.

---

## Quick Reference

| Task | What to do |
|------|-----------|
| Verify you're ready | Browse `cloud.data` for `tap-ilogin.sh` |
| Submit | `{ "name": "jupyter", "appId": "jupyter-hpc-native", "appVersion": "vista" }` |
| Limit session time | Add `"maxMinutes": 60` to the JSON |
| Check progress | Read the job's `status` line (wait for `RUNNING`) |
| Get the URL | Open `tapisjob.out`, copy the `JUPYTER_URL` line |
| Confirm GPU | Run `!nvidia-smi` in a cell |
| Stop early | Cancel the job in the Tapis UI |

---

## Getting Help

- **Stuck on a step?** See [Troubleshooting](troubleshooting.html).
- **TACC Documentation:** [docs.tacc.utexas.edu](https://docs.tacc.utexas.edu/)
- **MSF Questions:** [ashley.scruse@morehouse.edu](mailto:ashley.scruse@morehouse.edu)
