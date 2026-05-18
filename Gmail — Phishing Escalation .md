# Node 12 — Gmail — Phishing Escalation

**Node Name:** Gmail — Phishing Escalation  
**Node Type:** Gmail   
**Credential:** Gmail OAuth2 API  
**Resource:** Message  
**Operation:** Send  
**Email Type:** HTML  

<img width="1557" height="578" alt="image" src="https://github.com/user-attachments/assets/ff5cb8e9-36a7-41cf-a008-9ad0727eb4fb" />

## Inside
<img width="1867" height="821" alt="image" src="https://github.com/user-attachments/assets/614ff429-2a55-45b9-86f7-51c62180b7ed" />


## What This Node Does
This node sends a phishing confirmed HTML alert email to the SOC team when
the Phishing Analyst Agent confirms the email is a genuine phishing attempt.
The email contains a full analysis summary including the alert ID, verdict,
phishing type, confidence score, targeted user, malicious sender details,
SPF/DKIM status, malicious URLs detected, social engineering techniques
identified, and a prioritised list of required response actions.

## Why We Use It
When phishing is confirmed the clock starts ticking. If the targeted user
already clicked the link and entered credentials, every minute without a
password reset increases the risk of account takeover and lateral movement
across the organisation. By automatically firing a rich, structured escalation
email the moment phishing is confirmed, the workflow eliminates the manual
steps of writing the email, looking up context, and notifying the right people.
The SOC team receives everything they need to respond immediately.

## Why HTML Format
A plain text email listing security findings is easy to miss in a busy inbox.
A dark-themed, colour-coded HTML alert with distinct sections for Phishing
Analysis Summary, Analyst Finding, Malicious URLs, Social Engineering Techniques,
and Required Response Actions is faster to read under pressure. Orange headers
signal severity. Red text on IOCs makes them immediately identifiable.

## Role in the Workflow
- Triggered ONLY on TRUE branch of IF: Confirmed Phishing? (verdict = phishing)
- This node WAS triggered in the demo run — email was successfully sent
- Output confirmed: labelIds = SENT

## Configuration

| Field | Value |
|-------|-------|
| Node Name | Gmail — Phishing Escalation |
| Node Type | Gmail |
| Credential | Gmail OAuth2 API |
| Resource | Message |
| Operation | Send |
| To | YOUR_SOC_EMAIL@example.com |
| Email Type | HTML |

## Subject Line Expression

```JSON
⚠️ PHISHING CONFIRMED — Credential Harvesting Attack | {{ $('Phishing Analyst Agent').item.json.message?.content ? JSON.parse($('Phishing Analyst Agent').item.json.message.content).alert_id : 'UNKNOWN' }}
```
## HTML Message Body

