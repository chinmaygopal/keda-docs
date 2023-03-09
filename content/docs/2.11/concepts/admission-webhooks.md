+++
title = "Admission Webhooks"
description = "Automatically validate resource changes to prevent misconfiguration and enforce best practices"
weight = 600
+++

> 💡 The Admission Webhooks are an opt-in feature and will become an opt-out feature as of KEDA v2.12.

There are some several misconfiguration scenarios that can produce scaling problems in productive workloads, for example: in Kubernetes a single workload should never be scaled by 2 or more HPA because that will produce conflicts and unintended behaviors.

Some errors with data format can be detected during the model validation, but these misconfigurations can't be detected in that step because the model is correct indeed. For trying to avoid those misconfigurations at data plane detecting them early, admission webhooks validate all the incoming (KEDA) resources (new or updated) and reject any resource that doesn't match the rules below.

### Prevention Rules

KEDA will block all incoming changes to `ScaledObject` that don't match these rules:

- The scaled workload (`scaledobject.spec.scaleTargetRef`) is already autoscaled by another other sources (other ScaledObject or HPA).
- CPU and/or Memory trigger are used and the scaled workload doesn't have the requests defined. **This rule doesn't apply to all the workload types, only to `Deployment` and `StatefulSet`.**