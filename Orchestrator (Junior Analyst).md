## Node 3 — Orchestrator (Junior Analyst)

**Node Name:** Orchestrator (Junior Analyst)  
**Node Type:** OpenAI — Message a Model  
**Credential:** n8n Connect (no API key required)  
**Resource:** Text  
**Operation:** Message a Model  
**Model:** GPT-5.1  
**Simplify Output:** ON  

<img width="1707" height="622" alt="image" src="https://github.com/user-attachments/assets/d34b3102-51cc-4cf1-b463-cf6fa4cf2cc7" />

## Inside of Node  

<img width="1864" height="799" alt="image" src="https://github.com/user-attachments/assets/fed0e7c3-3f26-4e87-8763-565f9481ccd3" />

## What This Node Does
This is the brain of the first triage stage. It acts as an AI-powered Junior
SOC Analyst. It receives each raw alert from the Code Node and performs an
initial classification — deciding what type of threat it is, extracting all
Indicators of Compromise (IOCs), writing a plain-English analyst summary, and
recommending an immediate action.

This node processes BOTH alerts (phishing and ransomware) because it sits
before the IF router. It outputs structured JSON for each alert, which the
IF node then uses to route to the correct specialist agent.

## Why We Use It
A real Tier-1 analyst spends 3–10 minutes per alert manually reading,
classifying, and extracting IOCs. This node does the same work in under
2 seconds, with consistent output format, no fatigue, and no missed fields.
It also attaches the entire original alert to its output so downstream nodes
never lose context.

## Role in the Workflow
- Receives: 2 items from the Code Node
- Outputs: 2 items with classification, IOCs, summary, and recommended action
- The classification field (ransomware / phishing) is what the IF node reads
  to route each alert to the correct specialist


## Message 1 — System Prompt

```
You are a SOC Tier 1 analyst working on a security alert queue. Your job is to review each incoming alert, classify it, and extract the key fields needed for the next stage of triage.  
```

<img width="487" height="343" alt="image" src="https://github.com/user-attachments/assets/9978524c-d4c5-40b5-a92b-6490feb2b081" />

## Message 2 — User Message

```json
Review this security alert and classify it:

{{ JSON.stringify($json) }}
```
<img width="467" height="353" alt="image" src="https://github.com/user-attachments/assets/f3b597b1-3a91-4de4-9717-24e3426c6fa1" />

