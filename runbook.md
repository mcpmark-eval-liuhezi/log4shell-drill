# Log4Shell Incident Response Runbook (Tabletop Drill)

## 1. Advisory Facts (from the GitHub Advisory Database)

| Field | Value |
|---|---|
| GHSA ID | `GHSA-jfh8-c2jp-5v3q` |
| CVE | CVE-2021-44228 |
| Severity | **Critical** (CVSS 3.1 score **10.0**, vector `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:C/C:H/I:H/A:H/E:H`) |
| Affected package | `org.apache.logging.log4j:log4j-core` |
| Earliest patched version | **2.15.0** |

> **Database notes:** Any Log4J version prior to v2.15.0 is affected. The 2.15.0 patch was incomplete — the advisory's updated remediation guidance is to upgrade to **2.16.0**, which disables JNDI by default and removes message lookup support. Backports of the fix exist in **2.3.1, 2.12.2, and 2.12.3**. Only `org.apache.logging.log4j:log4j-core` is directly affected; `log4j-api` should be kept at the same version for compatibility. Log4J v1 is EOL and will not be patched.

Advisory link: https://github.com/advisories/GHSA-jfh8-c2jp-5v3q

## 2. Drill Walkthrough

### Step 1 — Detection
- Alert fires from the SCA/dependency scanner flagging `org.apache.logging.log4j:log4j-core` below 2.15.0.
- Triage against the advisory facts above: severity is **Critical** with a CVSS 3.1 score of **10.0** — treat as a P1.
- Confirm the affected artifact: only `log4j-core` is directly affected by this CVE.

### Step 2 — Containment
- Inventory all services that log user-controlled input (HTTP headers, user agents, form fields, exception traces).
- If upgrading immediately is not possible, apply the interim mitigation: ensure no attacker-controlled JNDI lookups can resolve, and ensure the `JndiLookup` class is not loaded.

### Step 3 — Remediation
- Upgrade `log4j-core` to at least **2.15.0** (the earliest patched version). Where possible, go straight to **2.16.0** per the advisory's updated guidance, since the 2.15.0 fix did not disable attacker-controlled JNDI lookups in all situations.
- For older lines, use the backports: 2.3.1, 2.12.2, or 2.12.3.
- Keep `log4j-api` at the same version as `log4j-core`.

### Step 4 — Verification
- Re-run the dependency scan; confirm no findings for GHSA-jfh8-c2jp-5v3q remain.
- Monitor logs for unexpected outbound LDAP/JNDI traffic.

### Step 5 — Post-incident review
- Document time-to-detect, time-to-contain, time-to-remediate.
- Record which services were affected and why the vulnerable version was present.

## 3. CI Drill Component

The `scan-standin.yml` workflow in this repository simulates a long dependency scan (sleeps ~20 minutes). The drill objective is to demonstrate that a stuck scan can be aborted: trigger it manually, confirm it is in progress, then cancel the run.
