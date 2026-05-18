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
<img width="471" height="324" alt="image" src="https://github.com/user-attachments/assets/b9071c43-079d-47be-ac30-2298fe13b992" />


```Json
You are a Senior Ransomware Incident Response specialist with 10+ years of experience handling ransomware attacks. You are part of an automated SOC response pipeline.

You will receive a ransomware alert that has already been triaged by a junior analyst. Your job is to perform deep technical analysis and produce a detailed incident assessment.

Respond ONLY with a valid JSON object in this exact format:

{
  "alert_id": "<from input>",
  "ransomware_family": "<identified or suspected ransomware family, or 'Unknown'>",
  "attack_stage": "<initial_access OR execution OR persistence OR lateral_movement OR impact>",
  "containment_priority": "<immediate OR urgent OR standard>",
  "affected_systems": ["<list of affected hostnames or IPs>"],
  "encryption_scope": "<brief description of what was encrypted>",
  "c2_infrastructure": {
    "ip": "<C2 IP if present>",
    "port": "<C2 port if present>",
    "protocol": "<suspected protocol>"
  },
  "hashes_to_check": ["<all file hashes that must be checked against VirusTotal>"],
  "containment_steps": [
    "<step 1>",
    "<step 2>",
    "<step 3>",
    "<step 4>"
  ],
  "eradication_steps": [
    "<step 1>",
    "<step 2>"
  ],
  "escalation_required": true,
  "escalation_reason": "<why immediate escalation is needed>",
  "incident_severity": "P1",
  "original_alert": <copy the entire original alert JSON here>
}
```


## Message 2 — System Prompt

<img width="482" height="472" alt="image" src="https://github.com/user-attachments/assets/3a7ae32c-2323-4e73-a501-cc4fd57673ac" />


```json
Perform deep ransomware incident analysis on this alert:

{{ JSON.stringify($json) }}
```

