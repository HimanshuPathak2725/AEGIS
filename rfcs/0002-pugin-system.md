# RFC-0002: Plugin Architecture

Status: Draft
Author: Himanshu Pathak
Created: 2026-07-20

---

## Scope

This RFC defines the architectural model for extending AEGIS through independently developed plugins.

It specifies the terminology, architectural principles, extension boundaries, lifecycle, compatibility model, and package organization required to build plugins that integrate with the AEGIS core architecture. Where this RFC discusses isolation and boundaries, it does so at the architectural level only — defining what a plugin may and may not reach across a boundary, not the runtime mechanism by which that boundary is enforced.

This RFC intentionally does not define runtime APIs, language-specific interfaces, plugin implementations, distribution mechanisms, or enforcement mechanisms for isolation. These topics are expected to be specified by future implementation-oriented RFCs.

---

## Summary

<!-- TODO -->

---

## Motivation

AI evaluation evolves substantially faster than traditional software engineering tooling. New attack techniques, evaluation methodologies, reporting formats, and integration targets emerge continuously, making it impractical for the AEGIS core architecture to evolve at the same rate.

If every new capability required modification of the core framework, the project's release cadence would become the limiting factor for innovation. Over time, the core would become increasingly difficult to maintain while simultaneously slowing the adoption of new evaluation techniques.

To avoid coupling the evolution of AI evaluation to the evolution of the framework itself, AEGIS adopts a plugin architecture. This architecture allows capabilities to evolve independently while preserving a stable core responsible only for orchestration, lifecycle management, and architectural contracts.

---

## Terminology

### Plugin

A plugin is an independently deployable module that contributes one or more discrete capabilities to AEGIS by attaching to explicitly declared framework extension points.

Plugins contribute capabilities through stable extension points while remaining isolated from internal implementation details of the AEGIS core.

### Extension Point

An Extension Point is an architectural surface explicitly declared by the AEGIS core through which external plugins may contribute capabilities. What an Extension Point exposes is an abstraction owned and defined by the core, never direct access to core internals or state. Everything not explicitly declared as an Extension Point remains an internal implementation detail of the AEGIS core.

### Capability

A Capability is a discrete functional unit contributed by a plugin that binds to exactly one declared Extension Point within the AEGIS architecture.

A Capability represents the smallest discrete unit of architectural extension recognized by the AEGIS framework.

Multiple related capabilities MAY be packaged within a single plugin, but each Capability MUST bind to exactly one declared Extension Point.

### Contract

A Contract is an architectural specification that defines the structural constraints and mutual obligations between the AEGIS core and a Capability bound to an Extension Point.

A Contract defines what a Capability and the AEGIS core may rely upon at an Extension Point without prescribing how either side fulfills those obligations.

Contracts are architectural constructs and MUST NOT be interpreted as language-specific interfaces, runtime APIs, or implementation mechanisms.

### Registry

A Registry is an architectural catalog of plugins, their declared capabilities, and the architectural metadata required for framework validation by the AEGIS core.

A Registry records structural information required for framework validation without owning plugin implementations, managing package resolution, or controlling the execution lifecycle.

Registries are architectural constructs and MUST NOT be interpreted as package managers, dependency injection containers, or runtime execution mechanisms.

---

## Design Goals

<!-- TODO -->

---

## Non-Goals

<!-- TODO -->

---

## Plugin Definition

A single plugin MAY contribute multiple related capabilities, provided those capabilities represent a coherent functional concern.

Plugins MUST NOT bypass declared extension points or directly depend on internal implementation details of the AEGIS core.

---

## Design Principles

<!-- TODO -->

---

### Extension Point

An Extension Point is an architectural surface explicitly declared by the AEGIS core through which external plugins may contribute capabilities.

What an Extension Point exposes is an abstraction owned and defined by the core, never direct access to core internals or state.

Everything not explicitly declared as an Extension Point MUST be treated as an internal implementation detail of the AEGIS core and MUST NOT be depended upon by plugins.

---

## Plugin Lifecycle

<!-- TODO -->

---

## Plugin Metadata

<!-- TODO -->

---

## Plugin Contracts

<!-- TODO -->

---

## Isolation Rules

<!-- TODO -->

---

## Version Compatibility

<!-- TODO -->

---

## Package Layout

<!-- TODO -->

---

## Open Questions

- Should plugins be lazily loaded, or eagerly loaded at framework startup?
- Should plugins be hot-reloadable, or does a plugin change always require a process restart?
- Should the Registry support multiple simultaneous plugin sources (e.g. local filesystem, npm, remote registry), or exactly one?
- Should Contracts be versioned independently of the plugin that implements them, or does a plugin version imply a contract version?
- Should plugins be sandboxed at the process level, or is in-process isolation (module boundary only) sufficient for the architecture this RFC defines?
- Where does the boundary between "what an Extension Point exposes" and "what Version Compatibility guarantees" actually sit — does Extension Point own that boundary definition, or does it only declare the surface while Isolation Rules and Version Compatibility jointly own what may cross it?
