## Node 2 — Code in JavaScript

**Node Name:** Code in JavaScript  
**Node Type:** Code  
**Language:** JavaScript  

<img width="1713" height="631" alt="image" src="https://github.com/user-attachments/assets/c7fdf6a5-f486-4ee8-94ce-97afb8acf44d" />

## Purpose
Generates 2 mock security alerts (phishing + ransomware) as separate items.

## Output
- 2 items → item 0: phishingAlert, item 1: ransomwareAlert

## Code to Paste

```json
const phishingAlert = {
  alert_id: "ALT-2026-001",
  alert_type: "email_security",
  severity: "high",
  timestamp: "2026-05-04T09:14:22Z",
  source_system: "Wazuh",
  rule_id: "100601",
  rule_level: 12,
  agent_name: "WIN-WORKSTATION-07",
  agent_ip: "10.10.10.45",
  email: {
    sender: "it-security@c0rporate-help.xyz",
    sender_ip: "203.0.113.45",
    recipient: "john.smith@company.com",
    subject: "URGENT: Your account will be suspended in 24 hours",
    body: "Dear Employee,\n\nOur security system has detected unusual activity on your account. Your account will be suspended within 24 hours unless you verify your credentials immediately.\n\nPlease click the link below to verify your identity:\nhttp://secure-login.totallylegit.xyz/verify?user=john.smith\n\nFailure to verify will result in permanent account suspension.\n\nIT Security Department",
    urls: ["http://secure-login.totallylegit.xyz/verify?user=john.smith"],
    attachment: null,
    headers: {
      return_path: "bounce@c0rporate-help.xyz",
      x_mailer: "PHPMailer 6.1.4",
      spf: "fail",
      dkim: "none"
    }
  }
};
 
const ransomwareAlert = {
  alert_id: "ALT-2026-002",
  alert_type: "endpoint_security",
  severity: "critical",
  timestamp: "2026-05-04T09:31:05Z",
  source_system: "Wazuh",
  rule_id: "100900",
  rule_level: 15,
  agent_name: "WIN-SERVER-03",
  agent_ip: "10.10.10.22",
  ransomware: {
    process_name: "encrypt.exe",
    process_path: "C:\\Users\\jsmith\\AppData\\Local\\Temp\\encrypt.exe",
    file_hash_md5: "e99a18c428cb38d5f260853678922e03",
    file_hash_sha256: "6b86b273ff34fce19d6b804eff5a3f5747ada4eaa22f1d49c01e52ddb7875b4b",
    files_encrypted: 847,
    extension_added: ".locked",
    ransom_note_path: "C:\\Users\\jsmith\\Desktop\\READ_ME_NOW.txt",
    c2_ip: "185.208.156.57",
    c2_port: 4444,
    lateral_movement_detected: true,
    affected_shares: ["\\\\10.10.10.22\\Finance", "\\\\10.10.10.22\\HR"]
  }
};
 
// Return both alerts as separate items.
// n8n processes each item independently through downstream nodes.
return [
  { json: phishingAlert },
  { json: ransomwareAlert }
];
```


## Output ->
```json
[
  {
    "alert_id": "ALT-2026-001",
    "alert_type": "email_security",
    "severity": "high",
    "timestamp": "2026-05-04T09:14:22Z",
    "source_system": "Wazuh",
    "rule_id": "100601",
    "rule_level": 12,
    "agent_name": "WIN-WORKSTATION-07",
    "agent_ip": "10.10.10.45",
    "email": {
      "sender": "it-security@c0rporate-help.xyz",
      "sender_ip": "203.0.113.45",
      "recipient": "john.smith@company.com",
      "subject": "URGENT: Your account will be suspended in 24 hours",
      "body": "Dear Employee,\n\nOur security system has detected unusual activity on your account. Your account will be suspended within 24 hours unless you verify your credentials immediately.\n\nPlease click the link below to verify your identity:\nhttp://secure-login.totallylegit.xyz/verify?user=john.smith\n\nFailure to verify will result in permanent account suspension.\n\nIT Security Department",
      "urls": [
        "http://secure-login.totallylegit.xyz/verify?user=john.smith"
      ],
      "attachment": null,
      "headers": {
        "return_path": "bounce@c0rporate-help.xyz",
        "x_mailer": "PHPMailer 6.1.4",
        "spf": "fail",
        "dkim": "none"
      }
    }
  },
  {
    "alert_id": "ALT-2026-002",
    "alert_type": "endpoint_security",
    "severity": "critical",
    "timestamp": "2026-05-04T09:31:05Z",
    "source_system": "Wazuh",
    "rule_id": "100900",
    "rule_level": 15,
    "agent_name": "WIN-SERVER-03",
    "agent_ip": "10.10.10.22",
    "ransomware": {
      "process_name": "encrypt.exe",
      "process_path": "C:\\Users\\jsmith\\AppData\\Local\\Temp\\encrypt.exe",
      "file_hash_md5": "e99a18c428cb38d5f260853678922e03",
      "file_hash_sha256": "6b86b273ff34fce19d6b804eff5a3f5747ada4eaa22f1d49c01e52ddb7875b4b",
      "files_encrypted": 847,
      "extension_added": ".locked",
      "ransom_note_path": "C:\\Users\\jsmith\\Desktop\\READ_ME_NOW.txt",
      "c2_ip": "185.208.156.57",
      "c2_port": 4444,
      "lateral_movement_detected": true,
      "affected_shares": [
        "\\\\10.10.10.22\\Finance",
        "\\\\10.10.10.22\\HR"
      ]
    }
  }
]
```
