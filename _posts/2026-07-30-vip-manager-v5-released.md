---
layout: post
title: >
  vip-manager v5 is out: what you need to know
date: 2026-07-30 10:00:00
description: >
  vip-manager v5 introduces safer VIP behavior and configuration changes. Read this before you upgrade in production.
tags:
  - personal
  - postgresql
  - what's new
---

High availability setups are never "set and forget". Every new release of your tooling can change how your cluster behaves at 3 a.m. when something breaks.

vip-manager v5 is one of those releases you **really** want to read about before just hitting `apt upgrade`. In this post I'll walk through the important breaking changes, what they mean in practice, and what you should do before rolling this out on production.

---

## Quick reminder: what vip-manager does

vip-manager is the small helper that manages a **Virtual IP (VIP)** in front of your PostgreSQL primary:

- It watches the Distributed Configuration Store (DCS) / leader info (patroni, etcd, Consul, ZooKeeper, etc.).
- When a node becomes leader, vip-manager attaches the VIP to it.
- When it loses leadership, vip-manager removes the VIP.

Clients connect to the VIP, not to the individual node. If the VIP is wrong or stale, your applications hit the wrong PostgreSQL instance. That's why these behavior changes matter.

---

## Breaking change #1: configuration refactor

The configuration handling in vip-manager v5 has been refactored and **deprecated parameters have been removed**.

In other words: if you still rely on old, deprecated keys in `vip-manager.yml`, v5 may **fail to start** or behave differently than you expect.

### What you should do

Before upgrading:

1. **Open your config**  
   Check your current `vip-manager.yml` on all nodes where vip-manager runs.

2. **Compare against current documentation**  
   Use the v5 docs or sample config and line them up with your file.

3. **Remove deprecated parameters**  
   Any parameter that is no longer recognized must go. Do not assume "it will just ignore it" — future refactors often get stricter.

4. **Rename to current keys**  
   Where keys have been renamed, update to the new names instead of keeping legacy aliases.

If you keep your configs in Git (you should), this is a good moment to commit a clean, documented `vip-manager.yml` so that the diff to v4 is obvious for your future self.

---

## Breaking change #2: VIP is removed when DCS is unreachable

This is the big behavioral change.

### Old behavior

- If the **DCS became unreachable**, vip-manager would **keep the VIP** on the current node.
- The logic was "better to keep serving traffic than to drop everything because of a temporary etcd/Consul hiccup".

Sounds comfortable, right? Unfortunately, this can help create **split-brain** situations.

### New behavior in v5

- When the **DCS becomes unreachable**, vip-manager now **automatically removes the VIP**.
- It will not pretend "everything is fine" when it has no idea who the leader is.

This is safer from a **consistency** perspective:

- If your DCS is down, vip-manager refuses to guess.
- That means fewer chances to have two nodes thinking they are primary and both serving writes.

But it also means:

- **Temporary DCS outages -> VIP loss.**

### Impact on your environment

You will see:

- More **failover-like events** during DCS issues (VIP dropping).
- Possible **short downtime** for applications even if PostgreSQL itself is healthy.
- A stricter coupling between DCS health and service availability.

This is not a bug; it is a conscious choice for correctness. But you must **teach your monitoring and on-call procedures** about this new truth.

---

## Fix: Stale VIP state after canceled etcd watch

This is not a new breaking behavior, but it changes how vip-manager recovers from certain edge cases.

### Old behavior

- After an **etcd watch was canceled**, vip-manager could end up with a **stale VIP state**.
- Practically, that could mean:
  - VIP still attached when it shouldn't be, or
  - VIP not updated after leadership changes.

In HA terms, "stale VIP" is one of the worst problems you can have.

### New behavior in v5

- After etcd watch cancellations, vip-manager now **properly re-synchronizes** its view of leadership.
- VIP state is updated to match the **real** leadership state in etcd.

So v5:

- Reduces the risk of inconsistent VIP assignments after transient etcd issues.
- Makes recovery from DCS/watch glitches more predictable.

If you ever had "phantom primaries" just because etcd had a bad day, this fix is for you.

---

## Recommended actions before upgrading to v5

Let's put all of this into a short, practical checklist you can follow.

### 1. Test in a non-production environment

I know, I know, everyone says that. But this time it's **really** not optional:

- Recreate your topology (at least 2–3 nodes + DCS).
- Deploy vip-manager v5 with your **updated** `vip-manager.yml`.
- Simulate:
  - DCS outages (block patroni/etcd/Consul/ZooKeeper, restart it).
  - Leader changes (trigger failover).
- Watch:
  - How quickly the VIP moves.
  - When the VIP is dropped.
  - Whether your apps handle the new behavior.

If something surprises you in staging, it will hurt much more in production.

### 2. Update monitoring and alerting

Your monitoring must now understand that:

- **VIP removal is expected** when DCS is unreachable.
- DCS health is now **directly tied to service availability**.

Concretely:

- Add/adjust alerts for:
  - DCS connectivity problems.
  - VIP missing on all nodes (vs. expected primary).
- Update runbooks:
  - Document that VIP removal during a DCS outage is **normal** for v5.
  - Explain what operators should check first (DCS, network, etc).

Without this, your on-call person will stare at "database down" while PostgreSQL happily serves traffic on localhost, just without VIP.

### 3. Review and update your configuration file

As mentioned in the first breaking change:

- Go through `vip-manager.yml`.
- Remove deprecated keys.
- Align with the **v5 schema**.

This is also a good moment to:

- Clean comments and outdated notes.
- Make sure your config is consistent across all nodes.
- Maybe introduce a **single source of truth** (Ansible, Puppet, etc.) if you still copy files by hand.

### 4. Re-evaluate your failover procedures

Because VIP behavior changed under DCS outages, your failover and incident procedures should be updated:

- When DCS dies:
  - Expect the VIP to be removed.
  - Don't waste time debugging PostgreSQL first.
- When you see a VIP disappear:
  - Check DCS health and connectivity.
- Make sure your team knows:
  - This is the **new normal** with v5.
  - Manual overrides (forcing VIP somewhere) are dangerous and should be well-documented and rare.

If you have automatic failover tooling on top (Patroni, custom scripts), verify how it behaves when vip-manager aggressively drops the VIP on DCS issues.

---

## Takeaways

vip-manager v5 is not a "cosmetic" release:

- **Configuration changed** — deprecated parameters are gone, you must update `vip-manager.yml`.
- **VIP behavior is stricter** — when DCS is unreachable, VIP is removed to avoid split-brain.
- **Stale VIP situations are improved** — better synchronization after etcd watch cancellations.

You get a **safer, more predictable HA layer**, but only if you:

- Test v5 in a safe environment first,
- Update configs and monitoring,
- And adjust procedures for the new DCS–VIP relationship.

If you do that preparation, vip-manager v5 will be a solid step forward for your PostgreSQL high availability setup, not a surprise during the next outage.

Bye-bye and see you soon! 👋💙💛
