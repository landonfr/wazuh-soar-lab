# Workflows

## Wazuh-FIM-Triage.json

```mermaid
flowchart LR
    T["Wazuh_Alerts<br/>(webhook trigger)"] --> R["Route_Alert<br/>reads rule.id"]
    R -->|"rule.id = 554<br/>(new file)"| V["VT_Hash_Lookup<br/>VirusTotal v3: file report<br/>for syscheck.sha256_after"]
    V --> D1["Discord_Alert<br/>FIM alert + VT verdict"]
    R -->|"rule.id != 554<br/>(Defender 62123/62125/62126)"| D2["Discord_Defender_Alert"]
```

| Node | App | What it does |
|---|---|---|
| `Wazuh_Alerts` | Webhook trigger | Receives JSON alerts from the Wazuh `shuffle` integration |
| `Route_Alert` | Shuffle Tools | Passes the rule ID through so branch conditions can route on it |
| `VT_Hash_Lookup` | VirusTotal v3 | Looks up the new file's SHA-256 (`$exec.syscheck.sha256_after`) |
| `Discord_Alert` | HTTP POST | Sends the host, rule, file path and VirusTotal result to Discord |
| `Discord_Defender_Alert` | HTTP POST | Sends Windows Defender detections to Discord |

### Importing

1. In Shuffle, go to **Workflows**, choose **Import**, and select this file.
2. Add your **VirusTotal API key** to `VT_Hash_Lookup` (Shuffle stores it as an app authentication).
3. Paste your **Discord webhook URL** into the `url` field of both Discord nodes.
4. Copy the webhook trigger's ID into `wazuh/config/wazuh_cluster/shuffle-integration.xml`, replacing `YOUR-SHUFFLE-WEBHOOK-ID`.

The API key and Discord URLs aren't in the export, and the webhook ID has been replaced with a placeholder.
