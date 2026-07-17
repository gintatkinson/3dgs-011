---
title: "Read Gauge Values with Saturation State Awareness"
issue_id: 42
type: "user-story"
generation_mode: "subagent"
spec_source: "RFC 9911"
---

# User Story: Read Gauge Values with Saturation State Awareness

## Parent Epic
- [ ] #36 - Common YANG Data Types: Counter and Gauge Measurement Types

## Domain Object Mapping
- **Primary Domain Objects:** gauge32, gauge64
- **Actor/Role:** Management Station / Monitoring Application

## BDD Scenario
**As a** Monitoring Application
**I want to** read gauge values and detect saturation states
**So that** I can determine when the measured quantity exceeds representable range

## UML Sequence Diagram
```mermaid
sequenceDiagram
    autonumber
    actor monitor as "monitor : MonitoringApplication"
    participant gaugeService as "gaugeService : GaugeService"
    participant persistence as "persistence : PersistenceStore"

    monitor->>gaugeService: readGauge(nodePath: String)
    gaugeService->>persistence: fetchValue(nodePath: String)
    persistence-->gaugeService: measuredValue : Integer
    alt [measuredValue >= maxRange]
        gaugeService->>gaugeService: clampToMax(measuredValue: Integer, maxValue: Integer)
        gaugeService-->monitor: gaugeValue(maxValue) : UnsignedInteger + saturationHigh
    else [measuredValue <= minRange]
        gaugeService->>gaugeService: clampToMin(measuredValue: Integer, minValue: Integer)
        gaugeService-->monitor: gaugeValue(minValue) : UnsignedInteger + saturationLow
    else [measuredValue within range]
        gaugeService-->monitor: gaugeValue(measuredValue) : UnsignedInteger
    end
```

## Required Features Matrix
- [ ] #22 - Represent Bounded Gauge Values with Rising and Falling Range (semantic linkage: this story exercises gauge saturation behavior)

## Source References
Structural Schema: ietf-yang-types.yang
Normative Specification: RFC 9911, Section 3
