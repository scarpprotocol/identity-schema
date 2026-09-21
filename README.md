# Governance-Enabling Identity for Autonomous Participants

An X.509 v3 extension schema for governance-enabling identity in the AI-enabled economy.

## What This Is

A technical specification defining the certificate structures, field definitions, lifecycle model, and verification procedures necessary for autonomous participants (AI agents, language model services, IoT devices, robots, vehicles, and all other computational and organizational entities) to carry governance-enabling attributes in their identity certificates.

The specification covers:

- A three-certificate chain model separating base identity, ownership binding, and operational delegation
- An entity-class OID namespace for classifying participants by type
- Governance scope as a mandatory certificate field
- Fifteen certificate fields derived from first-principles requirements for autonomous participation
- A push revocation network architecture for compromise response at machine speed
- An external-party-triggered certificate lifecycle mechanism with prior notification and scoped trigger authority
- An output provenance fingerprint architecture for per-artifact attestation binding

## Specification

**[ScarpIdentitySchema_TechSpec_v0.1.md](ScarpIdentitySchema_TechSpec_v0.1.md)** (v0.1.0, September 20, 2026)

## Related

- **Requirements paper:** [Universal Participant Identity for the AI-Enabled Economy](https://doi.org/10.5281/zenodo.22867377) defines the fifteen requirements this schema implements. [Website page](https://scarpprotocol.com/insights/identity-paper/).
- **Companion paper:** [Governable AI from the Ground Up: Identity, Cooperation, Governance, and Settlement as Foundational Infrastructure](https://doi.org/10.5281/zenodo.21927016) describes the broader four-layer substrate architecture.
- **Scarp Governance Gateway:** [scarpprotocol.com/scarp-governance-gateway/](https://scarpprotocol.com/scarp-governance-gateway/) describes a governance enforcement product designed to evaluate and enforce policy using identity credentials of this kind.
- **scarp-verify:** [github.com/scarpprotocol/scarp-verify](https://github.com/scarpprotocol/scarp-verify)

## Author

Victor Davidenko, [Scarp Protocol Inc.](https://scarpprotocol.com)

Contact: victor@scarpprotocol.com

## Patent Notice

The author has filed patent applications that establish priority dates predating this publication and that cover specific mechanisms related to the subject matter described herein.

## License

This work is licensed under [Creative Commons Attribution 4.0 International (CC BY 4.0)](LICENSE).
