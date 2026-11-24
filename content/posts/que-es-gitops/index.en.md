---
title: "What is GitOps? The case of the disappearing replica"
slug: "what-is-gitops"
date: 2026-11-24
description: "Why a Kubernetes Deployment can return to three replicas after you scale it to four."
tags: ["GitOps", "Kubernetes"]
---

You scale a Kubernetes Deployment from three replicas to four. The fourth pod appears, but a few minutes later you're back to three. What undid the change?

## Two states and a controller

In GitOps, the manifest in Git describes the **desired state**. The cluster shows the **actual state**. If Argo CD manages the Deployment and self-healing is enabled, it sees that Git calls for three replicas while the cluster has four. Reconciling the two means restoring the value from Git.

## How to check

Compare `spec.replicas` in the manifest with the live Deployment, then check the application's sync history in Argo CD. Pod count alone doesn't prove GitOps was involved: a *HorizontalPodAutoscaler* can also change the replica count.

If you want four fixed replicas and Git manages that field, update the manifest in a pull request. After the sync, four replicas are no longer a deviation: they're what the controller works to maintain.
