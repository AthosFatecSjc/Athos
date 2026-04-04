# User Registration and Access Approval

## Summary

As a user of the Tecsys energy analytics system, I want to register by providing basic platform access information so that I can request access to the system and use the analytics features after administrative approval.

## 1. Introduction

### 1.1 Business Logic and Rules

The system must allow new users to self-register on the platform by providing only the data strictly necessary for identification, authentication, and use of the solution.

After registration, the user must not receive immediate access to the system. The account must remain in `PENDING` status until an administrator approves or rejects the request.

This process ensures that only authorized users can access the platform's features.

For this initial phase of the project, user approval will be performed manually by an administrator due to the expected low number of users and the need for stricter access control.

Every administrative decision related to approval, rejection, granting, or removal of administrative privileges must record at least:

- administrator identifier
- date and time of the action
- type of action performed
- justification, when applicable

### 1.2 Access Control

System access control is managed by two main mechanisms: access status and administrative permission.

#### Access Status

Defines whether the user can access the system.

| Status | Description |
|---|---|
| `PENDING` | Waiting for administrator approval |
| `ACTIVE` | Access granted |
| `REJECTED` | Access denied |

#### Administrative Permission

Defines whether the user has administrative privileges within the platform.

| Permission | Description |
|---|---|
| `User` | Standard access to platform features |
| `Administrator` | Can manage users and view system logs |

Users with administrative permission may grant or remove this permission from other users, and this operation must be logged in the audit log.

#### Initial Administrator

The initial system administrator must be created directly in the database or through a secure application deployment routine, without requiring registration through the interface.

The initial credential must not appear in source code, public documentation, or versioned scripts.

The initial password must be defined through an environment variable, secure provisioning mechanism, or controlled generation during deployment.

On first login, the initial administrator must be required to change their password.

### 1.3 Data Protection and Transparency (LGPD)

The system must comply with the principles of purpose limitation, adequacy, necessity, security, transparency, and accountability in the processing of personal data.

User registration must collect only the data strictly necessary for identification, authentication, and use of the platform.

The Privacy Notice must be presented to the user clearly and accessibly at the time of registration, containing at least:

- processing purposes
- applicable legal bases
- retention period
- sharing information
- data subject rights
- contact channel

The record of reading the Privacy Notice serves the purpose of awareness and transparency and must not be used as an independent legal basis for processing the data necessary for platform operation.

Acceptance of the Terms of Use is mandatory to complete registration, as it is a condition for using the platform.

Consent for product updates, alerts, invitations, and email marketing must be optional, clearly separated from the other terms, and revocable at any time without affecting access to the platform's core features.

#### Mandatory Terms

Mandatory terms must be presented before registration is completed.

**Terms of Use**  
Defines the rules for using the platform, user responsibilities, access conditions, prohibited conduct, and service limitations.

**Privacy Notice**  
Explains which data is processed, the purposes, legal bases, retention, sharing, data subject rights, and contact channel.

#### Optional Terms

Optional terms must not prevent the user from accessing the system.

**Product Updates / Email Marketing Communications**  
Allows the sending of product updates, alerts, invitations, and promotional communications. The user may accept or refuse without losing access and may later revoke this choice.

## 2. User Module Features

The user management module must support the following operations:

- user registration
- viewing registered users
- search and filters
- user self-editing of personal data
- administrator approval or rejection of access requests
- user anonymization by administrators
- recording of acceptances, acknowledgements, and consents
- term versioning
- revocation of optional consent
- handling of data subject requests related to personal data

## 3. User Registration

The system must allow new users to register by entering their basic information.

After registration is submitted, the system must record the user with `PENDING` status, awaiting administrator approval.

At the time of registration, the system must:

- record acceptance of the current version of the Terms of Use
- record acknowledgement of the current version of the Privacy Notice
- separately record any optional consent for marketing communications
- store the date, time, and origin of the acceptance, acknowledgement, or consent, including IP address or an equivalent technical identifier when necessary for audit and security purposes

The IP address recorded for acceptance, acknowledgement, or consent must be treated as personal data and disclosed in the Privacy Notice, including its purpose and retention period.

### 3.1 Registration Fields

| Field | Type | Required | Notes |
|---|---|---|---|
| Name | Text | Yes | User's full name |
| Email | Text | Yes | Used for login and operational communication |
| Password | Text | Yes | Stored only as a secure cryptographic hash |
| Phone | Text | No | Optional field |
| Registration Date | DateTime | Yes | Generated automatically |
| Status | Enum | Yes | `PENDING`, `ACTIVE`, or `REJECTED` |
| Administrative Permission | Boolean | Yes | Defines whether the user has administrator privileges |

## 4. User Page - List View

The list page must display registered users according to the authorized access profile.

Each record must display:

- name
- email
- phone
- status
- registration date

Data visibility must respect access necessity and the authenticated user's profile.

### 4.1 Filters

The list must allow filtering by:

- name
- status

### 4.2 Search

