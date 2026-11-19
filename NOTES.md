# Project notes — Gardener Playground

> Hand-off notes so a fresh Claude Code session (opened in this folder) can continue
> seamlessly. Read this first.

## What this is
An interactive, **single-page** web app (`index.html`) that teaches newcomers how
**Gardener** (Kubernetes-as-a-Service) works, as a *living diagram*. Conceptual model,
not real Kubernetes — an in-memory simulation where "controllers" are `setTimeout`-driven
JS functions. No backend, no build step, works offline via `file://`.

Built with the user (a Gardener contributor). **Talk to the user in Bulgarian** (their
repo convention); the **app UI itself is in English** (as of the latest change).

## Where things are
- `index.html` — the whole app (HTML + CSS + JS in one file).
- `fonts/` — local woff2 (JetBrains Mono for headings/technical labels, Inter for body),
  cyrillic+latin subsets, referenced with relative paths so it works offline.
- `docs/scenarios-stage1-design.md` — design doc for the scenarios feature.
- `README.md`, `LICENSE` (Apache-2.0), `.gitignore`.
- **GitHub repo (public):** https://github.com/DobromirNPeev/gardener-playground
  (personal account `DobromirNPeev`, remote `origin`, branch `main`).

## Layout (three columns, left → right = control flow)
- **Garden** (left): `gardener-apiserver` (+ its `etcd`), `gardener-scheduler`,
  `gardener-controller-manager`, `extensions`. gardenlet is NOT here.
- **Seed** (middle): hosts shoot control planes as pods. Each seed runs a `gardenlet`;
  each scheduled shoot's control plane = `kube-apiserver`, `etcd`, `kube-controller-manager`,
  `kube-scheduler`, `machine-controller-manager`.
- **Shoot** (right): separate box per shoot (worker-node side) with an arrow to its seed.
  Per-node `kubelet` + `kube-proxy` (in 2 worker-node boxes); cluster-wide `CoreDNS`.

## Features that work
- Shoot lifecycle: Pending → Scheduling → Running; Hibernate/Wake; Reconcile (animated
  walk through CP components; can FAIL → Degraded if the provider extension is down).
- Scheduler picks a seed by provider+region match; unmatched → stays Pending with reason.
- Fault injection: disable a seed (→ Blocked), remove `aws` extension (existing aws shoots
  become **Degraded** with `ControlPlaneHealthy=False` on next reconcile, not killed).
- Click any component → side-panel explanation (text + "what it talks to").
- **Scenarios menu** (top of left rail): Sandbox / Healthy landscape / Unschedulable /
  Seed outage / Degraded-from-extension. Declarative `SCENARIOS` array + `applyScenario(id)`
  that resets and instantiates shoots directly in their target phase.

## Conceptual decisions (verified against real Gardener docs — keep these correct)
- gardenlet lives in the SEED, not the garden.
- API Server is the access point / source of truth, but DATA is stored in **etcd** behind it
  (both garden and shoot control planes have their own etcd).
- kube-proxy = DaemonSet (per-node); CoreDNS = cluster-wide Deployment (not per-node).
- Extension failure → shoot `status.conditions` `ControlPlaneHealthy=False` → Degraded
  (pods keep running; not immediate death). This mirrors shoot-health-status-conditions.md.

## Current state / uncommitted work
As of this note, these are **done but NOT yet committed** (user wanted to review first):
1. Scenarios feature (menu + SCENARIOS + applyScenario).
2. Typography pass: JetBrains Mono (headings + technical labels) + Inter (body), local fonts.
3. Full UI translation BG → English (`lang="en"`, `en-GB` log timestamps). 0 cyrillic left.

The only commit so far is the initial one (`d8b4744`). `git status` shows `index.html`
modified plus untracked `docs/` and `fonts/`. Next step was to commit all of the above in
one commit once the user approves — ask the user before committing.

## How to verify a change didn't break the JS
```
cd ~/SAPDevelop/gardener-playground && grep -oPz '(?s)<script>\K.*?(?=</script>)' index.html | tr -d '\0' > /tmp/gp-check.js && node --check /tmp/gp-check.js
```
Open with: `open index.html` (macOS).

## Likely next steps (not started)
- Scenario stages 2+: guided walkthroughs (step Next/Back), alternate topologies
  (ManagedSeed, workerless, multi-region), per-extension flows
  (Infrastructure/ControlPlane/Worker/OSC/DNSRecord/etc. visualized step-by-step).
- Optional: GitHub Pages hosting; repo topics; prune now-unused font subsets.
