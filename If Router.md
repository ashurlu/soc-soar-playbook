# Node 4 — IF: Ransomware or Phishing?

**Node Name:** If  
**Node Type:** IF  
 
<img width="1707" height="622" alt="image" src="https://github.com/user-attachments/assets/6714d9e2-5484-4d5c-95da-93e875f875d1" />

## Inside 
<img width="1866" height="798" alt="image" src="https://github.com/user-attachments/assets/8dea49c0-d2b2-46de-b0f9-fae4a9eb035b" />


## What This Node Does
This is the traffic router of the workflow. It reads the Orchestrator's
classification output and decides which specialist agent should handle each
alert. Ransomware alerts go to the Ransomware Expert Agent. Everything else
(phishing, malware, unknown) goes to the Phishing Analyst Agent.

## Why We Use It
Different threat types require fundamentally different analysis techniques.
Ransomware analysis involves hash checking, C2 identification, lateral movement
scope, and containment isolation steps. Phishing analysis involves header
forensics, URL reputation, social engineering pattern recognition, and
credential harvesting detection. Sending both to the same agent would produce
shallow, generic analysis. The IF node ensures each alert gets the right expert.

## Why "Contains" Instead of "Equals"
The Orchestrator returns a full JSON string as text. Using "contains ransomware"
is more resilient than trying to parse and extract a specific field — it catches
the classification value regardless of exactly where it appears in the output
string.

## Configuration

| Field | Value |
|-------|-------|
| Node Name | If |
| Node Type | IF |

## Condition

| Field | Value |
|-------|-------|
| Value 1 | `{{ $json.output[0].content[0].text }}` |
| Operator | contains |
| Value 2 | `ransomware` |

## OUTPUT

**True Branch
```JSON
[
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

** False Branch
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
  }
]
```

## Result from Screenshots
- Input: 2 items (one phishing, one ransomware)
- TRUE branch received: 1 item (ransomware alert — ALT-2026-002)
- FALSE branch received: 1 item (phishing alert — ALT-2026-001)

> ⚠️ If everything routes to the same branch: open the Orchestrator output
> in the Executions tab and confirm the classification field equals "ransomware"
> for the ransomware alert. The Value 1 expression must correctly reference
> the Orchestrator's output text path.
