---
title: "Common YANG Data Types: Time Duration Measurement Types"
issue_id: 39
type: "epic"
generation_mode: "subagent"
spec_source: "RFC 9911"
---

# Epic: Common YANG Data Types: Time Duration Measurement Types

## 1. Context
This epic covers YANG types for representing periods of time measured in various units from hours down to nanoseconds as defined in the "ietf-yang-types" module of RFC 9911. These types provide int32 and int64-based signed duration representations suitable for time intervals, delays, timeouts, and high-precision measurement. All types recommend range restriction for non-negative contexts.

## 2. Requirements & Checklist
- [ ] #29 - [Represent Coarse Time Duration Values](https://github.com/gintatkinson/3dgs-011/blob/main/docs/features/feat-09-coarse-time-duration.md) (hours32, minutes32, seconds32)
- [ ] #30 - [Represent Sub-Second Time Duration Values](https://github.com/gintatkinson/3dgs-011/blob/main/docs/features/feat-10-subsecond-time-duration.md) (centiseconds32, milliseconds32, microseconds32, microseconds64)
- [ ] #31 - [Represent High-Resolution Nanosecond Duration Values](https://github.com/gintatkinson/3dgs-011/blob/main/docs/features/feat-11-nanosecond-duration.md) (nanoseconds32, nanoseconds64)

### Associated Use Cases & User Stories
*(To be populated in Phases 2-3)*

## 3. Architecture and System Interaction Diagrams

### Subsystem Component Definition
```mermaid
classDiagram
    class DurationComponent {
        <<component>>
        +Integer toHours(Integer seconds)
        +Integer toMinutes(Integer seconds)
        +Integer convert(String fromUnit, String toUnit)
        +Boolean validateRange(Integer value, String range)
    }
```

## System-Level UML Class Diagram
```mermaid
classDiagram
    class DurationComponent {
        <<component>>
    }
    class CoarseDuration {
        +Integer hours [1]
        +Integer minutes [1]
        +Integer seconds [1]
    }
    class FineDuration {
        +Integer centiseconds [1]
        +Integer milliseconds [1]
        +Integer microseconds [1]
        +Integer nanoseconds [1]
    }
    DurationComponent *-- CoarseDuration
    DurationComponent *-- FineDuration
```

## 4. State Machine Definitions

## System State Machine Diagram
```mermaid
stateDiagram-v2
    [*] --> Normal
    Normal --> Overflow : value exceeds representable range [overflow] / clamp
    Normal --> Negative : value < 0 [negativeAllowed] / signed
    Negative --> Normal : value >= 0
    Overflow --> Normal : value in range [clampReleased]
```

## 5. Specification Context
This epic covers the hours32, minutes32, seconds32, centiseconds32, milliseconds32, microseconds32, microseconds64, nanoseconds32, and nanoseconds64 type definitions in the "ietf-yang-types" YANG module of RFC 9911 (Section 3).

## 6. Source References
Structural Schema: ietf-yang-types.yang
Normative Specification: RFC 9911
