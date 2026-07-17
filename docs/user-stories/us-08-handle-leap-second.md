---
title: "Handle Leap Seconds in Timestamp Processing"
issue_id: 54
type: "user-story"
generation_mode: "subagent"
spec_source: "RFC 9911"
---

# User Story: Handle Leap Seconds in Timestamp Processing

## Parent Epic
- [ ] #38 - Common YANG Data Types: Date-Time and Timestamp Types

## Domain Object Mapping
- **Primary Domain Objects:** date-and-time, time
- **Actor/Role:** Time Synchronization Service

## BDD Scenario
**As a** Time Synchronization Service
**I want to** validate and preserve leap second values (second = 60) in timestamps
**So that** I can represent UTC leap seconds per RFC 3339

## UML Sequence Diagram
```mermaid
sequenceDiagram
    autonumber
    actor sync as "sync : TimeSyncService"
    participant parser as "parser : TimestampParser"

    sync->>parser: validate(inputString: String)
    parser->>parser: extractSecond(inputString: String)
    alt [second == 60]
        parser->>parser: verifyLeapSecondContext(inputString: String)
        alt [lastMinuteOfMonth]
            parser-->sync: validationResult(true, "Leap second valid")
        else
            parser-->sync: validationResult(false, "Invalid leap second context")
        end
    else [second in 0..59]
        parser-->sync: validationResult(true, "Normal second")
    else [second > 60]
        parser-->sync: validationResult(false, "Invalid second value")
    end
```

## Required Features Matrix
- [ ] #26 - Represent Date and Time Values with Time Zone Offset (semantic linkage: leap second validation in datetime parsing)

## Source References
Structural Schema: ietf-yang-types.yang
Normative Specification: RFC 9911, Section 3
