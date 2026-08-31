+++
title = "Metadata"
date = 2020-07-11T22:33:15+08:00
weight = 8
+++

## Reading Metadata

Starting from firmware version 2.0.0, CanoKey supports reading PIV metadata with the Get Metadata command (`INS F7`). The command takes a reference: PIN (`80`), PUK (`81`), the management key (`9B`), or a supported asymmetric-key slot. The response follows the Yubico PIV metadata TLV format and reports values such as the algorithm, the PIN and touch policies, the key's origin (generated or imported), the public key, whether the secret still has its default value, and the retry counters, where applicable.

Reading the metadata is the reliable way to determine the policies and algorithms in use on a device, since defaults differ between firmware versions.
