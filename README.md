# Investigating IAM Anomalous Grant Findings with Event Threat Detection

A hands-on Google Cloud lab where I triggered a real Event Threat Detection finding, then used Security Command Center and Cloud Logging to tell the difference between normal activity and an actual security incident.

## Scenario
The security team had two IAM-related threat findings to investigate — one turned out to be benign, the other malicious. My job was to recreate the conditions that trigger both, then analyze the evidence to correctly tell them apart, and remediate the real one.

## What I did

### 1. Triggered an anomalous IAM grant
Granted the project Owner role to an external Gmail account (`bad.actor.demo@gmail.com`) — a deliberately risky action, since granting Owner to a principal outside the organization is exactly the kind of behavior Event Threat Detection is built to flag.

### 2. Found the findings in Security Command Center
Filtered Security Command Center's Findings page down to the **Persistence: IAM Anomalous Grant** category and located two findings: one from routine lab provisioning, one from the action I'd just taken.

### 3. Told them apart using the evidence
For each finding, checked the **Principal email** (who granted the role) and the **members** field under Source Properties (who received it):
- The earlier finding involved a principal and grantee both inside the organization — normal provisioning activity
- The later finding showed the Owner role granted to the external `bad.actor.demo@gmail.com` account — confirmed as the genuine incident

### 4. Cross-referenced Cloud Logging
Ran a Logs Explorer query filtered on `resourcemanager.projects.setIamPolicy` and `InsertProjectOwnershipInvite` to find the underlying audit log entry, and confirmed who made the request, who received the grant, and the request's originating IP and user agent.

### 5. Remediated the incident
Went back into IAM and removed the Owner role from the external account, closing the actual exposure.

## Key takeaways
- The same alert category can represent two completely different realities — the finding itself doesn't tell you if it's benign or malicious, the surrounding evidence does
- Principal email and grantee identity are often the fastest way to separate expected internal activity from an external actor gaining unauthorized access
- Cloud Logging audit entries (who, what, from where) are what actually confirm or rule out a theory formed from a Security Command Center finding

## Tools
Google Cloud Security Command Center, Event Threat Detection, Cloud Logging, IAM

---
*Completed as a Google Cloud Skills Boost lab.*
