+++
title = "PIN and Touch Policies"
date = 2020-07-11T22:33:15+08:00
weight = 3
+++

Each asymmetric key slot has a PIN policy and a touch policy, which together decide what the user must do before the key in that slot can be used.

## PIN Policy

* Never: Never verify PIN
* Always: Verify PIN for every use
* Once: Verify PIN once per session

## Touch Policy

* Never: Never require touch
* Always: Require touch for every use
* Cached: No touch required if touched within the last 15 seconds, otherwise touch is required

Touch requirements apply only over USB and are not enforced over NFC.

## Default Policies

{{% notice note %}}
Starting from firmware version 2.0.0, CanoKey supports configuring PIV PIN and touch policies.
{{% /notice %}}

| Key Slot | Default PIN Policy | Default Touch Policy |
|:---------|:-------------------|:---------------------|
| 9E       | Never              | Never                |
| Other slots | Once            | Never                |