There must be a search field to locate users by:

- name

## 5. Action Buttons

The user list must contain action buttons for record management.

| Button | Action | Description |
|---|---|---|
| View | Display user data | Opens the view screen |
| Edit | Change information | Allows data modification |
| Approve | Activate user | Changes status to `ACTIVE` |
| Reject | Deny access | Changes status to `REJECTED` |
| Anonymize | Remove user identification | Anonymizes personal data |

Administrative actions must require authentication and authorization compatible with the user's profile.

## 6. User Approval

Users registered with `PENDING` status must be reviewed by an administrator.

The administrator may:

- approve the registration, changing the status to `ACTIVE`
- reject the registration, changing the status to `REJECTED`

In case of rejection, the system must require the reason for the decision to be recorded.

Every approval or rejection decision must store:

- identifier of the responsible administrator
- date and time of the decision
- action performed
- rejection justification, when applicable

Only users with `ACTIVE` status may authenticate and access the system.

## 7. User Editing

Users may edit their own data in the **My Account** screen.

Fields that may be changed directly:

- name
- phone

The user's email must not be freely changed through simple editing.

Email correction or change must happen through an authenticated and validated flow, with identity confirmation and change logging, ensuring security and the right to correct personal data.

Fields that may not be freely changed:

- registration date
- account status

Administrators may also edit registration information when necessary, with mandatory audit log recording of the operation.

## 8. User Anonymization

Administrators may anonymize users in the system.

Before anonymization, the system must display the following confirmation message:

`Are you sure you want to anonymize this user?`

After confirmation:

- the user's personal data must be anonymized in the active system database
- records strictly necessary for audit, security, and compliance may be preserved
- the operation must be recorded in the logging system
- anonymization must remain consistent with historical records and term acceptances

Anonymization must be executed in a way that prevents re-identification of the data subject in the active database, preserving only the information strictly necessary for audit, security, fraud prevention, and proof of compliance.

The anonymization operation must record:

- person responsible for the execution
- date and time
- identifier of the affected user
- legal or operational basis for the action
- audit log record reference

### 8.1 Deletion and Retention

When the data subject requests deletion, the data must be removed from the active production database or anonymized, subject to legal retention requirements.

For this project, the adopted strategy will be anonymization of the user's registration data in the active database, preserving only records strictly necessary for audit, security, and compliance.

Initial retention policy for the project:

- security and audit logs: 6 months
- acceptance, acknowledgement, and consent records: while there is an active relationship with the user and for up to 5 years for academic, audit, and documentary evidence purposes
- backups: 90 days
- rejected registrations without activation: anonymization after the period defined by the project's internal policy

These periods may be revised in a future version of the retention policy, provided that the principles of necessity and data minimization are maintained.

### 8.2 Deletion and Backup Restoration

User anonymization must immediately affect the system's active database.

When backups exist from before anonymization, restoring the database must not automatically reactivate the user's original data.

To ensure consistency between technical restoration and compliance with data protection rules, the system may include:

- anonymization routine in the main database
- anonymization record table or collection
- reapplication of anonymization after backup restoration
- defined backup retention period
- secure disposal of backups after the retention period

Backup restoration must not result in the automatic reactivation of data that has already been anonymized or removed from the active database.

The system must provide a technical mechanism to reapply anonymization records after restoration processes.

## 9. Logging

The following operations must generate log records:

- user registration
- login attempt
- successful login
- user approval
- user rejection
- data editing
- user anonymization
- granting or removal of administrative privileges
- acceptance, acknowledgement, refusal, or revocation related to terms and optional communications

Logged operations must support auditability and traceability without exceeding what is necessary for that purpose.

Logs must not store:

- plaintext passwords
- authentication tokens
- system secrets
- full payloads containing personal data beyond what is strictly necessary

Whenever possible, personal data in logs must be masked or pseudonymized.

Access to logs must be restricted to authorized users with a compatible administrative profile.

## 10. Term Versioning and User Records

The system must maintain version control of the terms presented to the user, preserving the history of previous versions and their respective records.

When a new version of a mandatory term becomes effective, the user must accept it again to continue using the platform.

When the new version refers to an optional term, re-acceptance may be requested without blocking access.

### 10.1 Minimum Term Versioning Structure

Each term must contain at least:

- `termType`
- `version`
- `isMandatory`
- `effectiveAt`
- `contentHash`
- `status`

#### Table 1 - Term Versioning

| Field | Meaning | Example | Purpose in the system |
|---|---|---|---|
| `termType` | Type of term | `TERMS_OF_USE` / `PRIVACY_POLICY` | Identifies the document |
| `version` | Term version number | `1`, `2`, `3` | Controls changes over time |
| `isMandatory` | Whether the term is mandatory | `true` / `false` | Blocks access if a mandatory term is not accepted |
| `effectiveAt` | Date when the term became effective | `2026-01-01` | Determines which version was active at that time |
| `contentHash` | Identifier of the document content | text hash | Ensures the text was not changed afterward |
| `status` | Term status | `ACTIVE` / `INACTIVE` | Indicates which version is currently in use |

