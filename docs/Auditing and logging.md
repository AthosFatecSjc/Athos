# System Logging for Critical Operations

## Summary

As an administrator of the Tecsys energy analytics system, I want the system to record logs of critical operations performed on the platform so that it is possible to technically monitor the application, record basic access events, and identify failures or relevant system events.

## 1. Introduction

### 1.1 Business Logic and Rules

The system must automatically record logs of relevant events that occur during platform usage, without requiring manual intervention.

The records must contain only the information strictly necessary for security, technical monitoring, traceability, and minimum application governance, in accordance with the principles of necessity, purpose limitation, minimization, and security in the processing of personal data.

Each log must record enough information to identify:

- when the action occurred
- which operation was performed
- what the result of the operation was
- which user or process performed the action, pseudonymized when applicable
- minimal additional information about the event

Logs must not be used for excessive monitoring of user behavior, but only for legitimate purposes related to security, stability, traceability, and investigation of failures or relevant incidents.

### 1.2 Purpose of Logs

Log records are primarily intended to ensure:

- technical monitoring of application behavior
- recording of basic system access events
- identification of failures or unexpected behavior
- investigation of technical or security incidents
- maintenance of an auditable trail of the most relevant administrative operations

Log visualization must be restricted to users with administrative permissions compatible with this purpose.

Whenever logs are accessed, the access must respect the principle of necessity and, when applicable, should also be recorded by the system.

### 1.3 Log Classification

For organization and minimization purposes, system records must be classified into two main groups:

**Technical Logs**  
Intended for operational monitoring of the application, failures, authentication, and execution of automated processes.

**Audit Logs**  
Intended for recording administrative actions and operations that affect access, user data, or platform compliance.

This separation is intended to facilitate querying, retention, and access control.

## 2. System Log Recording

The system must record logs for critical events related to:

- user authentication
- user management
- execution of automated system processes

These records must be stored in a dedicated logging structure, logically separated from the application's main database.

### 2.1 Events That Must Generate Logs

#### System Access

- login attempt
- successful login
- failed login

#### User Management

- new user registration
- user approval by administrator
- user rejection by administrator
- editing of user data
- user anonymization
- granting or removal of administrative permission

#### System Processes

- start of ANEEL data extraction
- completion of data extraction
- failure in data extraction

### 2.2 Log Minimization Rules

Logs must record only events relevant to technical monitoring, security, auditing, and basic governance.

Trivial events or common UI interactions must not generate logs.

Logs must not store:

- plaintext passwords
- password hashes in responses or descriptions
- authentication tokens
- session cookies
- internal application secrets
- full request or response payloads containing personal data beyond what is strictly necessary

Whenever possible, additional fields must be masked, reduced, or pseudonymized.

## 3. Log Record Structure

Each record must contain at least the following information:

| Field | Type | Required | Notes |
|---|---|---|---|
| `timestamp` | DateTime | Yes | Date and time of the operation |
| `actorRef` | Text (UUID) | No | Pseudonymized identifier of the user, when there is a human actor |
| `sourceType` | Text | Yes | `USER`, `SYSTEM`, or `JOB` |
| `event` | Text | Yes | Event type |
| `result` | Enum | Yes | `SUCCESS` or `FAIL` |
| `description` | Text | Yes | Short event description |
| `metadata` | JSON or Text | No | Minimal and controlled additional information |
| `targetRef` | Text (UUID) | No | Pseudonymized reference to the target user, when applicable |
| `createdByModule` | Text | No | Module responsible for creating the record |

### Event Types

Examples of values for the `event` field:

- `LOGIN_ATTEMPT`
- `LOGIN_SUCCESS`
- `LOGIN_FAIL`
- `USER_REGISTER`
- `USER_APPROVED`
- `USER_REJECTED`
- `USER_EDITED`
- `USER_ANONYMIZED`
- `ADMIN_ROLE_GRANTED`
- `ADMIN_ROLE_REMOVED`
- `ANEEL_EXTRACTION_START`
- `ANEEL_EXTRACTION_SUCCESS`
- `ANEEL_EXTRACTION_FAIL`

### 3.1 User Identification in Logs

Log records must not directly store identifiable personal data such as name or email, except in strictly justified exceptional cases.

To reduce the risk of undue exposure, the system must record a pseudonymized user identifier called `actorRef`, preferably generated using a UUID or equivalent technical reference.

When there is a target user affected by an administrative action, the system may also record a separate pseudonymized reference such as `targetRef`.

