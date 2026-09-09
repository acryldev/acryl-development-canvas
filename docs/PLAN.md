# Development Canvas public package

Date: 2026-09-09. Copy-only extraction of the existing ACRYL Development Canvas
Host and Client Cordis plugin. The source package remains in the ACRYL monorepo.

## Capability and plugin boundary

The Canvas is independently configurable, mountable, and removable. The Host
owns loopback PTY routes and live PTY processes. The Client owns its desktop
slot contribution, browser-side PTY client, xterm resources, and styles.

## Provides and consumes

The Host hard-injects `webServer` and provides its owned routes. The Client
hard-injects `slots`, contributes to `desktop.main` only while that declaration
exists, and exposes no parallel service registry.

## Effects and disposal

One Host effect owns the route registrations and `CanvasPtyRegistry`; teardown
removes routes in reverse order and awaits all PTY termination. The Client
declaration effect owns the slot registration, styles, and client handles.

## Configuration and composition

The published `dsh.bundle.patch` inserts the stable
`desktop-development-canvas` Loader row. The row is ordinary Cordis desired
composition and supports disable/reload through the host Loader.

## Events and durability

PTY bytes and Canvas view state are presentation/transport concerns, not a
canonical agent history. Durable agent/room facts remain owned by their runtime
services. This package does not create a second event or persistence system.

## Verification and release

The copied suite covers Host activation, route rollback/removal, PTY lifecycle,
Client slot replacement, and client cleanup. Tags run build, typecheck, test,
npm provenance publish, and GitHub release creation.