```html
<html><body style="font-family: Arial, sans-serif; background: #0a0f1e; color: #e8f4fd; padding: 24px;">
<div style="background: #ff6b35; padding: 16px; border-radius: 8px; margin-bottom: 20px;">
  <h1 style="color: white; margin: 0;">⚠️ PHISHING EMAIL CONFIRMED — User at Risk</h1>
</div>
<div style="background: #111d35; padding: 16px; border-radius: 8px; margin-bottom: 16px;">
  <h2 style="color: #00c8ff;">Phishing Analysis Summary</h2>
  <table style="width: 100%; border-collapse: collapse;">
    <tr><td style="padding:6px;color:#8bafc7;width:220px;">Alert ID</td><td style="padding:6px;color:#a8ff78;font-family:monospace;">{{ $('Phishing Analyst Agent').item.json.message?.content ? JSON.parse($('Phishing Analyst Agent').item.json.message.content).alert_id : 'N/A' }}</td></tr>
    <tr><td style="padding:6px;color:#8bafc7;">Verdict</td><td style="padding:6px;color:#ff3b3b;font-weight:bold;">PHISHING CONFIRMED</td></tr>
    <tr><td style="padding:6px;color:#8bafc7;">Phishing Type</td><td style="padding:6px;color:#ff6b35;">{{ $('Phishing Analyst Agent').item.json.message?.content ? JSON.parse($('Phishing Analyst Agent').item.json.message.content).phishing_type : 'N/A' }}</td></tr>
    <tr><td style="padding:6px;color:#8bafc7;">Confidence</td><td style="padding:6px;color:#ffd700;">{{ $('Phishing Analyst Agent').item.json.message?.content ? JSON.parse($('Phishing Analyst Agent').item.json.message.content).confidence : 'N/A' }}%</td></tr>
    <tr><td style="padding:6px;color:#8bafc7;">Targeted User</td><td style="padding:6px;color:#ff6b35;">john.smith@company.com</td></tr>
    <tr><td style="padding:6px;color:#8bafc7;">Malicious Sender</td><td style="padding:6px;color:#ff3b3b;font-family:monospace;">it-security@c0rporate-help.xyz</td></tr>
    <tr><td style="padding:6px;color:#8bafc7;">Sender IP</td><td style="padding:6px;color:#ff3b3b;font-family:monospace;">203.0.113.45</td></tr>
    <tr><td style="padding:6px;color:#8bafc7;">SPF Status</td><td style="padding:6px;color:#ff3b3b;">FAIL</td></tr>
    <tr><td style="padding:6px;color:#8bafc7;">DKIM Status</td><td style="padding:6px;color:#ff3b3b;">NONE</td></tr>
  </table>
</div>
<div style="background: #111d35; padding: 16px; border-radius: 8px; margin-bottom: 16px;">
  <h2 style="color: #ff6b35;">🔍 Analyst Finding</h2>
  <p style="color: #e8f4fd; line-height: 1.7;">{{ $('Phishing Analyst Agent').item.json.message?.content ? JSON.parse($('Phishing Analyst Agent').item.json.message.content).analyst_verdict_explanation : 'See attached alert details.' }}</p>
</div>
<div style="background: #111d35; padding: 16px; border-radius: 8px; margin-bottom: 16px;">
  <h2 style="color: #ffd700;">🔗 Malicious URLs Detected</h2>
  <div style="background: #0c1829; padding: 12px; border-radius: 4px; font-family: monospace; color: #ff3b3b;">
    http://secure-login.totallylegit.xyz/verify?user=john.smith
  </div>
  <p style="color: #8bafc7; font-size: 12px;">Lookalike domain impersonating legitimate corporate login portal. Credential harvesting page.</p>
</div>
<div style="background: #111d35; padding: 16px; border-radius: 8px; margin-bottom: 16px;">
  <h2 style="color: #b56eff;">🛡️ Social Engineering Techniques Detected</h2>
  <ul style="color: #e8f4fd; line-height: 2;">
    <li><strong style="color: #ff6b35;">Authority Impersonation</strong> — Sender claims to be IT Security Department</li>
    <li><strong style="color: #ff6b35;">Urgency / Fear</strong> — "Account will be suspended in 24 hours"</li>
    <li><strong style="color: #ff6b35;">Credential Harvesting Link</strong> — Fake verification portal</li>
  </ul>
</div>
<div style="background: #111d35; padding: 16px; border-radius: 8px; margin-bottom: 16px;">
  <h2 style="color: #39ff14;">✅ Required Response Actions</h2>
  <ol style="color: #e8f4fd; line-height: 2;">
    <li>Block sender domain <code style="color: #ff3b3b;">c0rporate-help.xyz</code> in email gateway</li>
    <li>Block sender IP <code style="color: #ff3b3b;">203.0.113.45</code> at perimeter</li>
    <li>Quarantine the phishing email from john.smith inbox</li>
    <li>Warn john.smith — check if credentials were already entered</li>
    <li>If credentials entered → force password reset immediately</li>
    <li>Report domain to registrar for takedown</li>
  </ol>
</div>
<div style="background: #1a0a00; padding: 12px; border-radius: 8px; border: 1px solid #ff6b35;">
  <p style="color: #ff6b35; font-weight: bold; margin: 0;">⚠ Auto-generated by SOC SOAR platform. Verify with affected user whether credentials were submitted before taking containment actions.</p>
</div>
</body></html>
```

## Actual Output from Screenshots
- id: 19e2afda2a90917f
- threadId: 19e2afda2a90917f
- labelIds: SENT ✅
- Email was successfully delivered to the SOC inbox in the demo run

<img width="1536" height="36" alt="image" src="https://github.com/user-attachments/assets/32997e20-1da8-4d7a-82d1-86c0a58cf6fb" />
<img width="1243" height="662" alt="image" src="https://github.com/user-attachments/assets/59198aea-0ee9-44da-9902-75fcc37bc021" />
<img width="1498" height="662" alt="image" src="https://github.com/user-attachments/assets/c6f0479d-0362-42e2-bc0e-d44fcee28ee7" />

