---
title: "Validate Object Identifier Hierarchical Format"
issue_id: 46
type: "user-story"
generation_mode: "subagent"
spec_source: "RFC 9911"
---

# User Story: Validate Object Identifier Hierarchical Format

## Parent Epic
- [ ] #37 - Common YANG Data Types: Object Identifier and Network Address Types

## Domain Object Mapping
- **Primary Domain Objects:** object-identifier, object-identifier-128
- **Actor/Role:** System Administrator / YANG Schema Validator

## BDD Scenario
**As a** System Administrator
**I want to** validate object identifier values against ASN.1 hierarchical constraints
**So that** I ensure OID values conform to the registration tree rules

## UML Sequence Diagram
```mermaid
sequenceDiagram
    autonumber
    actor admin as "admin : SystemAdministrator"
    participant oidService as "oidService : OIDValidationService"

    admin->>oidService: validateOID(oidString: String)
    oidService->>oidService: parseSubIdentifiers(oidString: String)
    alt [firstArc not in {0,1,2}]
        oidService-->admin: validationResult(false, "Invalid first arc")
    else [secondArc > 39 when firstArc in {0,1}]
        oidService-->admin: validationResult(false, "Invalid second arc")
    else [subIdentifierCount < 2]
        oidService-->admin: validationResult(false, "Insufficient sub-identifiers")
    else [subIdentifierExceedsMax]
        oidService-->admin: validationResult(false, "Sub-identifier overflow")
    else [all constraints satisfied]
        oidService-->admin: validationResult(true, "Valid OID")
    end
```

## Required Features Matrix
- [ ] #23 - Represent Object Identifier Registration Hierarchy (semantic linkage: behavioral validation of OID format)

## Source References
Structural Schema: ietf-yang-types.yang
Normative Specification: RFC 9911, Section 3
