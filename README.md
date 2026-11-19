# Gardener Playground

An interactive, single-file web app for **onboarding into [Gardener](https://github.com/gardener/gardener)** —
the "Kubernetes Clusters as a Service" project.

It is a *living diagram* that lets a newcomer build intuition about how Gardener works:
how **garden**, **seed**, and **shoot** relate, how a shoot's control plane lives as pods
**inside a seed**, how the scheduler picks a seed, and what happens during the shoot
lifecycle (create → schedule → run → hibernate → reconcile).

> ⚠️ This is a **conceptual simulation**, not real Kubernetes. There is no backend and no
> real API. The "controllers" are small JavaScript functions on timers that advance an
> in-memory model so it behaves plausibly. It is a teaching aid — the real behaviour is
> defined by the actual Gardener code and docs.

## What you can do

- **Create a shoot** (pick provider / region / machine type) and watch it go
  `Pending → Scheduling → Running`.
- **See the scheduler choose a seed** by matching provider + region — and see a shoot
  stay `Pending` with a visible reason when no seed matches.
- **Hibernate / wake** a shoot and **trigger a reconcile**, watching the control-plane
  components get checked one by one.
- **Inject faults**: disable a seed, remove the `aws` extension, or request an invalid
  machine type — and see shoots block or become **Degraded** (with `status.conditions`
  like `ControlPlaneHealthy = False`, the same mechanism Gardener uses).
- **Click any component** (in the garden, in a seed, or on a shoot's worker node) to read
  a short plain-language explanation of what it does and what it talks to.
- **Load a scenario** from the dropdown at the top of the left panel to jump straight into
  a pre-built landscape: a healthy cluster, an unschedulable shoot, a seed outage, or a
  shoot degraded by a missing extension — no manual setup needed.

## The three columns

| Column | What it shows |
|--------|---------------|
| **Garden** (left) | The central cluster's components: `gardener-apiserver` (+ its `etcd`), `gardener-scheduler`, `gardener-controller-manager`, and `extensions`. |
| **Seed** (middle) | The clusters that **host shoot control planes**. Each seed runs a `gardenlet`; each scheduled shoot's control plane (`kube-apiserver`, `etcd`, `kube-controller-manager`, `kube-scheduler`, `machine-controller-manager`) runs here as pods. |
| **Shoot** (right) | The end-user clusters' own worker-node side: per-node `kubelet` + `kube-proxy`, and cluster-wide `CoreDNS`. An arrow links each shoot to the seed hosting its control plane. |

## Running it

No build step, no dependencies, no server. Just open the file:

```sh
open index.html      # macOS
# or double-click index.html in your file manager
```

It works offline — the Gardener logo is inlined and there are no external scripts.

## Learn more

The concepts here are drawn from the real Gardener documentation:

- [Gardener architecture](https://github.com/gardener/gardener/blob/master/docs/concepts/architecture.md)
- [`gardenlet`](https://github.com/gardener/gardener/blob/master/docs/concepts/gardenlet.md)
- [etcd](https://github.com/gardener/gardener/blob/master/docs/concepts/etcd.md)
- [Shoot health status conditions](https://github.com/gardener/gardener/blob/master/docs/extensions/shoot-health-status-conditions.md)

## License

[Apache-2.0](./LICENSE). Not an official Gardener project; the Gardener name and logo
belong to the Gardener project / SAP SE.
