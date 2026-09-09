# ACRYL Development Canvas

`acryl-development-canvas` is a standalone Host and Client Cordis plugin for
the ACRYL Development Canvas. It adds a workspace canvas to compatible Desktop
profiles, with terminal-backed tiles, file tiles, browser tiles, and a Client
contribution to the `desktop.main` slot.

## Install

```sh
dsh plugin --profile desktop add acryl-development-canvas
```

The package declares both a DSH bundle patch and an `acryl-package` manifest,
so it is discoverable through the ACRYL package catalog as well as normal DSH
plugin installation.

## What it owns

- The Host owns loopback PTY routes and every PTY process it starts.
- The Client owns its Desktop slot contribution, xterm clients, injected
  styles, and browser-side cleanup.
- Removing the Loader row unregisters routes, closes PTYs, removes the Canvas
  contribution, and restores the default conversation surface.

## Engineering principles

- **Dependency inversion and separated interfaces.** The Host consumes the
  normal `webServer` capability; the Client consumes `slots`. Neither imports a
  concrete Desktop bootstrap or creates another plugin runtime.
- **One owner for live resources.** Routes, PTYs, subscriptions, styles, and
  slot registrations are acquired inside their owning Cordis effects and
  disposed with their Fibers.
- **Reversible composition.** The stable Loader row controls the capability.
  Disable, unload, or replacement removes only Canvas contributions.
- **Orthogonality and information hiding.** Host process control and Client
  presentation remain separate; PTY transport does not become canonical agent
  history or a global application service.
- **Fail fast and contain failure.** A non-loopback Host is rejected before
  routes mount, and partial route activation rolls back registrations.
- **YAGNI.** The package uses the existing DSH/Cordis Host, Client, slot, and
  Loader seams instead of creating another PTY registry, event bus, or
  lifecycle system.
- **Refactor-safe behavior.** Tests exercise activation, route rollback,
  PTY disposal, Client cleanup, slot replacement, and repeated lifecycle use.

## Package compatibility

The Host requires a loopback DSH `webServer`. The Client is intended for a
profile that declares the `desktop.main` slot. When that slot is absent, the
Client leaves the profile untouched.

## Provenance

This package is a copy-only extraction from the ACRYL workspace. The source
package remains in the main repository. See [`provenance.json`](./provenance.json)
for the source boundary and [`docs/PLAN.md`](./docs/PLAN.md) for the Cordis
lifecycle design.
