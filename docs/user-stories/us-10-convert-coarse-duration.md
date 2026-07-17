---
title: "Convert and Validate Coarse Time Duration Units"
issue_id: 56
type: "user-story"
generation_mode: "subagent"
spec_source: "RFC 9911"
---

# User Story: Convert and Validate Coarse Time Duration Units

## Parent Epic
- [ ] #39 - Common YANG Data Types: Time Duration Measurement Types

## Domain Object Mapping
- **Primary Domain Objects:** hours32, minutes32, seconds32
- **Actor/Role:** Configuration Manager / Timeout Handler

## BDD Scenario
**As a** Configuration Manager
**I want to** convert between hours, minutes, and seconds duration units and validate range constraints
**So that** I can store and retrieve duration values in the appropriate unit with overflow protection

## UML Sequence Diagram
```mermaid
sequenceDiagram
    autonumber
    actor config as "config : ConfigurationManager"
    participant durationService as "durationService : DurationConversionService"

    config->>durationService: convert(value: Integer, fromUnit: String, toUnit: String)
    durationService->>durationService: computeConversion(value: Integer, fromUnit: String, toUnit: String)
    alt [rangeRestricted]
        durationService->>durationService: checkRange(convertedValue: Integer, range: String)
        alt [outOfRange(negative) && range == "0..max"]
            durationService-->config: convertResult(null, "Negative value not allowed")
        else
            durationService-->config: convertResult(convertedValue, null)
        end
    else
        durationService-->config: convertResult(convertedValue, null)
    end
```

## Required Features Matrix
- [ ] #29 - Represent Coarse Time Duration Values (semantic linkage: behavioral conversion of coarse duration units)

## Source References
Structural Schema: ietf-yang-types.yang
Normative Specification: RFC 9911, Section 3
