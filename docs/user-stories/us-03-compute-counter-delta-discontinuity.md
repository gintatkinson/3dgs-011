---
title: "Compute Counter Delta Across Discontinuity Events"
issue_id: 43
type: "user-story"
generation_mode: "subagent"
spec_source: "RFC 9911"
---

# User Story: Compute Counter Delta Across Discontinuity Events

## Parent Epic
- [ ] #36 - Common YANG Data Types: Counter and Gauge Measurement Types

## Domain Object Mapping
- **Primary Domain Objects:** counter32, counter64, zero-based-counter32, zero-based-counter64
- **Actor/Role:** Traffic Analyzer / Billing System

## BDD Scenario
**As a** Traffic Analyzer
**I want to** compute accurate counter deltas even when wrap-around or discontinuities occur
**So that** I can calculate correct traffic volumes and rates

## UML Sequence Diagram
```mermaid
sequenceDiagram
    autonumber
    actor analyzer as "analyzer : TrafficAnalyzer"
    participant deltaService as "deltaService : DeltaComputationService"
    participant persistence as "persistence : PersistenceStore"

    analyzer->>deltaService: computeDelta(counterPath: String, pollInterval: Duration)
    deltaService->>persistence: fetchPrevious(counterPath: String)
    persistence-->deltaService: previousValue : UnsignedInteger
    deltaService->>persistence: fetchCurrent(counterPath: String)
    persistence-->deltaService: currentValue : UnsignedInteger
    alt [currentValue < previousValue]
        deltaService->>deltaService: computeWrappedDelta(previousValue: Integer, currentValue: Integer, maxValue: Integer)
        deltaService-->analyzer: delta : UnsignedInteger + wrapFlag
    else [discontinuityDetected]
        deltaService->>persistence: fetchDiscontinuityTimestamp(counterPath: String)
        persistence-->deltaService: discontinuityTime : DateTime
        deltaService-->analyzer: result : DiscardResult
    else [currentValue >= previousValue]
        deltaService-->analyzer: delta(currentValue - previousValue) : UnsignedInteger
    end
```

## Required Features Matrix
- [ ] #21 - Represent Monotonic Counter Values with Wrap-Around (semantic linkage: algorithmic story for wrap delta computation)

## Source References
Structural Schema: ietf-yang-types.yang
Normative Specification: RFC 9911, Section 3
