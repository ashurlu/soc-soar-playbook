## 🛡️ Agentic SOC SOAR Playbook — n8n + OpenAI + VirusTotal + Gmail

An AI-powered Security Orchestration, Automation & Response (SOAR) workflow built entirely in n8n. The playbook automatically triages phishing and ransomware alerts, enriches them with VirusTotal intelligence, and sends rich HTML escalation emails — all without human intervention.

<img width="2139" height="797" alt="Screenshot 2026-05-15 133554" src="https://github.com/user-attachments/assets/d030920f-6f49-4de2-b7c6-5af7197b2b8a" />
Complete n8n canvas: Trigger → Orchestrator → IF Router → Ransomware Expert + VirusTotal → Phishing Analyst → Gmail Escalations


## 🏗️ Architecture

```
Trigger
  └─► Mock Alert Injector (Code Node — 2 alerts)
        └─► Orchestrator / Junior Analyst (GPT)
              └─► IF: Ransomware or Phishing?
                    ├─► [TRUE]  Ransomware Expert Agent (GPT)
                    │           └─► VirusTotal Hash Check
                    │                 └─► IF: Hash Malicious?
                    │                       ├─► [TRUE]  Gmail — Ransomware Escalation
                    │                       └─► [FALSE] Log — Hash Not Malicious
                    │
                    └─► [FALSE] Phishing Analyst Agent (GPT)
                                └─► IF: Confirmed Phishing?
                                      ├─► [TRUE]  Gmail — Phishing Escalation
                                      └─► [FALSE] Log — Not Confirmed Phishing
```

