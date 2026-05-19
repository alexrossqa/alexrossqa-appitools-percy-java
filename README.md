# Visual Regression Testing — Applitools Eyes + Selenium (Java)

Demo project showing AI visual regression testing using [Applitools Eyes](https://applitools.com) with Selenium 4 and TestNG.

**Repository:** https://github.com/alexrossqa/alexrossqa-applitools-java

## Demo

[Watch on Vimeo](https://vimeo.com/1192265401)

## What it does

Two TestNG tests demonstrate the full visual regression workflow:

| Test | What it does |
|---|---|
| `loginPageBaseline` | Opens the demo login page and captures a snapshot. This becomes the approved reference image that all future runs compare against. |
| `loginPageRegression` | Injects a CSS change (turns the login button red, simulating a bad deploy) and captures a new snapshot. Applitools compares it against the approved baseline and flags the difference. |

## How to run

The tests must be run in order, with a manual approval step in between. This is how Applitools works — it needs a human to sign off on the baseline before it can judge future snapshots as pass or fail.

### Step 1 — Run the baseline test

```
mvn test -Dtest=loginPageBaseline
```

This sends a screenshot to your Applitools account. The snapshot will appear in the dashboard with the status **New**, meaning no approved baseline exists yet.

### Step 2 — Approve the baseline in the Applitools dashboard

1. Log in at [eyes.applitools.com](https://eyes.applitools.com)
2. Open the test run — you'll see the snapshot marked as **New**
3. Review it and click **Accept** (thumbs up)

This tells Applitools "this is the correct UI — use it as the reference for all future comparisons." Without this step, the regression test has nothing to compare against.

### Step 3 — Run the regression test

```
mvn test -Dtest=loginPageRegression
```

Applitools compares the new snapshot against the approved baseline. The injected CSS change is detected and the test is marked **Unresolved**, with the diff highlighted in the dashboard.

### Or run both at once

```
mvn test
```

If you run both tests together without approving in between, the regression test will also show as **New** rather than **Unresolved** — you won't see the diff flagged. Run them separately with the approval step to see the full workflow.

## Prerequisites

- Java 11+
- Maven
- Google Chrome
- An [Applitools Eyes](https://applitools.com) account (free tier available)

## Setup

### Applitools API key

Set as an environment variable — never hardcode it:

```powershell
$env:APPLITOOLS_API_KEY = "your_api_key_here"
```

Your API key is in the Applitools dashboard under **Profile → My API Key**.

## How the AI works

Applitools uses a Visual AI engine to compare screenshots — not pixel-by-pixel diffing. It understands what constitutes a meaningful visual change vs. irrelevant rendering noise (anti-aliasing, sub-pixel differences, font rendering). This makes it practical in CI pipelines where dumb pixel comparison generates constant false positives.
