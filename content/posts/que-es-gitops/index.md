---
title: "Qué es GitOps: el caso de la réplica que vuelve"
date: 2026-11-24
description: "Por qué un Deployment vuelve a tres réplicas después de haberlo escalado a cuatro."
tags: ["GitOps", "Kubernetes"]
---

Imagina que un Deployment de Kubernetes tiene tres réplicas. Lo escalas a cuatro desde la terminal, compruebas que aparece el cuarto pod y, al rato, vuelves a ver tres. ¿Quién deshizo el cambio?

## Dos estados, un controlador

En un flujo GitOps, el manifiesto versionado describe el **estado deseado**. El clúster muestra el **estado real**. Si Argo CD gestiona ese Deployment y tiene activada la autocorrección (*self-heal*), detecta que Git pide tres réplicas y el clúster tiene cuatro. Al reconciliar ambos estados, vuelve a aplicar el valor de Git.

## Cómo comprobarlo

Primero compara `spec.replicas` en el manifiesto con el valor del Deployment. Después revisa el historial de sincronización de la aplicación en Argo CD. El número de pods por sí solo no demuestra que haya intervenido GitOps: un *HorizontalPodAutoscaler* también puede cambiar las réplicas.

Si quieres mantener cuatro réplicas fijas y Git gestiona ese campo, cambia el manifiesto con un *pull request*. Tras sincronizar, las cuatro réplicas ya no son una desviación: son el estado que el controlador intenta conservar.
