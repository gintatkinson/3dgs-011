---
title: "Common YANG Data Types: Date-Time and Timestamp Types"
issue_id: 38
type: "epic"
generation_mode: "subagent"
spec_source: "RFC 9911"
---

# Epic: Common YANG Data Types: Date-Time and Timestamp Types

## 1. Context
This epic covers YANG types for representing date-time instants, calendar dates, daily recurring times, and system clock timeticks/timestamps as defined in the "ietf-yang-types" module of RFC 9911. These types provide ISO 8601/RFC 3339/RFC 9557-compliant timestamp handling with time zone support, leap second handling, and system uptime measurement with modulo wrap semantics.

## 2. Requirements & Checklist
- [ ] #26 - [Represent Date and Time Values with Time Zone Offset](https://github.com/gintatkinson/3dgs-011/blob/main/docs/features/feat-06-date-time-with-timezone.md) (date-and-time, date, time with timezone)
- [ ] #27 - [Represent Date and Time Values Without Time Zone](https://github.com/gintatkinson/3dgs-011/blob/main/docs/features/feat-07-date-time-no-zone.md) (date-no-zone, time-no-zone)
- [ ] #28 - [Represent System Timeticks and Epoch Timestamps](https://github.com/gintatkinson/3dgs-011/blob/main/docs/features/feat-08-system-timeticks-timestamp.md) (timeticks, timestamp with wrap)

### Associated Use Cases & User Stories
*(To be populated in Phases 2-3)*

## 3. Architecture and System Interaction Diagrams

### Subsystem Component Definition
```mermaid
classDiagram
    class DateTimeComponent {
        <<component>>
        +Boolean validateTimestamp(String ts)
        +String canonicalForm(String ts)
        +Integer toTimeticks(String epoch)
        +Boolean detectLeapSecond(String ts)
    }
```

## System-Level UML Class Diagram
```mermaid
classDiagram
    class DateTimeComponent {
        <<component>>
    }
    class DateTimeTypes {
        +String isoValue [1]
        +String timezone [0..1]
        +Boolean hasLeapSecond [1]
    }
    class TimeTickTypes {
        +Integer hundredths [0..4294967295]
        +Integer referenceEpoch [1]
    }
    DateTimeComponent *-- DateTimeTypes
    DateTimeComponent *-- TimeTickTypes
```

## 4. State Machine Definitions

## System State Machine Diagram
```mermaid
stateDiagram-v2
    [*] --> Active
    Active --> Wrapped : timeticks >= 2^32 [overflow] / resetTimestamps
    Wrapped --> Active : timeticks < 2^32 [postWrap]
    Active --> LeapSecond : second == 60 [leapSecondEvent] / validate
    LeapSecond --> Active : second < 60 [normalTick]
```

## 5. Specification Context
This epic covers the date-and-time, date, date-no-zone, time, time-no-zone, timeticks, and timestamp type definitions in the "ietf-yang-types" YANG module of RFC 9911 (Section 3).

## 6. Source References
Structural Schema: ietf-yang-types.yang
Normative Specification: RFC 9911
