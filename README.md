# Okta AI Identity Audit Lab

![Okta](https://img.shields.io/badge/Okta-007DC1?style=flat-square&logo=okta&logoColor=white)
![OAuth 2.0](https://img.shields.io/badge/OAuth_2.0-Identity-blue?style=flat-square)
![Python](https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white)
![Least Privilege](https://img.shields.io/badge/Least_Privilege-Access_Governance-green?style=flat-square)

## Overview

I completed this lab to learn how IAM controls apply to identities used by AI applications and agents.

Using Okta, I configured an OAuth application, reviewed granted scopes, documented human ownership, and checked a mock service account’s group memberships. I then used Python running in Docker to audit a manually recorded inventory and detect an unnecessary OAuth scope.

This project demonstrates configuration review, least privilege, ownership tracking, and automated audit checks for a simulated AI workload.

## Scenario

A fictional organization is preparing an AI assistant integration. The IAM team needs to answer:

* What identities support the application?
* Who is accountable for each identity?
* What access has been granted?
* Does that access match the approved business requirement?
* What evidence is available to support the review?

The approved OAuth scope for this exercise was `okta.users.read`. This was a mock requirement established for the lab.

## Tools Used

| Tool | Purpose |
|---|---|
| Okta Admin Console | Configure the OAuth application and review scopes, roles, groups, and API tokens |
| OAuth 2.0 | Review application identity and granted scopes |
| Public/private key authentication | Configure the application’s authentication method |
| Python | Compare inventory records against approved access and ownership requirements |
| Docker | Run Python locally without installing it directly on macOS |
| JSON | Store the inventory and ownership records |
| Okta System Log | Investigate group membership changes |

## Identities Reviewed

| Identity | Type | Human Owner | Approved Access |
|---|---|---|---|
| LAB AI Document Assistant | OAuth API Services client | Keneisha Newman | `okta.users.read` |
| svc.ai.inventory@example.com | Test user representing a service account | Keneisha Newman | AI Inventory Readers membership |

Ownership was recorded in a separate inventory. The service account and OAuth client were separate test identities.

## Lab Walkthrough

### 1. Configure the OAuth Application

Created an API Services application named **LAB AI Document Assistant**.

Configured:

* Public/private key authentication
* An active public key
* The approved `okta.users.read` scope
* No administrative roles

The private key was saved locally and excluded from the project.

### 2. Review and Remove an Extra Scope

Temporarily granted `okta.groups.read` to introduce a controlled audit finding.

Compared the granted scopes with the approved baseline and revoked the unnecessary scope.

| Scope | Approved | Final State |
|---|---|---|
| `okta.users.read` | Yes | Granted |
| `okta.groups.read` | No | Revoked |

This demonstrated that read access can still exceed business requirements.

Granted scopes alone do not prove effective API access. No administrative roles were assigned, and API authorization was not tested.

### 3. Review the Mock Service Account

Created an active test account named **AI Inventory Service**.

Used two ordinary test groups:

* AI Inventory Readers
* AI Inventory Editors

The final membership review confirmed that Readers was present and Editors was absent.

These groups represented the lab’s approved and unnecessary memberships. They were not connected to actual application read or edit permissions.

### 4. Review API Tokens

Checked the API-token inventory in the Okta console.

No API tokens were listed during the review. No new token was created.

### 5. Create the Local Inventory

Manually recorded the Okta configuration in JSON files:

* `okta_inventory.json`
* `okta_owners.json`

The records included identity type, owner, granted access, approved access, and status.

Last-use information was left unknown because reliable usage evidence had not been verified.

### 6. Run the Baseline Audit

Ran the Python audit script through Docker against the recorded inventory.

| Metric | Result |
|---|---|
| Identities inventoried | 2 |
| Identities recorded as active | 2 |
| Findings | 2 |
| Finding type | USAGE_UNKNOWN |

Both identities had documented owners and recorded access matching the approved baseline.

The two findings remained open because last use had not been verified.

### 7. Test Excess-Permission Detection

Created a separate local test inventory containing the extra `okta.groups.read` scope.

Ran the audit against that test copy.

| Identity | Finding | Evidence |
|---|---|---|
| OKTA001 | EXCESS_PERMISSION | `okta.groups.read` exceeded the approved baseline |
| OKTA001 | USAGE_UNKNOWN | No verified last-use date |
| OKTA002 | USAGE_UNKNOWN | No verified last-use date |

The test produced **3 findings**, confirming that the script detected the extra scope.

This test changed only the local inventory copy. It did not change Okta.

## Results

| Audit Area | Outcome |
|---|---|
| OAuth scopes | Extra scope revoked; approved scope retained |
| Human ownership | Recorded for both identities |
| Service account membership | Readers present; Editors absent |
| API-token inventory | No tokens listed during review |
| Automated detection | Extra OAuth scope detected in the test inventory |
| Usage evidence | Two findings retained as unresolved |
| Historical removal evidence | Account-specific removal event not conclusively verified |

## What I Learned

* AI application identities need documented human accountability.
* Read access can be excessive when it has no approved business purpose.
* Granted scopes and effective permissions are different audit questions.
* Group names do not establish application permissions.
* Missing usage evidence does not prove that an identity is inactive.
* Current-state verification and historical change evidence support different conclusions.
* Automated checks depend on accurate inventory and an approved access baseline.

## Project Limitations

This lab represented an AI workload; it did not run an AI model or autonomous agent.

The inventory was recorded manually. The Python script did not collect configuration directly from Okta.

Authentication and effective API access were not tested. Last-use evidence remained unverified, and the account-specific group removal event could not be conclusively linked.

## Next Steps

* Collect inventory through authorized Okta APIs.
* Test successful authentication and effective read access.
* Verify that unauthorized operations are denied.
* Add reliable usage evidence.
* Test ownership transfer when an accountable employee leaves.

## Career Connection

This project builds on my experience with access reviews and user lifecycle management.

My goal is to apply those same ownership, least-privilege, and audit practices to the identities used by AI applications and agents.
