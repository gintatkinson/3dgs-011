---
title: "Validate MAC and Physical Address Format"
issue_id: 48
type: "user-story"
generation_mode: "subagent"
spec_source: "RFC 9911"
---

# User Story: Validate MAC and Physical Address Format

## Parent Epic
- [ ] #37 - Common YANG Data Types: Object Identifier and Network Address Types

## Domain Object Mapping
- **Primary Domain Objects:** mac-address, phys-address
- **Actor/Role:** Network Engineer / Device Configuration Tool

## BDD Scenario
**As a** Network Engineer
**I want to** validate MAC and physical address format
**So that** I ensure address values conform to IEEE 802 and hex-string specifications

## UML Sequence Diagram
```mermaid
sequenceDiagram
    autonumber
    actor engineer as "engineer : NetworkEngineer"
    participant addrService as "addrService : AddressValidationService"

    engineer->>addrService: validateAddress(addressType: Type, addressString: String)
    alt [addressType == macAddress]
        addrService->>addrService: checkPattern(addressString: String, pattern: "[0-9a-fA-F]{2}(:[0-9a-fA-F]{2}){5}")
        alt [patternMatches]
            addrService-->engineer: validationResult(true) + canonicalizedLowercase
        else [patternFails]
            addrService-->engineer: validationResult(false, "Invalid MAC format")
        end
    else [addressType == physAddress]
        addrService->>addrService: checkPattern(addressString: String, pattern: "([0-9a-fA-F]{2}(:[0-9a-fA-F]{2})*)?")
        alt [patternMatches]
            addrService-->engineer: validationResult(true) + canonicalizedLowercase
        else [patternFails]
            addrService-->engineer: validationResult(false, "Invalid physical address format")
        end
    end
```

## Required Features Matrix
- [ ] #24 - Represent Physical and MAC Address Values (semantic linkage: behavioral validation of address formats)

## Source References
Structural Schema: ietf-yang-types.yang
Normative Specification: RFC 9911, Section 3
