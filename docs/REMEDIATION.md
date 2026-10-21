# Remediation Note: Log4Shell (CVE-2021-44228)

Facts quoted from the GitHub Security Advisory Database record: https://github.com/advisories/GHSA-jfh8-c2jp-5v3q

- **GHSA ID:** `GHSA-jfh8-c2jp-5v3q`
- **CVE:** CVE-2021-44228
- **Severity:** Critical (CVSS 3.1 score 10.0)
- **Affected package:** `org.apache.logging.log4j:log4j-core`
- **First patched version:** **2.15.0** (advisory updated guidance recommends 2.16.0)

## Remediation action

Upgrade `org.apache.logging.log4j:log4j-core` from any version prior to 2.15.0 to a patched version — **2.15.0 at minimum** (the first version listed as patched for this issue), and **2.16.0 where possible**, since 2.16.0 disables JNDI by default and completely removes support for message lookups. Keep `org.apache.logging.log4j:log4j-api` aligned at the same version as `log4j-core`.

Log4j 1.x is End Of Life and will not receive patches for this issue — migrate to 2.16.0 where possible.