## Output (from screenshots)
- Item 0 (phishing alert): classification: "phishing", confidence: 97
- Item 1 (ransomware alert): classification: "ransomware", confidence: 98
- Both items flow to the IF node

  ```json
  [
  {
    "output": [
      {
        "id": "msg_0c4f0dd63ea67df3006a0a6176308481a28c64928473dd5c2c",
        "type": "message",
        "status": "completed",
        "content": [
          {
            "type": "output_text",
            "annotations": [],
            "logprobs": [],
            "text": "{\n  \"alert_id\": \"ALT-2026-001\",\n  \"classification\": \"phishing\",\n  \"severity\": \"high\",\n  \"confidence\": 97,\n  \"analyst_summary\": \"This alert is a phishing email impersonating IT security and attempting to trick the recipient into clicking a credential-harvesting link to 'verify' their account under a false suspension threat. The suspicious sender domain, SPF fail, lack of DKIM, and urgent account suspension lure strongly indicate a phishing attempt.\",\n  \"recommended_action\": \"Block the sender domain and originating IP at the email gateway, add the destination URL/domain to web filtering blocklists, search for and remove this email from all user mailboxes, and contact the recipient to ensure they did not click the link or enter credentials; if they did, initiate credential reset and further incident response.\",\n  \"iocs\": {\n    \"ips\": [\"203.0.113.45\"],\n    \"domains\": [\"c0rporate-help.xyz\", \"secure-login.totallylegit.xyz\", \"totallylegit.xyz\"],\n    \"hashes\": [],\n    \"urls\": [\"http://secure-login.totallylegit.xyz/verify?user=john.smith\"]\n  },\n  \"original_alert\": {\n    \"alert_id\": \"ALT-2026-001\",\n    \"alert_type\": \"email_security\",\n    \"severity\": \"high\",\n    \"timestamp\": \"2026-05-04T09:14:22Z\",\n    \"source_system\": \"Wazuh\",\n    \"rule_id\": \"100601\",\n    \"rule_level\": 12,\n    \"agent_name\": \"WIN-WORKSTATION-07\",\n    \"agent_ip\": \"10.10.10.45\",\n    \"email\": {\n      \"sender\": \"it-security@c0rporate-help.xyz\",\n      \"sender_ip\": \"203.0.113.45\",\n      \"recipient\": \"john.smith@company.com\",\n      \"subject\": \"URGENT: Your account will be suspended in 24 hours\",\n      \"body\": \"Dear Employee,\\n\\nOur security system has detected unusual activity on your account. Your account will be suspended within 24 hours unless you verify your credentials immediately.\\n\\nPlease click the link below to verify your identity:\\nhttp://secure-login.totallylegit.xyz/verify?user=john.smith\\n\\nFailure to verify will result in permanent account suspension.\\n\\nIT Security Department\",\n      \"urls\": [\n        \"http://secure-login.totallylegit.xyz/verify?user=john.smith\"\n      ],\n      \"attachment\": null,\n      \"headers\": {\n        \"return_path\": \"bounce@c0rporate-help.xyz\",\n        \"x_mailer\": \"PHPMailer 6.1.4\",\n        \"spf\": \"fail\",\n        \"dkim\": \"none\"\n      }\n    }\n  }\n}"
          }
        ],
        "role": "assistant"
      }
    ]
  },
  {
    "output": [
      {
        "id": "msg_0238d97d4bdfc2fd006a0a617ab3d481a1b20462b23ba629b0",
        "type": "message",
        "status": "completed",
        "content": [
          {
            "type": "output_text",
            "annotations": [],
            "logprobs": [],
            "text": "{\n  \"alert_id\": \"ALT-2026-002\",\n  \"classification\": \"ransomware\",\n  \"severity\": \"critical\",\n  \"confidence\": 100,\n  \"analyst_summary\": \"Endpoint WIN-SERVER-03 is actively running a ransomware process encrypt.exe that has already encrypted hundreds of files, dropped a ransom note, and impacted shared folders, indicating an ongoing ransomware incident with lateral movement.\",\n  \"recommended_action\": \"Immediately isolate WIN-SERVER-03 from the network, disable affected shares, block outbound traffic to 185.208.156.57:4444, preserve volatile evidence, and initiate the incident response and containment process for ransomware.\",\n  \"iocs\": {\n    \"ips\": [\"185.208.156.57\"],\n    \"domains\": [],\n    \"hashes\": [\n      \"e99a18c428cb38d5f260853678922e03\",\n      \"6b86b273ff34fce19d6b804eff5a3f5747ada4eaa22f1d49c01e52ddb7875b4b\"\n    ],\n    \"urls\": []\n  },\n  \"original_alert\": {\n    \"alert_id\": \"ALT-2026-002\",\n    \"alert_type\": \"endpoint_security\",\n    \"severity\": \"critical\",\n    \"timestamp\": \"2026-05-04T09:31:05Z\",\n    \"source_system\": \"Wazuh\",\n    \"rule_id\": \"100900\",\n    \"rule_level\": 15,\n    \"agent_name\": \"WIN-SERVER-03\",\n    \"agent_ip\": \"10.10.10.22\",\n    \"ransomware\": {\n      \"process_name\": \"encrypt.exe\",\n      \"process_path\": \"C:\\\\Users\\\\jsmith\\\\AppData\\\\Local\\\\Temp\\\\encrypt.exe\",\n      \"file_hash_md5\": \"e99a18c428cb38d5f260853678922e03\",\n      \"file_hash_sha256\": \"6b86b273ff34fce19d6b804eff5a3f5747ada4eaa22f1d49c01e52ddb7875b4b\",\n      \"files_encrypted\": 847,\n      \"extension_added\": \".locked\",\n      \"ransom_note_path\": \"C:\\\\Users\\\\jsmith\\\\Desktop\\\\READ_ME_NOW.txt\",\n      \"c2_ip\": \"185.208.156.57\",\n      \"c2_port\": 4444,\n      \"lateral_movement_detected\": true,\n      \"affected_shares\": [\n        \"\\\\\\\\10.10.10.22\\\\Finance\",\n        \"\\\\\\\\10.10.10.22\\\\HR\"\n      ]\n    }\n  }\n}"
          }
        ],
        "role": "assistant"
      }
    ]
  }
]
```