#### Example

| termType | version | isMandatory | effectiveAt | status |
|---|---|---|---|---|
| `TERMS_OF_USE` | `1` | `true` | `2025-01-01` | `INACTIVE` |
| `TERMS_OF_USE` | `2` | `true` | `2026-01-01` | `ACTIVE` |
| `PRIVACY_POLICY` | `1` | `true` | `2026-01-01` | `ACTIVE` |

### 10.2 Minimum User Acceptance Structure

Each record related to terms and communications must store:

- `userId`
- `termId`
- `termVersion`
- `recordType` (`ACCEPTANCE`, `ACKNOWLEDGEMENT`, `CONSENT`)
- `recordedAt`
- `recordedFromIp` or equivalent
- `status` (`ACCEPTED`, `DECLINED`, `REVOKED`, when applicable)

The system must distinguish between:

- mandatory acceptance of the Terms of Use
- acknowledgement of the Privacy Notice
- optional consent for promotional communications

#### Table 2 - User Acceptance Record

| Field | Meaning | Example | Purpose in the system |
|---|---|---|---|
| `userId` | User identifier | user UUID | Identifies who accepted the term |
| `termId / termType` | Which term was accepted | `TERMS_OF_USE` | Identifies the document |
| `termVersion` | Accepted term version | `2` | Identifies which version the user accepted |
| `acceptedAt` | Date and time of acceptance | `2026-03-02 14:30` | Records when acceptance occurred |
| `acceptedFromIp` | IP address of acceptance | `177.55.23.8` | Audit and security |
| `status` | Acceptance status | `ACCEPTED` / `DECLINED` | Indicates whether it was accepted or refused |

#### Example

| userId | termType | termVersion | acceptedAt | status |
|---|---|---|---|---|
| `8a7c22f` | `TERMS_OF_USE` | `2` | `2026-03-02 14:30` | `ACCEPTED` |
| `8a7c22f` | `PRIVACY_POLICY` | `1` | `2026-03-02 14:30` | `ACCEPTED` |

### 10.3 Versioning Rules

- new mandatory term: the user must re-accept before continuing
- new optional term: the user is invited to review it, without access being blocked
- old versions must not be deleted
- previous records must not be overwritten
- the system must preserve a history of which version was presented, accepted, declined, or revoked, and when

### 10.4 Consent Revocation

The user may revoke consent related to optional communications at any time, without affecting their account or access to the platform's essential features.

The revocation must be recorded with date, time, and user identifier.

## 11. Data Structure Example

Example of a user structure stored in the database:

```json
{
  "id": "uuid",
  "name": "Joao Silva",
  "email": "joao@email.com",
  "phone": "11999999999",
  "passwordHash": "bcrypt_hash",
  "status": "PENDING",
  "isAdmin": false,
  "createdAt": "2026-03-01T10:20:00"
}
```

## 12. Data Subject Rights

The system must provide a channel or mechanism to handle data subject requests, including:

- confirmation of whether data processing exists
- access to processed personal data
- correction of incomplete, inaccurate, or outdated data
- anonymization or deletion, when applicable
- revocation of consent for optional communications

Data subject requests must be recorded with minimum traceability for proof of fulfillment.

### 12.1 Record of Processing Activities

The project must maintain a minimum documented inventory of personal data processing operations performed in the user module.

This inventory must contain at least:

- collected data
- purpose
- legal basis
- retention period
- access profile
- any sharing
- applied protection measure

## 13. Acceptance Criteria

- The system must allow new user registration.
- Registered users must start with `PENDING` status.
- Administrators must be able to approve or reject users.
- Only users with `ACTIVE` status may access the system.
- The phone field must be optional.
- The password must be stored using a secure cryptographic hash.
- The system must display the Terms of Use and the Privacy Notice before registration is completed.
- The system must separately record acceptance of the Terms of Use, acknowledgement of the Privacy Notice, and optional consent for promotional communications.
- The system must maintain a history of term versions and their respective records.
- In case of a data subject request, registration data must be anonymized in the active database, subject to legal retention requirements.
- All administrative operations must generate log records.
- Every registration rejection must record reason, responsible person, date, and time.
- Email changes must occur through an authenticated and validated flow.
- The system must not log passwords, tokens, or secrets.
- The system must allow revocation of consent for optional communications.
- The initial administrator must be created with secure credentials and mandatory password change on first login.
- The system must have a retention policy for registration data, logs, acceptance records, consent records, and backups.
- The system must provide a mechanism to handle data subject rights.
- The system must maintain a minimum inventory of personal data processing for compliance and documentation purposes.

## 13. Links and References

### 13.1 References

| Item | Link |
|---|---|
| Prototype | Done |
| Epic (Jira) | [ATS-7](https://athos-software.atlassian.net/browse/ATS-7) |
