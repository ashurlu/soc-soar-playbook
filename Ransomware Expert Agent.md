# Node 5 — Ransomware Expert Agent

**Node Name:** Ransomware Expert Agent
**Node Type:** OpenAI — Message a Model
**Credential:** n8n Connect (no API key required)
**Resource:** Text
**Operation:** Message a Model
**Model:** GPT-5.1
**Simplify Output:** ON
**Input:** 1 item (from TRUE branch of IF node)\

<img width="1707" height="622" alt="image" src="https://github.com/user-attachments/assets/2e109b53-4d5f-41ff-b8c2-2c2a95e757d0" />

## Inside

<img width="1867" height="806" alt="image" src="https://github.com/user-attachments/assets/276eb6fd-ec3c-4bee-86af-abf33baa4387" />

## What This Node Does
This node acts as a Senior Incident Responder specialising in ransomware.
It receives the ransomware alert that was routed from the IF node and performs
a deep forensic analysis — going far beyond the Orchestrator's initial triage.

It identifies the ransomware family, maps out the full scope of the attack,
determines the attack stage in the kill chain, analyses the C2 infrastructure,
extracts file hashes for threat intelligence verification, and produces a
prioritised and ordered list of containment and eradication steps that a real
IR team would follow.

## Why We Use It
The Orchestrator is a generalist — it handles any alert type but goes only
one level deep. The Ransomware Expert Agent has deep, specialised knowledge
of ransomware behaviour, MITRE ATT&CK techniques, IR procedures, and forensic
triage. This mirrors how a real SOC operates: Tier 1 triages, Tier 2/3 owns
the deep investigation. Using a specialist agent produces far more actionable
and accurate output than asking a generalist to do both jobs.

## Role in the Workflow
- Receives: 1 item (ransomware alert from TRUE branch)
- Outputs: Structured JSON with ransomware family, affected systems,
  encryption scope, C2 details, hashes to check, containment steps,
  eradication steps
- The hashes_to_check field feeds directly into Node 6 (VirusTotal Hash Check)

## Configuration

| Field | Value |
|-------|-------|
| Node Name | Ransomware Expert Agent |
| Node Type | OpenAI — Message a Model |
| Credential | n8n Connect |
| Resource | Text |
| Operation | Message a Model |
| Model | GPT-5.1 |
| Simplify Output | ON |

## Message 1 — System Prompt
