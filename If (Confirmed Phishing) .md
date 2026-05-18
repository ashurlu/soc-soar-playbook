# Node 11 — IF: Confirmed Phishing?

**Node Name:** Confirmed Phishing  
**Node Type:** IF

<img width="1557" height="578" alt="image" src="https://github.com/user-attachments/assets/6f5ac90d-427d-44bd-9d0e-2061d1c19618" />

## Inside 

<img width="1873" height="796" alt="image" src="https://github.com/user-attachments/assets/ec07efa0-0287-43c2-a6bb-5b385a131b66" />

## Condition

| Field | Value |
|-------|-------|
| Value 1 | `{{ $json.output[0].content[0].text }}` |
| Operator | contains |
| Value 2 | `"verdict": "phishing"` |

## Branches

| Branch | Condition | Goes To |
|--------|-----------|---------|
| TRUE | verdict contains "phishing" | Gmail — Phishing Escalation |
| FALSE | verdict is "legitimate" or "suspicious" | Not Confirmed Phishing (Set node) |

## Result from screenshots
- Branch taken: TRUE (verdict was "phishing", confidence 97%)
- 1 item sent to Gmail — Phishing Escalation

## Output

**True Branch**

```JSON
[
  {
    "output": [
      {
        "id": "msg_014cf1a151cfec33006a0a61a2defc819d99e34dd0897e1314",
        "type": "message",
        "status": "completed",
        "content": [
          {
            "type": "output_text",
            "annotations": [],
            "logprobs": [],
            "text": "{\n  \"alert_id\": \"ALT-2026-001\",\n  \"verdict\": \"phishing\",\n  \"confidence\": 98,\n  \"phishing_type\": \"credential_harvesting\",\n  \"sender_analysis\": {\n    \"domain_legitimate\": false,\n    \"lookalike_of\": \"corporate-help\",\n    \"spf_pass\": false,\n    \"dkim_pass\": false\n  },\n  \"url_analysis\": [\n    {\n      \"url\": \"http://secure-login.totallylegit.xyz/verify?user=john.smith\",\n      \"suspicious\": true,\n      \"reason\": \"Off-domain link with deceptive subdomain (secure-login) on low-reputation .xyz TLD, uses HTTP (no TLS), and a 'verify' path indicative of credential harvesting.\"\n    }\n  ],\n  \"social_engineering_techniques\": [\"urgency\", \"authority_impersonation\", \"fear\", \"credential_harvest_pretext\"],\n  \"analyst_verdict_explanation\": \"The sender domain c0rporate-help.xyz is a typosquat (0 for o) and fails SPF with no DKIM, inconsistent with legitimate corporate IT communications. The email uses urgent account-suspension threats to push the user to a non-corporate HTTP link designed to collect credentials.\",\n  \"recommended_action\": \"block_sender AND quarantine_email AND warn_recipient\",\n  \"escalate\": true,\n  \"original_alert\": {\n    \"alert_id\": \"ALT-2026-001\",\n    \"classification\": \"phishing\",\n    \"severity\": \"high\",\n    \"confidence\": 97,\n    \"analyst_summary\": \"This alert is a phishing email impersonating IT security and attempting to trick the recipient into clicking a credential-harvesting link to 'verify' their account under a false suspension threat. The suspicious sender domain, SPF fail, lack of DKIM, and urgent account suspension lure strongly indicate a phishing attempt.\",\n    \"recommended_action\": \"Block the sender domain and originating IP at the email gateway, add the destination URL/domain to web filtering blocklists, search for and remove this email from all user mailboxes, and contact the recipient to ensure they did not click the link or enter credentials; if they did, initiate credential reset and further incident response.\",\n    \"iocs\": {\n      \"ips\": [\n        \"203.0.113.45\"\n      ],\n      \"domains\": [\n        \"c0rporate-help.xyz\",\n        \"secure-login.totallylegit.xyz\",\n        \"totallylegit.xyz\"\n      ],\n      \"hashes\": [],\n      \"urls\": [\n        \"http://secure-login.totallylegit.xyz/verify?user=john.smith\"\n      ]\n    },\n    \"original_alert\": {\n      \"alert_id\": \"ALT-2026-001\",\n      \"alert_type\": \"email_security\",\n      \"severity\": \"high\",\n      \"timestamp\": \"2026-05-04T09:14:22Z\",\n      \"source_system\": \"Wazuh\",\n      \"rule_id\": \"100601\",\n      \"rule_level\": 12,\n      \"agent_name\": \"WIN-WORKSTATION-07\",\n      \"agent_ip\": \"10.10.10.45\",\n      \"email\": {\n        \"sender\": \"it-security@c0rporate-help.xyz\",\n        \"sender_ip\": \"203.0.113.45\",\n        \"recipient\": \"john.smith@company.com\",\n        \"subject\": \"URGENT: Your account will be suspended in 24 hours\",\n        \"body\": \"Dear Employee,\\n\\nOur security system has detected unusual activity on your account. Your account will be suspended within 24 hours unless you verify your credentials immediately.\\n\\nPlease click the link below to verify your identity:\\nhttp://secure-login.totallylegit.xyz/verify?user=john.smith\\n\\nFailure to verify will result in permanent account suspension.\\n\\nIT Security Department\",\n        \"urls\": [\n          \"http://secure-login.totallylegit.xyz/verify?user=john.smith\"\n        ],\n        \"attachment\": null,\n        \"headers\": {\n          \"return_path\": \"bounce@c0rporate-help.xyz\",\n          \"x_mailer\": \"PHPMailer 6.1.4\",\n          \"spf\": \"fail\",\n          \"dkim\": \"none\"\n        }\n      }\n    }\n  }\n}"
          }
        ],
        "role": "assistant"
      }
    ]
  }
]
```
**False Branch**  
Nothing
