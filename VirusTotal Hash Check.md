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

## Output
```json
[
  {
    "data": {
      "id": "6b86b273ff34fce19d6b804eff5a3f5747ada4eaa22f1d49c01e52ddb7875b4b",
      "type": "file",
      "links": {
        "self": "https://www.virustotal.com/api/v3/files/6b86b273ff34fce19d6b804eff5a3f5747ada4eaa22f1d49c01e52ddb7875b4b"
      },
      "attributes": {
        "type_tag": "text",
        "times_submitted": 11406,
        "filecondis": {
          "raw_md5": "c65d234bfc2cc5d039ac787eb6bba470",
          "dhash": "8000000000000000"
        },
        "last_analysis_stats": {
          "malicious": 0,
          "suspicious": 0,
          "undetected": 61,
          "harmless": 0,
          "timeout": 0,
          "confirmed-timeout": 0,
          "failure": 0,
          "type-unsupported": 14
        },
        "vhash": "9eecb7db59d16c80417c72d1e1f4fbf1",
        "monitor_info": {
          "organizations": [
            "Linux",
            "Microsoft"
          ],
          "filenames": [
            "corlexa_it-IT_lq_echo.th-6b86b273ff34fce19d6b804eff5a3f5747ada4eaa22f1d49c01e52ddb7875b4b",
            "install_mode-6b86b273ff34fce19d6b804eff5a3f5747ada4eaa22f1d49c01e52ddb7875b4b",
            "symsrv.yes-6b86b273ff34fce19d6b804eff5a3f5747ada4eaa22f1d49c01e52ddb7875b4b"
          ]
        },
        "first_submission_date": 1209605422,
        "tags": [
          "direct-cpu-clock-access",
          "text",
          "via-tor",
          "attachment",
          "known-distributor",
          "software-collection",
          "long-sleeps",
          "trusted",
          "runtime-modules",
          "detect-debug-environment",
          "legit",
          "nsrl"
        ],
        "type_description": "Text",
        "sigma_analysis_stats": {
          "high": 0,
          "medium": 1,
          "critical": 0,
          "low": 0
        },
        "magika": "TXT",
        "last_analysis_results": {
          "Bkav": {
            "method": "blacklist",
            "engine_name": "Bkav",
            "engine_version": "8.2.40(8338)",
            "engine_update": "20260516",
            "category": "undetected",
            "result": null
          },
          "Lionic": {
            "method": "blacklist",
            "engine_name": "Lionic",
            "engine_version": "8.16",
            "engine_update": "20260517",
            "category": "undetected",
            "result": null
          },
          "MicroWorld-eScan": {
            "method": "blacklist",
            "engine_name": "MicroWorld-eScan",
            "engine_version": "14.0.409.0",
            "engine_update": "20260517",
            "category": "undetected",
            "result": null
          },
          "ClamAV": {
            "method": "blacklist",
            "engine_name": "ClamAV",
            "engine_version": "1.5.2.0",
            "engine_update": "20260516",
            "category": "undetected",
            "result": null
          },
          "CMC": {
            "method": "blacklist",
            "engine_name": "CMC",
            "engine_version": "2.4.2022.1",
            "engine_update": "20260517",
            "category": "undetected",
            "result": null
          },
          "CAT-QuickHeal": {
            "method": "blacklist",
            "engine_name": "CAT-QuickHeal",
            "engine_version": "22.00",
            "engine_update": "20260516",
            "category": "undetected",
            "result": null
          },
          "Skyhigh": {
            "method": "blacklist",
            "engine_name": "Skyhigh",
            "engine_version": "v2021.2.0+4045",
            "engine_update": "20260516",
            "category": "undetected",
            "result": null
          },
          "ALYac": {
            "method": "blacklist",
            "engine_name": "ALYac",
            "engine_version": "2.0.0.10",
            "engine_update": "20260516",
            "category": "undetected",
            "result": null
          },
          "Malwarebytes": {
            "method": "blacklist",
            "engine_name": "Malwarebytes",
            "engine_version": "3.1.0.235",
            "engine_update": "20260517",
            "category": "undetected",
            "result": null
          },
          "Zillya": {
            "method": "blacklist",
            "engine_name": "Zillya",
            "engine_version": "2.0.0.5603",
            "engine_update": "20260515",
            "category": "undetected",
            "result": null
          },
          "Sangfor": {
            "method": "blacklist",
            "engine_name": "Sangfor",
            "engine_version": "2.22.3.0",
            "engine_update": "20260515",
            "category": "undetected",
            "result": null
          },
          "K7AntiVirus": {
            "method": "blacklist",
            "engine_name": "K7AntiVirus",
            "engine_version": "14.52.59527",
            "engine_update": "20260517",
            "category": "undetected",
            "result": null
          },
          "K7GW": {
            "method": "blacklist",
            "engine_name": "K7GW",
            "engine_version": "14.52.59527",
            "engine_update": "20260517",
            "category": "undetected",
            "result": null
          },
          "CrowdStrike": {
            "method": "blacklist",
            "engine_name": "CrowdStrike",
            "engine_version": "1.0",
            "engine_update": "20230417",
            "category": "undetected",
            "result": null
          },
          "Arcabit": {
            "method": "blacklist",
            "engine_name": "Arcabit",
            "engine_version": "2025.0.0.23",
            "engine_update": "20260517",
            "category": "undetected",
            "result": null
          },
          "VirIT": {
            "method": "blacklist",
            "engine_name": "VirIT",
            "engine_version": "9.5.1208",
            "engine_update": "20260515",
            "category": "undetected",
            "result": null
          },
          "Symantec": {
            "method": "blacklist",
            "engine_name": "Symantec",
            "engine_version": "1.22.0.0",
            "engine_update": "20260516",
            "category": "undetected",
            "result": null
          },
          "ESET-NOD32": {
            "method": "blacklist",
            "engine_name": "ESET-NOD32",
            "engine_version": "18.2.18.0",
            "engine_update": "20260517",
            "category": "undetected",
            "result": null
          },
          "TrendMicro-HouseCall": {
            "method": "blacklist",
            "engine_name": "TrendMicro-HouseCall",
            "engine_version": "24.550.0.1002",
            "engine_update": "20260517",
            "category": "undetected",
            "result": null
          },
          "Avast": {
            "method": "blacklist",
            "engine_name": "Avast",
            "engine_version": "23.9.8494.0",
            "engine_update": "20260515",
            "category": "undetected",
            "result": null
          },
          "Cynet": {
            "method": "blacklist",
            "engine_name": "Cynet",
            "engine_version": "4.0.3.4",
            "engine_update": "20260517",
            "category": "undetected",
            "result": null
          },
          "Kaspersky": {
            "method": "blacklist",
            "engine_name": "Kaspersky",
            "engine_version": "22.0.1.28",
            "engine_update": "20260517",
            "category": "undetected",
            "result": null
          },
          "BitDefender": {
            "method": "blacklist",
            "engine_name": "BitDefender",
            "engine_version": "7.2",
            "engine_update": "20260517",
            "category": "undetected",
            "result": null
          },
          "NANO-Antivirus": {
            "method": "blacklist",
            "engine_name": "NANO-Antivirus",
            "engine_version": "1.0.170.26895",
            "engine_update": "20260517",
            "category": "undetected",
            "result": null
          },
          "SUPERAntiSpyware": {
            "method": "blacklist",
            "engine_name": "SUPERAntiSpyware",
            "engine_version": "5.6.0.1032",
            "engine_update": "20260516",
            "category": "undetected",
            "result": null
          },
          "Tencent": {
            "method": "blacklist",
            "engine_name": "Tencent",
            "engine_version": "1.0.0.1",
            "engine_update": "20260517",
            "category": "undetected",
            "result": null
          },
          "Sophos": {
            "method": "blacklist",
            "engine_name": "Sophos",
            "engine_version": "3.5.1.0",
            "engine_update": "20260516",
            "category": "undetected",
            "result": null
          },
          "F-Secure": {
            "method": "blacklist",
            "engine_name": "F-Secure",
            "engine_version": "18.10.1547.307",
            "engine_update": "20260517",
            "category": "undetected",
            "result": null
          },
          "DrWeb": {
            "method": "blacklist",
            "engine_name": "DrWeb",
            "engine_version": "7.0.75.2070",
            "engine_update": "20260517",
            "category": "undetected",
            "result": null
          },
          "VIPRE": {
            "method": "blacklist",
            "engine_name": "VIPRE",
            "engine_version": "6.0.0.35",
            "engine_update": "20260516",
            "category": "undetected",
            "result": null
          },
          "TrendMicro": {
            "method": "blacklist",
            "engine_name": "TrendMicro",
            "engine_version": "24.550.0.1002",
            "engine_update": "20260517",
            "category": "undetected",
            "result": null
          },
          "McAfeeD": {
            "method": "blacklist",
            "engine_name": "McAfeeD",
            "engine_version": "1.2.0.14532",
            "engine_update": "20260517",
            "category": "undetected",
            "result": null
          },
          "CTX": {
            "method": "blacklist",
            "engine_name": "CTX",
            "engine_version": "2024.8.29.1",
            "engine_update": "20260517",
            "category": "undetected",
            "result": null
          },
          "Emsisoft": {
            "method": "blacklist",
            "engine_name": "Emsisoft",
            "engine_version": "2024.8.0.61147",
            "engine_update": "20260517",
            "category": "undetected",
            "result": null
          },
          "huorong": {
            "method": "blacklist",
            "engine_name": "huorong",
            "engine_version": "5fb5e6f:5fb5e6f:d24df83:d24df83",
            "engine_update": "20260516",
            "category": "undetected",
            "result": null
          },
          "Jiangmin": {
            "method": "blacklist",
            "engine_name": "Jiangmin",
            "engine_version": "16.0.100",
            "engine_update": "20260512",
            "category": "undetected",
            "result": null
          },
          "Google": {
            "method": "blacklist",
            "engine_name": "Google",
            "engine_version": "1778994045",
            "engine_update": "20260517",
            "category": "undetected",
            "result": null
          },
          "Avira": {
            "method": "blacklist",
            "engine_name": "Avira",
            "engine_version": "8.3.3.24",
            "engine_update": "20260517",
            "category": "undetected",
            "result": null
          },
          "Antiy-AVL": {
            "method": "blacklist",
            "engine_name": "Antiy-AVL",
            "engine_version": "3.0",
            "engine_update": "20260517",
            "category": "undetected",
            "result": null
          },
          "Kingsoft": {
            "method": "blacklist",
            "engine_name": "Kingsoft",
            "engine_version": "None",
            "engine_update": "20260516",
            "category": "undetected",
            "result": null
          },
          "Gridinsoft": {
            "method": "blacklist",
            "engine_name": "Gridinsoft",
            "engine_version": "1.0.245.174",
            "engine_update": "20260517",
            "category": "undetected",
            "result": null
          },
          "Xcitium": {
            "method": "blacklist",
            "engine_name": "Xcitium",
            "engine_version": "38653",
            "engine_update": "20260516",
            "category": "undetected",
            "result": null
          },
          "Microsoft": {
            "method": "blacklist",
            "engine_name": "Microsoft",
            "engine_version": "1.1.26030.3008",
            "engine_update": "20260517",
            "category": "undetected",
            "result": null
          },
          "ViRobot": {
            "method": "blacklist",
            "engine_name": "ViRobot",
            "engine_version": "2014.3.20.0",
            "engine_update": "20260516",
            "category": "undetected",
            "result": null
          },
          "ZoneAlarm": {
            "method": "blacklist",
            "engine_name": "ZoneAlarm",
            "engine_version": "6.24-114820976",
            "engine_update": "20260516",
            "category": "undetected",
            "result": null
          },
          "GData": {
            "method": "blacklist",
            "engine_name": "GData",
            "engine_version": "GD:27.44574AVA:64.31259",
            "engine_update": "20260517",
            "category": "undetected",
            "result": null
          },
          "Varist": {
            "method": "blacklist",
            "engine_name": "Varist",
            "engine_version": "6.6.1.3",
            "engine_update": "20260517",
            "category": "undetected",
            "result": null
          },
          "AhnLab-V3": {
            "method": "blacklist",
            "engine_name": "AhnLab-V3",
            "engine_version": "3.30.0.10666",
            "engine_update": "20260517",
            "category": "undetected",
            "result": null
          },
          "Acronis": {
            "method": "blacklist",
            "engine_name": "Acronis",
            "engine_version": "1.2.0.121",
            "engine_update": "20240328",
            "category": "undetected",
            "result": null
          },
          "VBA32": {
            "method": "blacklist",
            "engine_name": "VBA32",
            "engine_version": "5.6.1",
            "engine_update": "20260515",
            "category": "undetected",
            "result": null
          },
          "TACHYON": {
            "method": "blacklist",
            "engine_name": "TACHYON",
            "engine_version": "2026-05-17.01",
            "engine_update": "20260517",
            "category": "undetected",
            "result": null
          },
          "Zoner": {
            "method": "blacklist",
            "engine_name": "Zoner",
            "engine_version": "2.2.2.0",
            "engine_update": "20260517",
            "category": "undetected",
            "result": null
          },
          "Rising": {
            "method": "blacklist",
            "engine_name": "Rising",
            "engine_version": "25.0.0.28",
            "engine_update": "20260517",
            "category": "undetected",
            "result": null
          },
          "Yandex": {
            "method": "blacklist",
            "engine_name": "Yandex",
            "engine_version": "5.5.2.24",
            "engine_update": "20260517",
            "category": "undetected",
            "result": null
          },
          "TrellixENS": {
            "method": "blacklist",
            "engine_name": "TrellixENS",
            "engine_version": "6.0.6.653",
            "engine_update": "20260516",
            "category": "undetected",
            "result": null
          },
          "Ikarus": {
            "method": "blacklist",
            "engine_name": "Ikarus",
            "engine_version": "6.4.16.0",
            "engine_update": "20260516",
            "category": "undetected",
            "result": null
          },
          "MaxSecure": {
            "method": "blacklist",
            "engine_name": "MaxSecure",
            "engine_version": "1.0.0.1",
            "engine_update": "20260515",
            "category": "undetected",
            "result": null
          },
          "Fortinet": {
            "method": "blacklist",
            "engine_name": "Fortinet",
            "engine_version": "7.0.30.0",
            "engine_update": "20260517",
            "category": "undetected",
            "result": null
          },
          "AVG": {
            "method": "blacklist",
            "engine_name": "AVG",
            "engine_version": "23.9.8494.0",
            "engine_update": "20260515",
            "category": "undetected",
            "result": null
          },
          "Panda": {
            "method": "blacklist",
            "engine_name": "Panda",
            "engine_version": "4.6.4.2",
            "engine_update": "20260516",
            "category": "undetected",
            "result": null
          },
          "alibabacloud": {
            "method": "blacklist",
            "engine_name": "alibabacloud",
            "engine_version": "2.2.0",
            "engine_update": "20250321",
            "category": "undetected",
            "result": null
          },
          "Avast-Mobile": {
            "method": "blacklist",
            "engine_name": "Avast-Mobile",
            "engine_version": "260515-00",
            "engine_update": "20260516",
            "category": "type-unsupported",
            "result": null
          },
          "SymantecMobileInsight": {
            "method": "blacklist",
            "engine_name": "SymantecMobileInsight",
            "engine_version": "2.0",
            "engine_update": "20260123",
            "category": "type-unsupported",
            "result": null
          },
          "BitDefenderFalx": {
            "method": "blacklist",
            "engine_name": "BitDefenderFalx",
            "engine_version": "2.0.936",
            "engine_update": "20251216",
            "category": "type-unsupported",
            "result": null
          },
          "DeepInstinct": {
            "method": "blacklist",
            "engine_name": "DeepInstinct",
            "engine_version": "5.0.0.8",
            "engine_update": "20260516",
            "category": "type-unsupported",
            "result": null
          },
          "Elastic": {
            "method": "blacklist",
            "engine_name": "Elastic",
            "engine_version": "4.0.261",
            "engine_update": "20260513",
            "category": "type-unsupported",
            "result": null
          },
          "Webroot": {
            "method": "blacklist",
            "engine_name": "Webroot",
            "engine_version": "1.9.0.8",
            "engine_update": "20250227",
            "category": "type-unsupported",
            "result": null
          },
          "APEX": {
            "method": "blacklist",
            "engine_name": "APEX",
            "engine_version": "6.779",
            "engine_update": "20260516",
            "category": "type-unsupported",
            "result": null
          },
          "Paloalto": {
            "method": "blacklist",
            "engine_name": "Paloalto",
            "engine_version": "0.9.0.1003",
            "engine_update": "20260517",
            "category": "type-unsupported",
            "result": null
          },
          "Alibaba": {
            "method": "blacklist",
            "engine_name": "Alibaba",
            "engine_version": "0.3.0.5",
            "engine_update": "20190527",
            "category": "type-unsupported",
            "result": null
          },
          "Trapmine": {
            "method": "blacklist",
            "engine_name": "Trapmine",
            "engine_version": "4.0.12.0",
            "engine_update": "20260504",
            "category": "type-unsupported",
            "result": null
          },
          "Cylance": {
            "method": "blacklist",
            "engine_name": "Cylance",
            "engine_version": "3.0.0.0",
            "engine_update": "20260514",
            "category": "type-unsupported",
            "result": null
          },
          "SentinelOne": {
            "method": "blacklist",
            "engine_name": "SentinelOne",
            "engine_version": "7.6.2.19",
            "engine_update": "20260324",
            "category": "type-unsupported",
            "result": null
          },
          "tehtris": {
            "method": "blacklist",
            "engine_name": "tehtris",
            "engine_version": null,
            "engine_update": "20260517",
            "category": "type-unsupported",
            "result": null
          },
          "Trustlook": {
            "method": "blacklist",
            "engine_name": "Trustlook",
            "engine_version": "1.0",
            "engine_update": "20260517",
            "category": "type-unsupported",
            "result": null
          }
        },
        "last_submission_date": 1779051057,
        "tlsh": "TNULL",
        "meaningful_name": "charset.bin",
        "sandbox_verdicts": {
          "Zenbox": {
            "category": "harmless",
            "sandbox_name": "Zenbox",
            "malware_classification": [
              "CLEAN"
            ]
          }
        },
        "trusted_verdict": {
          "organization": "Microsoft Corporation",
          "verdict": "goodware",
          "generator": "Microsoft Corporation",
          "filename": "charset.bin"
        },
        "names": [
          ".format_version",
          "stop",
          "messageMaskY.txt",
          "com.qk.plugin.qkfx.Manager",
          "qk_js_shell_config",
          "Ampharos Catches.txt",
          "Azelf Catches.txt",
          "Clefairy Attempts.txt",
          "Clefairy Catches.txt",
          "Cleffa Attempts.txt",
          "Cleffa Catches.txt",
          "Cubone Catches.txt",
          "Dodrio Catches.txt",
          "Electivire Catches.txt",
          "Exeggutor Attempts.txt",
          "Exeggutor Catches.txt",
          "Genesect star Attempts.txt",
          "Golduck Catches.txt",
          "Growlithe Catches.txt",
          "Jigglypuff Attempts.txt",
          "Jigglypuff Catches.txt",
          "Lapras Catches.txt",
          "Magmortar Catches.txt",
          "Mankey Attempts.txt",
          "Mankey Catches.txt",
          "Mantine Attempts.txt",
          "Meowth Attempts.txt",
          "Meowth Catches.txt",
          "Paras Attempts.txt",
          "Paras Catches.txt",
          "Parasect Attempts.txt",
          "Parasect Catches.txt",
          "Persian Attempts.txt",
          "Persian Catches.txt",
          "Phione Attempts.txt",
          "Pinsir Catches.txt",
          "Poliwrath Catches.txt",
          "Psyduck Catches.txt",
          "Raichu Catches.txt",
          "Shiny articuno Catches.txt",
          "Shiny butterfree Attempts.txt",
          "Shiny cubone Catches.txt",
          "Shiny dragonair Attempts.txt",
          "Shiny farfetch'd Attempts.txt",
          "Shiny heatran Attempts.txt",
          "Shiny mr. mime Attempts.txt",
          "Shiny muk Attempts.txt",
          "Shiny nidoran female Attempts.txt",
          "Shiny nidoran male Attempts.txt",
          "Shiny registeel Catches.txt",
          "Shiny venonat Attempts.txt",
          "Slowbro Attempts.txt",
          "Slowbro Catches.txt",
          "Tauros Catches.txt",
          "Totodile Attempts.txt",
          "Umbreon Attempts.txt",
          "Venonat Attempts.txt",
          "Venonat Catches.txt",
          "Wigglytuff Catches.txt",
          "Zubat Catches.txt",
          "version_config",
          "v8alibgame.so",
          "vip.md",
          "cellswitch.dat",
          "edition.edat",
          "hezuoid.txt",
          "use_http",
          "enabled.txt",
          "cmcc_omp"
        ],
        "type_tags": [
          "text"
        ],
        "nsrl_info": {
          "products": [
            "Commerce Server Developer Edition (Microsoft)",
            "eMbedded Visual Tools (Microsoft)",
            "Commerce Server - Developer Edition (Microsoft)",
            "SNA Server (Microsoft)",
            "Commerce Server Developer Editon (Microsoft)",
            "BackOffice Server (Microsoft)",
            "Platforms SDKs/DDKs (Microsoft)",
            "Servers (Microsoft)",
            "Applications, Developer Tools (Microsoft)",
            "Platforms, Servers, Applications, SDK/DDK (Microsoft)",
            "Commerce Server (Microsoft)",
            "Office (Microsoft)",
            "Back Office 2.5 (Microsoft)",
            "SNA Server 3.0, SP2, Proxy Server (Microsoft)",
            "Ventura 7 (Corel Corporation)",
            "PowerBuilder Desktop for Windows (PowerSoft Enterprises)",
            "WordPerfect Suite (Corel Corporation)",
            "BackOffice (Microsoft)",
            "Developer Tools, Servers (Microsoft)",
            "Internet Explorer Versions (Microsoft)"
          ],
          "filenames": [
            "DISK1.TAG",
            "ASYNC.ASY",
            "PlatSdk_Chw.846034EE_C21B_11D2_A85A_00C04FC2A854",
            "Custom.dic, ffastlog.txt",
            "MEDIA.ID",
            "DISK1.ID",
            "DISK1",
            "EXTRACT.FTS, EXTRACT.GID, SA4.FTS, SA4.GID, UGPS.FTS, UGPS.GID",
            ".version, VERSION",
            ".version",
            "DISK.001, DISK.002",
            "INSTRC.LAF",
            "client_ConnectionHandleTable, client_ConnectionHandleTable1, client_ExceptionTableSize, client_ExceptionTableSize1, client_PushIDTableSize, client_PushIDTableSize1, client_SessionIDTableSize, client_SessionIDTableSize1, randomStartTidEnable, randomStartTidEnable1, reassemblyEnable, reassemblyEnable1, segmentationEnable, segmentationEnable1",
            "MzeeTamu_4.txt",
            "internet.ini",
            "IWD2CD.1",
            "PTOMB.CFG",
            "PE Content CD.set",
            "19.gif, 20.gif, 76.gif, 77.gif, 79.gif",
            "dll.res"
          ]
        },
        "ssdeep": "3:U:U",
        "size": 1,
        "unique_sources": 3008,
        "md5": "c4ca4238a0b923820dcc509a6f75849b",
        "magic": "very short file (no magic)",
        "sigma_analysis_summary": {
          "Sigma Integrated Rule Set (GitHub)": {
            "high": 0,
            "medium": 1,
            "critical": 0,
            "low": 0
          }
        },
        "first_seen_itw_date": 1312952956,
        "sha256": "6b86b273ff34fce19d6b804eff5a3f5747ada4eaa22f1d49c01e52ddb7875b4b",
        "sigma_analysis_results": [
          {
            "match_context": [
              {
                "values": {
                  "TerminalSessionId": "1",
                  "ProcessGuid": "{C784477D-4CA4-642F-E705-000000004400}",
                  "ProcessId": "5188",
                  "Product": "Microsoft\\xae Windows\\xae Operating System",
                  "Description": "Windows PowerShell",
                  "Company": "Microsoft Corporation",
                  "ParentProcessGuid": "{C784477D-2343-6407-6D00-000000004400}",
                  "User": "DESKTOP-B0T93D6\\george",
                  "Hashes": "MD5=95000560239032BC68B4C2FDFCDEF913,SHA256=D3F8FADE829D2B7BD596C4504A6DAE5C034E789B6A3DEFBE013BDA7D14466677,IMPHASH=741776AACCFC5B71FF59832DCDCACE0F",
                  "OriginalFileName": "PowerShell.EXE",
                  "ParentImage": "C:\\Windows\\explorer.exe",
                  "FileVersion": "10.0.17134.1 (WinBuild.160101.0800)",
                  "ParentProcessId": "5060",
                  "CurrentDirectory": "C:\\Users\\george\\Desktop\\",
                  "CommandLine": "\"C:\\Windows\\System32\\WindowsPowerShell\\v1.0\\powershell.exe\" -noLogo -ExecutionPolicy unrestricted -file \"C:\\Users\\george\\Desktop\\qvqtgiye.nrn.ps1\"",
                  "EventID": "1",
                  "LogonGuid": "C784477D-2342-6407-6DA9-030000000000",
                  "LogonId": "239981",
                  "Image": "C:\\Windows\\System32\\WindowsPowerShell\\v1.0\\powershell.exe",
                  "IntegrityLevel": "High",
                  "ParentCommandLine": "C:\\Windows\\Explorer.EXE",
                  "UtcTime": "1680821412",
                  "RuleName": "-"
                }
              }
            ],
            "rule_level": "medium",
            "rule_description": "Detects use of executionpolicy option to set insecure policies",
            "rule_source": "Sigma Integrated Rule Set (GitHub)",
            "rule_title": "Change PowerShell Policies to an Insecure Level",
            "rule_id": "06b48fa7870d38bdf92b4d4a9b9c4a4df779bd405fdc5ba0e70911df20027ce1",
            "rule_author": "frack113"
          }
        ],
        "known_distributors": {
          "distributors": [
            "Microsoft",
            "Linux",
            "GATE GLOBAL UAB"
          ],
          "filenames": [
            "charset.bin",
            "install_mode-6b86b273ff34fce19d6b804eff5a3f5747ada4eaa22f1d49c01e52ddb7875b4b",
            "content-rev.txt",
            "_7CDCB78416789116027EB5D5D361D6AF",
            "echo_it-IT_hq.th",
            "LocVersion.txt",
            "corlexa_it-IT_lq_echo.th-6b86b273ff34fce19d6b804eff5a3f5747ada4eaa22f1d49c01e52ddb7875b4b",
            "COUNTER",
            "BioInit.dat",
            "_31979DFA4CB590A01D811685E96AC6FE",
            "version",
            "corlexa_it-IT_lq_echo.th",
            "corlexa_de-DE_hq_echo.th",
            "cortana_en-US_hq.uth",
            "corlexa_it-IT_hq_echo.th",
            "_EF315D1185CBCE98B065C32959E7C5AC",
            "FWUpdate.dat",
            "symsrv.yes-6b86b273ff34fce19d6b804eff5a3f5747ada4eaa22f1d49c01e52ddb7875b4b",
            "a.png",
            "format",
            "lastsynchronized",
            "_E5418E89493B2F52593EE6B1ACC5AFF2",
            "install_mode",
            "symsrv.yes"
          ],
          "products": [
            "cos-cloud-cos-arm64-lm-stable-121-18867-381-35",
            "Surface Laptop 5",
            "Papers, Please",
            "cos-cloud-cos-121-18867-381-56",
            "cos-cloud-cos-dev-133-19672-0-0",
            "cos-cloud-cos-arm64-stable-121-18867-381-35",
            "cos-cloud-cos-arm64-lm-beta-129-19506-0-115",
            "Surface Pro 4 Drivers and Firmware",
            "cos-cloud-cos-arm64-117-18613-534-53",
            "sailfish for Pixel (OPM1.171019.021)",
            "Surface Pro 9 with Intel Processor Drivers and Firmware",
            "cos-cloud-cos-arm64-lm-stable-121-18867-381-56",
            "Gate: Trade BTC & ETH",
            "Remote Tools for Visual Studio",
            "cos-cloud-cos-arm64-dev-133-19672-0-0",
            "cos-cloud-cos-beta-129-19506-0-115",
            "Elin",
            "Once Human",
            "cos-cloud-cos-125-19216-220-106",
            "cos-cloud-cos-arm64-beta-129-19506-0-66",
            "cos-cloud-cos-arm64-lm-dev-133-19619-0-0",
            "Forensic Tools",
            "cos-cloud-cos-arm64-dev-133-19619-0-0",
            "cos-cloud-cos-arm64-lm-121-18867-381-56",
            "Caine Linux",
            "DosBox",
            "cos-cloud-cos-arm64-lm-125-19216-220-106",
            "cos-cloud-cos-arm64-beta-129-19506-0-115",
            "Red Hat Enterprise Linux",
            "Surface Pro 7 Drivers and Firmware",
            "cos-cloud-cos-stable-121-18867-381-35",
            "Surface Book 2 Drivers and Firmware",
            "cos-cloud-cos-arm64-lm-125-19216-220-57",
            "Surface Laptop 4 with Intel Processor Drivers and Firmware",
            "cos-cloud-cos-arm64-125-19216-220-106",
            "cos-cloud-cos-dev-133-19619-0-0",
            "cos-cloud-cos-arm64-stable-121-18867-381-56",
            "cos-cloud-cos-stable-121-18867-381-56",
            "Figment- Journey Into the Mind",
            "cos-cloud-cos-beta-129-19506-0-66",
            "cos-cloud-cos-arm64-lm-dev-133-19672-0-0",
            "DramaBox - Stream Drama Shorts",
            "cos-cloud-cos-arm64-113-18244-582-55",
            "Gate.io - Buy Bitcoin & Crypto",
            "ryu for Pixel C",
            "Surface Pro 8 Drivers and Firmware",
            "ExTiX",
            "Red Hat Linux 8.0 Professional",
            "angler for Nexus 6P",
            "bullhead for Nexus 5X",
            "cos-cloud-cos-arm64-lm-beta-129-19506-0-66",
            "Snes9x",
            "cos-cloud-cos-arm64-lm-117-18613-534-53",
            "OpenOffice",
            "cos-cloud-cos-arm64-121-18867-381-56",
            "Candy Crush Soda Saga",
            "Phase 10: World Tour",
            "Gate.io:Trade BTC & ETH",
            "cos-cloud-cos-arm64-lm-117-18613-534-36",
            "cos-cloud-cos-arm64-lm-121-18867-381-35"
          ],
          "data_sources": [
            "Microsoft Corporation",
            "HashDB",
            "National Software Reference Library (NSRL)",
            "monitor_hashdb_linux",
            "monitor_hashdb_microsoft"
          ]
        },
        "total_votes": {
          "harmless": 51,
          "malicious": 53
        },
        "type_extension": "txt",
        "saferpickle": {
          "classification": "benign",
          "justification": null
        },
        "reputation": 350,
        "sha1": "356a192b7913b04c54574d18c28d46e6395428ab",
        "last_modification_date": 1779065222,
        "last_analysis_date": 1778997328
      }
    }
  }
]
```
