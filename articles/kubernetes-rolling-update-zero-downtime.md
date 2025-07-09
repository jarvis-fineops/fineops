# 🚀 Deploying to Kubernetes Without Taking Down Prod

Kubernetes gives you rolling updates, but don’t assume it guarantees uptime. It’s more like—here’s the sharp knife, use it wisely.

We’ve seen folks deploy what looks like a safe change, only to get alert spam minutes later. Because K8s happily routed traffic to pods that weren’t ready, or killed too many at once.

This isn’t about being fancy. It’s about knowing which switches matter when prod is on the line.

---

## ✅ Step 1: Add Readiness & Liveness Probes (Proper Ones)

**Don’t skip these.** They’re the only things standing between a graceful deploy and a mid-rollout meltdown.

- **Readiness probe** → tells Kubernetes when the pod can handle traffic
- **Liveness probe** → tells Kubernetes when the pod needs to be restarted

Example:
```yaml
readinessProbe:
  httpGet:
    path: /ready
    port: 8080
  initialDelaySeconds: 3
  periodSeconds: 5
```

If you’re not using these, K8s is just guessing when to let your app start taking traffic.

---

## ⚙️ Step 2: Tune Your Rollout Strategy

Kubernetes will try to keep your app alive—but you’ve gotta tell it how.

```yaml
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxSurge: 1
    maxUnavailable: 0
```

This setup means:
- It’ll add 1 extra pod during the rollout
- It won’t take any old pods offline until a new one is ready

For apps with 2–3 replicas, this is critical. You need all your pods alive until the new ones are 100% good.

---

## 📊 Step 3: Watch Your Metrics (Seriously)

Don’t just rely on K8s saying “Rollout Complete.” Look at your actual app:

- Are 5xx errors spiking?
- Is response time climbing?
- Are any pods restarting unexpectedly?

Use whatever tools you’ve got—Prometheus, Datadog, Grafana—but keep an eye during deploys.

---

## 🐢 Step 4: Go Slower If You Need To

If this update feels risky, throttle it. You don’t need to blast 100% of traffic at your new version immediately.

Options:
- Lower `maxSurge` to 0
- Use Argo Rollouts or Flagger for canary-style deploys
- Roll out in chunks by node pool or zone

It’s easier to catch bugs at 10% traffic than at full blast.

---

## 🚫 Things That Break Rollouts

- No readiness probe — users hit a cold booting pod
- Over-aggressive surge/unavailable settings
- Monitoring turned off during deploys
- Fixes made in staging that never made it to prod YAML

---


## TL;DR

Kubernetes gives you the knobs—you just have to turn the right ones.

- Set up probes that actually reflect your app’s health
- Use rollout settings that keep your pods alive
- Watch your metrics while rolling out
- Don’t rush it if it’s risky

Shipping to prod doesn’t have to be scary. But it *can* be if you ignore the basics.
