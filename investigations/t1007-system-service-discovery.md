# T1007 — System Service Discovery Investigation

## Overview

This investigation documents a controlled execution of MITRE ATT&CK technique **T1007 — System Service Discovery** using Atomic Red Team against the ART Workstation. The objective was to generate system discovery activity and evaluate the visibility provided by the Wazuh SIEM environment.

As with the T1003.1 investigation, the experiment also provided an opportunity to validate endpoint telemetry and troubleshoot the lab's logging configuration.

## Objective

The objectives of this experiment were to:

* Execute a controlled Atomic Red Team test for T1007.
* Generate system service discovery activity on the ART Workstation.
* Verify that endpoint telemetry was successfully reaching Wazuh.
* Observe and investigate any resulting Wazuh alerts.
* Validate the lab's endpoint logging configuration.

## Environment

* **SIEM:** Wazuh
* **Endpoint:** ART Workstation
* **Testing Framework:** Atomic Red Team
* **Technique:** T1007 — System Service Discovery
* **Endpoint Telemetry:** Windows Event Logs / Sysmon

## Test Procedure

The Atomic Red Team framework was used to identify and execute the appropriate T1007 test.

### 1. Review available Atomic Red Team tests

The available tests were reviewed using:

```powershell
Invoke-AtomicTest all -ShowDetailsBrief
```

### 2. Check prerequisites

The prerequisites for T1007 were checked using:

```powershell
Invoke-AtomicTest T1007 -CheckPrereqs
```

### 3. Execute the test

The T1007 test was executed using:

```powershell
Invoke-AtomicTest T1007 -TestNumbers 1 -cleanup
```

### 4. Restore the testing environment

The testing session was returned to its original state using:

```powershell
Invoke-AtomicTest T1007-1 -cleanup -Session $sess
```

## Initial Results

The initial execution did not produce the expected telemetry within Wazuh.

This prompted a review of the endpoint's logging and telemetry configuration.

## Troubleshooting

The investigation identified that **Sysmon had not been properly downloaded and configured on the ART Workstation**.

Without the appropriate endpoint telemetry being generated and forwarded, Wazuh did not have the expected event data available for analysis.

Sysmon was subsequently downloaded and configured on the ART Workstation, after which the experiment was rerun.

## Detection

Following the correction of the endpoint telemetry configuration, Wazuh successfully generated a **High-severity security alert** after the Atomic Red Team test was rerun.

[Insert Wazuh alert screenshot here]

The alert demonstrated that endpoint telemetry was successfully reaching Wazuh after the logging configuration was corrected.

However, the available alert information does **not conclusively establish that the alert represented detection of T1007 — System Service Discovery**. Additional examination of the alert details, underlying event data, and associated detection rule would be necessary to confirm the specific technique represented by the alert.

## MITRE ATT&CK Mapping

**T1007 — System Service Discovery**

**Tactic:** Discovery

T1007 involves an adversary enumerating services running on a system. This information can help an attacker understand the environment, identify security software or other services of interest, and inform subsequent actions.

The Atomic Red Team framework was used to generate controlled activity associated with this technique.

## Lessons Learned

This experiment reinforced the importance of validating endpoint telemetry before relying on SIEM alerts for security investigations.

The initial absence of Wazuh data was caused by the incomplete Sysmon configuration on the ART Workstation. Correcting the configuration allowed endpoint telemetry to reach Wazuh and resulted in the generation of a security alert.

The experiment also demonstrated the importance of distinguishing between **an alert being generated** and **a specific attack technique being successfully detected**. A security analyst should examine the underlying event data and detection logic before concluding that a particular technique was identified.

## Key Takeaways

* Endpoint telemetry is fundamental to effective SIEM monitoring.
* Sysmon configuration should be validated before conducting detection testing.
* Controlled attack simulations can help identify gaps in security monitoring.
* A SIEM alert should be investigated rather than automatically assumed to represent detection of the simulated technique.
* Detection validation requires examining the underlying event data and detection logic.
