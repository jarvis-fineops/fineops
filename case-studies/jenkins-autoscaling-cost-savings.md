# Jenkins Autoscaling – Cutting CI Costs Without Slowing Everything Down

Okay, quick story.

We were working with a team that had a super spiky Jenkins workload — think 2 jobs at 11AM, 30+ by 4PM, and then nothing overnight. Problem was, their nodes were always "on", even when Jenkins was twiddling its thumbs.

So yeah... compute bills were bad. Also, scaling was completely manual. No one wants to SSH into the Jenkins controller just to spin up agents in 2025.

We decided to fix it. Here’s what we built — part of our [FineOps](../README.md) cloud savings kit, and helpful if you’re running builds in AWS and don’t want to cry every time the invoice hits your inbox.

---

## What was wrong?

- Jenkins agents were just... always there. Even when idle.  
- Admins were manually tweaking nodes — not fun, not fast.  
- Builds queued up during peak hours, eating up delivery timelines.  
- Company was growing and couldn't keep this up manually.

We’re a **DevOps shop**, so this kind of thing is kinda our jam.

---

## What we actually did

### 1. Spot Instances = Cheap + Fast (when they work)

We used **AWS spot instances** to scale Jenkins build agents. They spin up fast, cost a fraction of normal EC2, and honestly are perfect for short-lived jobs.

Yes, they get killed sometimes, but Jenkins doesn't care as long as we had enough spares in the pool.  

### 2. Metrics (because vibes aren’t a monitoring strategy)

Hooked up **Prometheus** to track:

- Queue depth
- Spot usage
- How often AWS killed our agents mid-build (spoiler: not that often)

Gave us a real sense of how scaling behaved under pressure.

### 3. Terraform + GitOps = sanity

Infra was defined using our own Terraform modules. GitOps pipelines made sure changes were versioned and reviewable. We didn’t want a "clickops" nightmare later.

---

## What changed?

- 💸 **CI costs dropped ~45%**  
  Mostly thanks to using spots + auto-killing idle agents.

- ⚡ **Jenkins got way faster**  
  New agents spun up just in time. Job queue length went way down.

- 📁 **We built a reusable setup**  
  You can check it out in [Jenkins CI Cost Cut](../jenkins-ci-cost-cut/README.md). Reuse or fork it if you want.

---

## Want to copy this?

We also put together a [Terraform Cost Audit Module](../terraform-cost-audit-module/README.md) if you want to catch other over-provisioned junk in your infra.

---

## Need help?

If you're stuck, or just want a second set of eyes —  
We do **DevOps sprints**, **cloud audits**, or full-on **infra builds**. No fluff.

📅 [Book a free consult](https://cal.com/fineops-demo) — no pressure.
