# Node 6 — VirusTotal Hash Check

**Node Name:** VirusTotal Hash Check  
**Node Type:** HTTP Request  
**Method:** GET    

<img width="1707" height="622" alt="image" src="https://github.com/user-attachments/assets/c224c4a5-1387-43e3-8004-87d875de519c" />

## Inside
<img width="1866" height="797" alt="image" src="https://github.com/user-attachments/assets/9b5ce026-690d-4301-a015-033df098f404" />

## What This Node Does
This node takes the SHA256 file hash extracted by the Ransomware Expert Agent
and submits it to the VirusTotal API for real-time threat intelligence
verification. VirusTotal checks the hash against the databases of 70+ antivirus
engines and returns a detailed verdict including how many vendors flagged it as
malicious, sandbox analysis results, tags, and reputation scores.

## Why We Use It
AI analysis alone — even from a specialised expert agent — can produce false
positives or work with incomplete information. VirusTotal provides ground truth
from real threat intelligence databases. By combining AI reasoning with
real-world threat intelligence, the workflow becomes significantly more accurate.
A hash confirmed malicious by multiple AV vendors is a hard, objective data
point that justifies immediate escalation with no ambiguity.

## Important Limitation
A result of 0 malicious vendors does NOT mean the file is safe. Three reasons:
1. The sample may be new and not yet in the VirusTotal database
2. Attackers commonly modify one byte of known malware to change the hash
3. Polymorphic malware generates unique hashes per victim machine
This is why the AI analysis from the Ransomware Expert Agent remains the primary
signal, and VirusTotal is a supporting enrichment layer.

## Role in the Workflow
- Receives: 1 item from the Ransomware Expert Agent
- Submits: SHA256 hash to VirusTotal GET /api/v3/files/{hash}
- Outputs: Full VirusTotal file report including last_analysis_stats
- The malicious count from last_analysis_stats feeds the next IF node

## Configuration

| Field | Value |
|-------|-------|
| Node Name | VirusTotal Hash Check |
| Node Type | HTTP Request |
| Method | GET |
| Authentication | None |
| Send Query Parameters | OFF |
| Send Headers | ON |
| Send Body | OFF |

## URL Expression

```json
https://www.virustotal.com/api/v3/files/{{ $('Code in JavaScript').item.json.ransomware.file_hash_sha256 }}
```

## Get your VirusTotal API KEY
https://docs.virustotal.com/docs/please-give-me-an-api-key

## Headers

| Name | Value |
|------|-------|
| x-apikey | YOUR_VIRUSTOTAL_API_KEY |
<img width="1866" height="797" alt="image" src="https://github.com/user-attachments/assets/1f88416f-cd1d-43ad-b833-7b0d520a422f" />


> ⚠️ Replace YOUR_VIRUSTOTAL_API_KEY with your real key.
> Header name must be exactly x-apikey — all lowercase, no spaces, no quotes.
> Free tier: 4 requests/minute, 500 lookups/day.

## Actual Output from Screenshots

| Field | Value |
|-------|-------|
| data.id | 6b86b273ff34fce19d6b804eff5a3f5747ada4eaa22f1d49c01e52ddb7875b4b |
| data.type | file |
| attributes.reputation | 350 |
| attributes.safepickle.classification | benign |
| attributes.last_analysis_stats.malicious | 0 |
| attributes.last_analysis_stats.suspicious | 0 |
| attributes.last_analysis_stats.undetected | 56 |
| attributes.last_analysis_stats.harmless | 0 |
| attributes.last_analysis_stats.timeout | 5 |
| attributes.times_submitted | 11399 |
| sandbox_verdicts.Zenbox.category | harmless |
| sandbox_verdicts.Zenbox.malware_classification[0] | CLEAN |
| sigma_analysis_summary.high | 0 |
| sigma_analysis_summary.medium | 1 |
| sigma_analysis_summary.critical | 0 |

> ℹ️ In this demo run the hash returned 0 malicious detections.
> This routes the workflow to the FALSE branch (Hash Not Malicious).
> In a real incident with a known ransomware hash the malicious count
> would be much higher (often 40–60+ vendors).
