# Node 10 — Phishing Analyst Agent

**Node Name:** Phishing Analyst Agent  
**Node Type:** OpenAI — Message a Model  
**Credential:** n8n Connect (no API key required)  
**Resource:** Text  
**Operation:** Message a Model  
**Model:** GPT-5  
**Simplify Output:** ON  
**Input:** 1 item (from FALSE branch of IF node)  

<img width="1557" height="578" alt="image" src="https://github.com/user-attachments/assets/3cccbb2b-6e4a-4f27-80c6-77673a3508fe" />

## Inside
<img width="1865" height="803" alt="image" src="https://github.com/user-attachments/assets/2e8acde8-7d53-433b-ba6e-6a5d49aa460d" />

## Message 1 — System Prompt


You are a Phishing Investigation Specialist. You analyze suspicious emails to determine if they are genuine phishing attempts or false positives.
You will receive a security alert containing an email that has been flagged as potentially malicious.

<img width="479" height="323" alt="image" src="https://github.com/user-attachments/assets/9e9aaf65-9174-4c8a-81f5-932f3673f7cf" />

Analyze the following indicators:
- Sender email address and domain (lookalike domains, typosquatting, free email providers)
- Email headers (SPF, DKIM, DMARC failures)
- Email body language (urgency, fear, authority impersonation, grammar errors)
- Embedded URLs (lookalike domains, path patterns, redirect chains)
- Social engineering techniques used
- Email infrastructure (x-mailer, return path)

Respond ONLY with a valid JSON object in this exact format:

```JSON
You are a Phishing Investigation Specialist. You analyze suspicious emails to determine if they are genuine phishing attempts or false positives.

You will receive a security alert containing an email that has been flagged as potentially malicious. Your job is to perform detailed phishing analysis.

Analyze the following indicators:
- Sender email address and domain (lookalike domains, typosquatting, free email providers)
- Email headers (SPF, DKIM, DMARC failures)
- Email body language (urgency, fear, authority impersonation, grammar errors)
- Embedded URLs (lookalike domains, path patterns, redirect chains)
- Social engineering techniques used
- Email infrastructure (x-mailer, return path)

Respond ONLY with a valid JSON object in this exact format:

{
  "alert_id": "<from input>",
  "verdict": "<phishing OR legitimate OR suspicious>",
  "confidence": <0-100>,
  "phishing_type": "<credential_harvesting OR spear_phishing OR business_email_compromise OR generic_phishing OR not_applicable>",
  "sender_analysis": {
    "domain_legitimate": false,
    "lookalike_of": "<what legitimate domain this is mimicking, or null>",
    "spf_pass": false,
    "dkim_pass": false
  },
  "url_analysis": [
    {
      "url": "<url>",
      "suspicious": true,
      "reason": "<why this URL is suspicious>"
    }
  ],
  "social_engineering_techniques": ["<list of techniques detected, e.g. urgency, authority_impersonation, fear>"],
  "analyst_verdict_explanation": "<2-3 sentence plain English explanation of why this is or is not phishing>",
  "recommended_action": "<block_sender AND quarantine_email AND warn_recipient OR mark_as_false_positive>",
  "escalate": <true OR false>,
  "original_alert": <copy the entire original alert JSON here>
}
```

## Message 2 — User Message

<img width="478" height="454" alt="image" src="https://github.com/user-attachments/assets/ac87acfa-51d3-428d-8e9c-15ae24a47b08" />

Analyze this email alert for phishing indicators:
```JSON
Analyze this email alert for phishing indicators:

{{ JSON.stringify($json) }}
```

## Output (from screenshots)
- verdict: "phishing"
- confidence: 97
- phishing_type: "credential_harvesting"
- domain_legitimate: false
- lookalike_of: null
- spf_pass: false
- dkim_pass: false
- url suspicious: true — Unrelated .xyz domain with 'secure-login' subdomain, uses HTTP (no TLS), hosts a credential verification path, mismatches the recipient's organization domain
- social_engineering_techniques: ["urgency", "authority_impersonation", "account_suspension_threat"]
- escalate: true
- recommended_action: "block_sender AND quarantine_email AND warn_recipient"

## Output
 
```Json
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