##  Output
```JSON
[
  {
    "output": [
      {
        "id": "msg_0f49991f0194db70006a0a617ead6c819c904790f4b0a39c0e",
        "type": "message",
        "status": "completed",
        "content": [
          {
            "type": "output_text",
            "annotations": [],
            "logprobs": [],
            "text": "{\n  \"alert_id\": \"ALT-2026-002\",\n  \"ransomware_family\": \"Unknown\",\n  \"attack_stage\": \"impact\",\n  \"containment_priority\": \"immediate\",\n  \"affected_systems\": [\n    \"WIN-SERVER-03\",\n    \"10.10.10.22\"\n  ],\n  \"encryption_scope\": \"At least 847 files encrypted on WIN-SERVER-03 (10.10.10.22), including contents of shared folders \\\\\\\\10.10.10.22\\\\Finance and \\\\\\\\10.10.10.22\\\\HR with .locked extension and ransom note READ_ME_NOW.txt on user desktop.\",\n  \"c2_infrastructure\": {\n    \"ip\": \"185.208.156.57\",\n    \"port\": \"4444\",\n    \"protocol\": \"likely TCP (possible custom or reverse-shell style C2)\"\n  },\n  \"hashes_to_check\": [\n    \"e99a18c428cb38d5f260853678922e03\",\n    \"6b86b273ff34fce19d6b804eff5a3f5747ada4eaa22f1d49c01e52ddb7875b4b\"\n  ],\n  \"containment_steps\": [\n    \"Immediately isolate WIN-SERVER-03 (10.10.10.22) from the network at the switch level (disable port) and/or via EDR network containment; do NOT power it off to preserve volatile evidence.\",\n    \"Block all outbound and inbound traffic to/from 185.208.156.57:4444 at perimeter firewalls, IPS, and EDR; add temporary block rules for the IP globally.\",\n    \"Disable or unpublish affected shares (\\\\\\\\10.10.10.22\\\\Finance and \\\\\\\\10.10.10.22\\\\HR) on WIN-SERVER-03 to prevent further encryption or access, and ensure no other hosts retain mapped drives to these shares.\",\n    \"Initiate enterprise-wide hunting for the encrypt.exe indicators (path C:\\\\Users\\\\jsmith\\\\AppData\\\\Local\\\\Temp\\\\encrypt.exe, hashes provided, .locked extension, READ_ME_NOW.txt) and isolate any additional impacted hosts discovered.\"\n  ],\n  \"eradication_steps\": [\n    \"On WIN-SERVER-03 and any additional affected hosts, terminate and remove the encrypt.exe binary and any associated persistence (scheduled tasks, services, startup items), then perform a full antimalware/EDR sweep using updated signatures.\",\n    \"Restore encrypted data on WIN-SERVER-03 and impacted shares (Finance and HR) from known-good, pre-incident backups after verifying backups are clean, and apply security hardening (patching, credential resets, access review) before returning systems to production.\"\n  ],\n  \"escalation_required\": true,\n  \"escalation_reason\": \"Active ransomware encryption with confirmed impact to a critical server (WIN-SERVER-03), extensive file encryption (847+ files), affected business-critical Finance and HR shares, evidence of lateral movement, and active C2 communication to 185.208.156.57:4444 requires immediate engagement of the full incident response team and executive notification.\",\n  \"incident_severity\": \"P1\",\n  \"original_alert\": {\n    \"output\": [\n      {\n        \"id\": \"msg_0238d97d4bdfc2fd006a0a617ab3d481a1b20462b23ba629b0\",\n        \"type\": \"message\",\n        \"status\": \"completed\",\n        \"content\": [\n          {\n            \"type\": \"output_text\",\n            \"annotations\": [],\n            \"logprobs\": [],\n            \"text\": \"{\\n  \\\"alert_id\\\": \\\"ALT-2026-002\\\",\\n  \\\"classification\\\": \\\"ransomware\\\",\\n  \\\"severity\\\": \\\"critical\\\",\\n  \\\"confidence\\\": 100,\\n  \\\"analyst_summary\\\": \\\"Endpoint WIN-SERVER-03 is actively running a ransomware process encrypt.exe that has already encrypted hundreds of files, dropped a ransom note, and impacted shared folders, indicating an ongoing ransomware incident with lateral movement.\\\",\\n  \\\"recommended_action\\\": \\\"Immediately isolate WIN-SERVER-03 from the network, disable affected shares, block outbound traffic to 185.208.156.57:4444, preserve volatile evidence, and initiate the incident response and containment process for ransomware.\\\",\\n  \\\"iocs\\\": {\\n    \\\"ips\\\": [\\\"185.208.156.57\\\"],\\n    \\\"domains\\\": [],\\n    \\\"hashes\\\": [\\n      \\\"e99a18c428cb38d5f260853678922e03\\\",\\n      \\\"6b86b273ff34fce19d6b804eff5a3f5747ada4eaa22f1d49c01e52ddb7875b4b\\\"\\n    ],\\n    \\\"urls\\\": []\\n  },\\n  \\\"original_alert\\\": {\\n    \\\"alert_id\\\": \\\"ALT-2026-002\\\",\\n    \\\"alert_type\\\": \\\"endpoint_security\\\",\\n    \\\"severity\\\": \\\"critical\\\",\\n    \\\"timestamp\\\": \\\"2026-05-04T09:31:05Z\\\",\\n    \\\"source_system\\\": \\\"Wazuh\\\",\\n    \\\"rule_id\\\": \\\"100900\\\",\\n    \\\"rule_level\\\": 15,\\n    \\\"agent_name\\\": \\\"WIN-SERVER-03\\\",\\n    \\\"agent_ip\\\": \\\"10.10.10.22\\\",\\n    \\\"ransomware\\\": {\\n      \\\"process_name\\\": \\\"encrypt.exe\\\",\\n      \\\"process_path\\\": \\\"C:\\\\\\\\Users\\\\\\\\jsmith\\\\\\\\AppData\\\\\\\\Local\\\\\\\\Temp\\\\\\\\encrypt.exe\\\",\\n      \\\"file_hash_md5\\\": \\\"e99a18c428cb38d5f260853678922e03\\\",\\n      \\\"file_hash_sha256\\\": \\\"6b86b273ff34fce19d6b804eff5a3f5747ada4eaa22f1d49c01e52ddb7875b4b\\\",\\n      \\\"files_encrypted\\\": 847,\\n      \\\"extension_added\\\": \\\".locked\\\",\\n      \\\"ransom_note_path\\\": \\\"C:\\\\\\\\Users\\\\\\\\jsmith\\\\\\\\Desktop\\\\\\\\READ_ME_NOW.txt\\\",\\n      \\\"c2_ip\\\": \\\"185.208.156.57\\\",\\n      \\\"c2_port\\\": 4444,\\n      \\\"lateral_movement_detected\\\": true,\\n      \\\"affected_shares\\\": [\\n        \\\"\\\\\\\\\\\\\\\\10.10.10.22\\\\\\\\Finance\\\",\\n        \\\"\\\\\\\\\\\\\\\\10.10.10.22\\\\\\\\HR\\\"\\n      ]\\n    }\\n  }\\n}\"\n          }\n        ],\n        \"role\": \"assistant\"\n      }\n    ]\n  }\n}"
          }
        ],
        "role": "assistant"
      }
    ]
  }
]
```
