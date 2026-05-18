# Node 13 — Not Confirmed Phishing

**Node Name:** Not Confirmed Phishing  
**Node Type:** Set (Manual Mapping)  
**Triggered by:** FALSE branch of IF: Confirmed Phishing?  

## What This Node Does
This node is the clean exit point for the phishing branch when the Phishing
Analyst Agent's verdict is "legitimate" or "suspicious" rather than a confirmed
phishing attempt. It sets a status field to record that the email alert was
investigated, an AI phishing specialist reviewed all indicators, and the case
is being closed as not confirmed phishing.

## Why We Use It
Every branch in a SOAR playbook needs a defined and logged exit. Without this
node, the FALSE branch would end silently with no record of the outcome.
This Set node creates an audit trail that downstream logging systems, SIEM
integrations, or ticketing tools can consume to mark the alert as reviewed
and closed — maintaining a complete chain of custody for every alert the
workflow processes.

## The Difference Between "Not Confirmed" and "Safe"
A verdict of "legitimate" or "suspicious" from the Phishing Analyst Agent
does not necessarily mean the email is safe. "Suspicious" means there are
concerning indicators that do not meet the threshold for a confirmed phishing
verdict. In a real SOC, "suspicious" verdicts would typically be queued for
manual review by a Tier-2 analyst rather than being fully closed. The
closed_not_phishing status in this lab is a simplified representation
of that outcome.

## Role in the Workflow
- Triggered by FALSE branch of IF: Confirmed Phishing? (verdict ≠ phishing)
- This is the terminal node for the phishing branch when no escalation is needed
- This node was NOT reached in the demo run — phishing was confirmed (TRUE branch)

## Configuration

| Field | Value |
|-------|-------|
| Node Name | Not Confirmed Phishing |
| Node Type | Set (Manual Mapping) |
| Mode | Manual Mapping |

## Fields to Set

| Field Name | Type | Value |
|------------|------|-------|
| status | String | closed_not_phishing |

## Note
In the demo run this node was NOT reached.
The Phishing Analyst Agent returned verdict = "phishing" with confidence 97%,
so the TRUE branch was taken and the Gmail escalation fired instead.