Any link between these pseudonymized identifiers and the data subject must remain segregated, with restricted access only for authorized administrative profiles and only when there is a legitimate need.

This approach reinforces the principles of:

- security
- necessity
- minimization
- accountability

### 3.2 Allowed Content in `description` and `metadata`

The `description` and `metadata` fields must contain only the minimum information necessary to understand the event.

They must not be used to fully replicate the operation data.

It is allowed to record, for example:

- technical error code
- name of the automated process
- summarized step of the operation
- reason for administrative rejection
- pseudonymized technical identifiers

It is not allowed to record:

- password
- token
- full email address
- full name of the data subject
- complete form content
- full request and response payloads

## 4. Log Persistence

Logs must be stored in a structure separated from the application's main database, using a simple and low-operational-cost approach.

For this project phase, there is no need for dedicated or highly complex infrastructure exclusively for logs.

The solution may use:

- a separate collection in a non-relational database
- a separate table in a relational database

The choice of technology should prioritize:

- simplicity of implementation
- ease of querying
- low maintenance cost
- logical separation from the main database

Each recorded event must generate a new document or record containing the information related to the performed operation.

## 5. Log Querying

The system must allow authorized administrators to query log records.

Log visualization must be restricted to users with administrative permissions compatible with this function.

The query must display at least:

- operation date and time
- pseudonymized actor identifier (`actorRef`), when applicable
- event type
- operation result
- short event description

When applicable, it may also display:

- pseudonymized reference of the action target (`targetRef`)
- module responsible for the event
- minimal additional technical information

At this initial stage, there is no need to build a complex auditing system. A simple, organized, filterable, and searchable listing is sufficient for the project's goals.

Whenever possible, queries to administrative logs should generate a minimum access trail, especially when there is a possibility of indirect re-identification.

## 6. Log Retention and Volume

Log records can grow quickly over time. For this reason, the system must record only events relevant to technical monitoring, security, and basic governance.

Trivial events or common UI interactions must not generate logs.

### 6.1 Retention Policy

For this project phase, it is recommended to keep log history for approximately 6 months.

Initial policy application:

- authentication logs: 6 months
- administrative and audit logs: 6 months
- automated process execution logs: 6 months

After this period, records may be:

- automatically removed
- securely discarded according to the application's policy

Whenever possible, historical records should remain in pseudonymized form.

### 6.2 Simplified Storage Strategy

To avoid bottlenecks and unnecessary database costs, the system must adopt a simple log storage strategy.

Recommended best practices for the project:

- keep logs in a structure separate from the main database
- record only truly relevant events
- apply an automatic 6-month retention policy
- avoid storing identifiable personal data
- use simple queries restricted to authorized administrators
- control or limit data subject re-identification to justified cases

At this stage, it is not necessary to use dedicated tools such as Redis, queues, or external observability platforms unless required by future technical needs.

## 7. Data Structure Example

Example of a log structure stored in the database:

```json
{
  "timestamp": "2026-03-08T18:40:00",
  "actorRef": "4f8c0b7a-2b89-4c9d-90b4-4f4a72c5c711",
  "sourceType": "USER",
  "event": "LOGIN_SUCCESS",
  "result": "SUCCESS",
  "description": "Login completed successfully",
  "metadata": {
    "module": "auth"
  }
}
```

## 8. Acceptance Criteria

- The system must record logs for critical system events.
- The system must record logs for successful and failed logins.
- The system must record logs for user registration, approval, rejection, editing, and anonymization.
- The system must record logs for ANEEL data collection execution.
- Each log must contain at least the date, event type, and operation result.
- Logs must use a pseudonymized user identifier (`actorRef`) when there is a human actor.
- Logs must not directly store personal data such as name or email, except in duly justified exceptional cases.
- Logs must not store passwords, tokens, session cookies, internal secrets, or full payloads containing unnecessary personal data.
- Logs must have an approximate retention period of 6 months.
- Log querying must be restricted to authorized administrator users.
- The system must use a simple solution, logically separated from the main database, to store logs.
- The system must allow distinction between technical logs and audit logs.
- The system must record the reason for administrative events that require justification, such as user rejection, when applicable.

## 9. Links and References

### 9.1 References

| Item | Link |
|---|---|
| Prototype | [Figma](https://www.figma.com/design/lH1iaR2cBfTC9PUxxo93dX/Sem-t%C3%ADtulo?node-id=0-1&m=dev) |
| Epic (Jira) | [ATS-4: Auditing and logging](https://athos-software.atlassian.net/browse/ATS-4) |
