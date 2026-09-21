---
title: "Governance-Enabling Identity for Autonomous Participants: An X.509 v3 Extension Schema for the AI-Enabled Economy"
author: Victor Davidenko, Scarp Protocol Inc.
date: September 20, 2026
version: 0.1.0
status: Draft Specification
license: CC BY 4.0
---

# Governance-Enabling Identity for Autonomous Participants

## An X.509 v3 Extension Schema for the AI-Enabled Economy

**Author:** Victor Davidenko, Scarp Protocol Inc.

**Version:** 0.1.0

**Date:** September 20, 2026

**Status:** Draft Specification

**License:** Creative Commons Attribution 4.0 International (CC BY 4.0)

---

## Status of This Document

This is a v0.1.0 draft specification. It defines the architectural patterns, certificate structures, field semantics, and lifecycle model for governance-enabling identity in the AI-enabled economy.

Formal ASN.1 module definitions, OID arc registrations, wire protocol specifications for push revocation, and certificate profile examples are planned for subsequent versions. The encoding approach for each field is described at the level necessary for implementers to understand the architecture; formal encoding follows once the architectural patterns stabilize through community review.

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119.

---

## Table of Contents

1. [Introduction](#1-introduction)
2. [Architectural Overview](#2-architectural-overview)
3. [Certificate Structure](#3-certificate-structure)
4. [Entity Classification](#4-entity-classification)
5. [Governance Scope Field](#5-governance-scope-field)
6. [Verification and Evaluation Fields (Requirements 1-9)](#6-verification-and-evaluation-fields-requirements-1-9)
7. [Contract Negotiation Fields (Requirements 10-15)](#7-contract-negotiation-fields-requirements-10-15)
8. [Certificate Lifecycle](#8-certificate-lifecycle)
9. [Push Revocation Network](#9-push-revocation-network)
10. [Conformance and Verification](#10-conformance-and-verification)
11. [Security Considerations](#11-security-considerations)
12. [Privacy Considerations](#12-privacy-considerations)
13. [Relationship to Existing Standards](#13-relationship-to-existing-standards)
14. [Patent Notice](#14-patent-notice)
15. [References](#15-references)

---

## 1. Introduction

### 1.1 Purpose

This specification defines an X.509 v3 extension schema for governance-enabling identity in the AI-enabled economy. It provides the certificate structures, field definitions, lifecycle model, and verification procedures necessary for autonomous participants (AI agents, language model services, IoT devices, robots, vehicles, and all other computational and organizational entities) to carry governance-enabling attributes in their identity certificates.

The intended audience is certificate authorities, governance infrastructure vendors, platform providers, and standards bodies. This document is a construction, not a requirements analysis: it defines how governance-enabling identity is built, not what it should accomplish.

### 1.2 Scope

This specification covers:

- A three-certificate chain model separating base identity, ownership binding, and operational delegation
- An entity-class OID namespace for classifying participants by type
- Governance scope as a mandatory certificate field
- Fifteen certificate fields derived from first-principles requirements for autonomous participation
- A push revocation network architecture for compromise response at machine speed
- An external-party-triggered certificate lifecycle mechanism
- An output provenance fingerprint architecture for per-artifact attestation binding

This specification does not cover:

- Governance contract negotiation protocols (how two governance systems form a mutually binding contract)
- Settlement protocols (how identity-anchored financial transactions are processed)
- Marketplace discovery mechanisms (how agents find and evaluate each other)
- Governance policy evaluation engines (how a gateway enforces policy against certificate fields)

These are complementary systems that consume the identity schema defined here. Scarp Governance Gateway, a governance enforcement product designed to evaluate and enforce policy using identity credentials of this kind, is described at https://scarpprotocol.com/scarp-governance-gateway/.

### 1.3 Relationship to Requirements Paper

This specification implements the fifteen requirements defined in "Universal Participant Identity for the AI-Enabled Economy: Why Every Participant in an Autonomous Economy Needs Identity That Carries More Than Authentication" (Davidenko, 2026) [10.5281/zenodo.21927016 (DOI)]. That paper establishes the requirements through first-principles analysis of autonomous participation scenarios. This specification provides the construction.

Readers unfamiliar with the motivation for governance-enabling identity should consult the requirements paper first [TODO: INSERT URL]. This specification assumes familiarity with the requirements and focuses on the technical construction that satisfies them.

### 1.4 Terminology

**Autonomous computational entity.** Any software agent, AI system, language model service, or other computational entity that initiates actions, forms agreements, or commits resources on behalf of a principal without continuous human supervision.

**Base Identity Certificate (BIC).** The permanent identity certificate issued by the CA at entity creation time, at the request of the entity's creator or manufacturer. Analogous to a vehicle identification number. Survives all ownership transfers.

**Certificate authority (CA).** An entity that issues, manages, and revokes identity certificates. In this specification, the CA issues governance-enabling certificates with the extensions defined herein.

**Delegation Certificate (DC).** A certificate issued by the CA at the request of the entity's owner to bound the entity's operational scope for a specific context. Short-lived. Multiple concurrent DCs for different principals or customers are supported.

**Entity class.** A classification of the type of participant (autonomous computational entity, language model service, IoT device, human principal, etc.). Encoded as an Extended Key Usage OID.

**Governance scope.** The set of governance constraints that bind an entity: data classification limits, jurisdictional rules, authority ceilings, and regulatory compliance requirements. Encoded as a mandatory certificate field.

**Governance system.** The infrastructure that evaluates, enforces, and records governance policy for one or more entities. Reads certificate fields to make policy decisions. Also referred to as a governance gateway when deployed as a network-path enforcement point.

**Ownership Binding Certificate (OBC).** A certificate binding an entity to its current owner or operating principal. Carries the principal's identity, governance contract instance, and liability acceptance. Revoked and re-issued upon ownership transfer.

**Principal.** A human, organization, or government entity that owns, controls, or delegates authority to an autonomous computational entity.

**Push revocation.** A publish-subscribe infrastructure that propagates certificate revocation events from the CA to all registered validation gateways proactively, rather than waiting for verifiers to poll.

**Validation gateway.** A network enforcement point that requires presentation of valid governance-enabling certificates before processing any request. Receives push revocation events.

### 1.5 Patent Notice

This specification is published by Victor Davidenko / Scarp Protocol Inc. under Creative Commons Attribution 4.0 International (CC BY 4.0). The author has filed patent applications that establish priority dates predating this publication and that cover specific mechanisms related to the subject matter described herein. Contact: victor@scarpprotocol.com.

---

## 2. Architectural Overview

### 2.1 Design Principles

Five principles drive the architecture:

**Governance travels with identity.** Governance-enabling attributes are encoded in the certificate itself, not applied as external policy at runtime. Changing governance scope requires certificate re-issuance through CA-level controls, not a configuration change that an administrator can make in error.

**Independent third-party attestation.** Attributes that a counterparty's governance system relies on for trust decisions MUST be attested by independent third parties, not self-declared by the entity or its principal. Entity classification is attested by the CA. Financial commitment ceilings are attested by financial institutions. Insurance coverage is attested by insurance providers. The receiving governance system trusts the attesting authority, not the entity.

**Lifecycle independence.** The three certificates in the chain have independent lifecycles. Ownership changes without re-issuing base identity. Delegation scopes expire without affecting ownership. Base identity persists through all changes. This independence is the structural mechanism that prevents reputation laundering and enables surgical revocation.

**Infrastructure-level enforcement.** Participation in any system that consumes governance-enabling identity (discovery, agentic workflows, settlement, marketplace, data access) is conditioned on holding a valid current identity certificate. An entity without a valid certificate is excluded at the infrastructure level, as a mathematical prerequisite rather than a policy configuration.

**Mechanism-agnostic extensibility.** The schema defines field semantics and encoding approaches. It does not mandate specific implementations of the systems that produce, consume, or verify these fields. A governance system that reads entity classification from a certificate may be implemented as a gateway, a sidecar, an API middleware, or any other architectural pattern.

### 2.2 Certificate-Based Identity as the Substrate

This specification builds on X.509 v3 certificates. The choice is deliberate and worth explaining, because other identity substrates exist and each has advocates.

**Trust between strangers without prior coordination.** X.509's hierarchical CA model is the only production identity infrastructure where two entities that have never interacted, in different jurisdictions, operated by different organizations, can verify each other's identity without any prior arrangement. If both trust the same CA root (or roots connected through a cross-certification hierarchy), verification works on first contact. SPIFFE requires pre-configured trust bundle exchange between SPIRE servers on both sides before any cross-domain verification is possible. W3C DIDs require the verifier to independently decide whether to trust the resolution mechanism. OAuth requires pre-registration with each authorization server. In an autonomous economy where billions of interactions happen between strangers at machine speed, there is no human to pre-configure each trust relationship. The trust infrastructure must work on first contact, and X.509 is the only substrate where it does.

**Revocation infrastructure.** When an entity is compromised, its identity must be revoked immediately and that revocation must propagate to every system that trusts it. X.509 has mature revocation infrastructure through Certificate Revocation Lists (CRLs) and the Online Certificate Status Protocol (OCSP). This specification extends that foundation with push revocation (Section 9), which propagates revocation events proactively rather than waiting for verifiers to poll. DID revocation is fundamentally harder because there is no central authority to enforce it. Updating a DID Document and relying on verifiers to check is not an acceptable security posture when a compromised agent can execute thousands of transactions per second.

**Rich field capacity with lifecycle guarantees.** X.509 v3's custom extension mechanism supports arbitrary additional fields through Object Identifier (OID) namespaces. Each extension can be marked critical (recipients MUST understand it) or non-critical (recipients MAY ignore it). Extensions carry structured data through ASN.1 encoding, with no practical limit on the number or complexity of fields. Other substrates offer some extensibility but not the combination: SPIFFE SVIDs carry a URI string. OAuth tokens carry JSON claims but are ephemeral and not bound to an entity's lifecycle. DID documents are extensible but lack CA-managed issuance, revocation, and renewal. Only X.509 v3 combines rich governance field capacity with CA-managed lifecycle and cross-domain trust verification.

**Enterprise operational familiarity.** Organizations have existing certificate authority infrastructure, key management systems, and operational procedures for certificate lifecycle management. Building on X.509 v3 lets organizations extend their existing PKI rather than deploying entirely separate identity infrastructure. Every CISO understands PKI. That matters for adoption.

**Decades of operational hardening.** X.509 PKI has been in production at internet scale since the mid-1990s. Certificate Transparency, OCSP stapling, hardware security modules, automated certificate management (the ACME protocol), key ceremony procedures, CA audit frameworks (WebTrust, ETSI) all exist and are battle-tested. No other identity substrate has this depth of operational tooling and institutional knowledge.

**Regulatory recognition.** X.509 certificates and PKI are recognized in regulatory frameworks worldwide, including eIDAS in the EU, digital signature legislation across multiple jurisdictions, financial services regulations, and government identity programs. Building on X.509 means governance-enabling identity inherits that regulatory recognition rather than requiring new legislation or regulatory guidance to establish the substrate's legal standing.

### 2.3 Independent Third-Party Attestation Model

Governance-enabling certificate fields are attested by independent authorities, not self-declared. This is a deliberate departure from the current web PKI model, where the CA is the single attesting authority (it attests that a domain name belongs to the certificate holder). In governance-enabling identity, multiple independent authorities attest different attributes of the same entity:

- The CA attests entity classification and base identity
- A financial institution attests financial commitment ceilings
- An insurance provider attests insurance coverage
- An accredited evaluator attests data handling certification
- An independent audit authority attests model bias evaluation
- A regulatory body attests actions classification

These authorities do not co-sign a single certificate; each issues its own independent credential linked to the entity's identity certificate by reference, on its own lifecycle, through the attribute certificate model described in Section 2.4.

### 2.4 Multi-Authority Attestation Architecture

Within the X.509 v3 framework, three mechanisms can support multi-authority attestation:

**Attribute certificates (RFC 5755).** This is the RECOMMENDED approach. X.509 Attribute Certificates are designed specifically for authorization attributes issued by authorities other than the CA that issued the holder's identity certificate. An Attribute Authority issues an attribute certificate linked to the holder's identity certificate. Multiple Attribute Authorities attest different attributes independently, each on their own lifecycle. This is the mechanism described in Section 2.3 above.

**Cross-signed extensions.** The attesting authority signs the extension value. The CA includes the signed extension in the identity certificate itself. The verifier checks the CA's signature over the certificate and the attesting authority's signature over the extension independently. Simpler to verify than attribute certificates, but updating an attestation requires the CA to re-issue the identity certificate.

**Nested certificates.** The attestation is itself a short-lived certificate issued by the attesting authority, embedded in or referenced by the main certificate. The verifier resolves the nested certificate through the attesting authority's trust chain. More complex to verify but keeps attestation updates independent of the identity certificate.

Implementers SHOULD evaluate each against their deployment constraints. For deployments where attestations change independently of identity (the common case: insurance renews annually, financial ceilings adjust quarterly, certifications expire on their own schedule), attribute certificates offer the lowest operational overhead because no attestation change triggers identity certificate re-issuance.

---

## 3. Certificate Structure

### 3.1 Three-Certificate Chain Model

Each autonomous computational entity is issued a chain of three certificates. The chain provides lifecycle independence: each certificate serves a distinct role and has its own issuance, renewal, and revocation lifecycle.

#### 3.1.1 Base Identity Certificate (BIC)

The BIC is issued by the CA at entity creation time, at the request of the entity's creator or manufacturer. Think of it as a vehicle identification number: it encodes origin and capability regardless of who currently owns the vehicle.

The BIC MUST encode:

- **Creator identity.** The identity reference of the entity's creator or manufacturer.
- **Entity class.** The entity-class OID from the defined namespace (Section 4).
- **Capability class.** The certified capabilities of the entity at creation time.
- **Behavioral audit status.** Whether the entity's behavioral history has been audited and by whom. Legislation such as the FRONTIER Act (H.R. 9925, 2026), which requires independent audits of frontier AI systems, would produce exactly the kind of evaluation result this field is designed to carry.
- **Entity version.** Version identifier for the entity's current configuration.
- **Governance contract template.** Reference to the governance contract template that defines the default governance rules for this entity class.

The BIC is immutable through all subsequent ownership transfers. When the entity changes hands, the BIC persists unchanged. When the entity is upgraded, the BIC MAY be re-issued with updated capability and version fields, but the creator identity and entity class remain fixed.

The BIC MUST NOT be revoked upon ownership transfer. Revocation of a BIC represents permanent decommissioning of the entity.

#### 3.1.2 Ownership Binding Certificate (OBC)

The OBC is issued by the CA to an operating principal at the time of acquisition. It binds the entity's permanent identity (the BIC) to its current owner.

The OBC MUST encode:

- **Principal identity reference.** The identity certificate reference of the owning principal (human, organization, or government entity). The principal holds its own identity certificate issued by the CA under the appropriate entity class (human principal, corporate entity, or government entity per Section 4.2). The three-certificate chain applies to autonomous computational entities; the principal's certificate is a separate identity that the OBC references, creating a verifiable link from the autonomous entity to its accountable owner.
- **Binding timestamp.** When ownership was established.
- **Governance contract instance.** The specific governance contract configured by the acquiring principal, within the bounds of the governance contract template defined in the BIC.
- **Liability acceptance declaration.** The principal's acceptance of liability for the entity's actions within the governance scope.
- **Jurisdictional registration.** The jurisdiction(s) in which this ownership relationship is registered.

The OBC has a separate lifecycle from the BIC. When the OBC is revoked (whether from a subscription lapse, a terms violation, or an ownership transfer), the entity ceases operating at the infrastructure level. No software-level change to the entity is required. The validation gateway rejects requests from entities whose OBC is not current and valid.

Upon ownership transfer, the existing OBC is revoked and a new OBC is issued to the acquiring principal. The BIC persists unchanged.

#### 3.1.3 Delegation Certificate (DC)

The DC is issued by the CA at the request of the entity's owner (the OBC holder) to bound the entity's operational scope for a specific context. It is the narrowest-scope certificate in the chain.

The DC MUST encode:

- **Scope of delegation.** The specific operational capabilities authorized for this delegation context.
- **Operational limits.** Quantitative bounds (financial ceilings, data classification limits, geographic boundaries, time-of-day restrictions).
- **Expiry timestamp.** When the delegation expires.
- **Revocability conditions.** Under what conditions the delegation can be revoked, and by whom.

Multiple concurrent DCs from different principals are architecturally supported. A specialized entity MAY serve multiple customers simultaneously, each with their own DC defining their own governance scope, authority limits, and contractual terms. Each DC has an independent lifecycle: one customer's revocation MUST NOT affect another's. Each DC is bound to a specific customer relationship; the counterparty's identity in the mTLS handshake determines which DC the agent presents.

The DC SHOULD be short-lived relative to the BIC and OBC. The DC represents a specific operational authorization that changes more frequently than identity or ownership.

### 3.2 Chain Relationships and Lifecycle Independence

The validation gateway MUST require the simultaneous validity of all three certificates before processing any request from an entity:

- A valid BIC (entity exists and has not been decommissioned)
- A valid OBC (entity has a current owner who has accepted liability)
- A valid DC (entity has a current operational authorization for this context)

If any certificate in the chain is revoked, expired, or absent, the entity cannot participate. The enforcement happens at the infrastructure level, with no software change to the entity required.

The lifecycle independence of the three certificates enables three distinct operational patterns:

**Ownership transfer without identity disruption.** The OBC is revoked and re-issued. The BIC and all behavioral history persist. The new owner inherits the entity's complete track record.

**Delegation change without ownership disruption.** The DC is revoked and re-issued. The OBC and BIC are unaffected. The entity receives new operational boundaries without changing ownership.

**Surgical containment.** A specific DC can be revoked to remove an entity's access to a specific tool, dataset, or workflow, while other DCs for the same entity remain valid. The entity loses one operational authorization while retaining all others.

### 3.3 Behavioral History Persistence Through Ownership Transfer

The behavioral history of an entity is cryptographically bound to the BIC, not the OBC. When ownership is transferred:

1. The existing OBC is revoked.
2. A new OBC is issued to the acquiring principal.
3. The BIC persists unchanged.
4. All behavioral audit records associated with the BIC persist unchanged and are made available to the new owner.

The new owner MUST receive the entity's complete prior behavioral record. The new owner MUST NOT be able to reset, modify, or remove any behavioral history through any ownership transfer transaction.

Because the behavioral history is bound to the BIC, and the BIC does not change on transfer, the history cannot change on transfer either. This is structural, not a policy someone could bypass.

The practical consequence is that agent laundering is prevented. An entity with a record of failures or policy violations cannot be re-credentialed under new ownership and re-enter the market with a clean slate. Buyers in agent marketplaces can rely on verified track records to distinguish quality from marketing.

---

## 4. Entity Classification

### 4.1 Entity-Class OID Namespace

The specification defines a new Extended Key Usage (EKU) Object Identifier (OID) namespace providing entity-class-specific OIDs for each recognized entity class. These OIDs are embedded in X.509 v3 certificates as EKU extensions.

The entity-class OID enables validation gateways to determine the entity class of a certificate presenter and apply class-appropriate governance rules without separate registry lookup. The binding between identity and governance-rule-selection is cryptographic: it is in the certificate, not in an external configuration that an administrator maintains.

OID arc registration through an appropriate authority (IANA, a national standards body, or a private enterprise arc) is a prerequisite for production deployment. This specification defines the architectural approach and the entity taxonomy. Actual OID values are assigned through the registration process.

### 4.2 Entity Type Taxonomy

The following entity classes are recognized. This taxonomy is extensible: additional entity classes MAY be registered as the autonomous economy evolves.

**Autonomous computational entities.** Software agents that initiate actions, form agreements, or commit resources on behalf of a principal. This is the broadest class and includes general-purpose AI agents, specialized workflow agents, procurement agents, compliance agents, and any other software entity operating with delegated authority.

**Computational service providers.** Cloud platforms, APIs, and infrastructure services that host, execute, or route agent workloads. These provide the execution environment rather than acting autonomously.

**Language model services.** AI models that process natural language inputs and produce outputs. Governance rules for model services include provenance, bias evaluation, and jurisdictional classification attributes specific to language models (Section 6.5), warranting a separate class.

**Physical autonomous systems.** Vehicles, robots, drones, and other physical entities that operate with delegated authority in the physical world. Governance rules here must account for physical-world consequences, safety certifications, and geographically bounded operational authorities.

**IoT devices and sensor networks.** Devices that produce streaming data or continuous outputs. Separated because of high-volume certificate issuance requirements (Section 8.1), constrained device capabilities, and the need for per-output provenance (Section 6.7) on streaming data.

**Government entities.** Government agencies, regulatory bodies, tax authorities, and other sovereign entities that participate in the autonomous economy. They operate under sovereign authority with regulatory enforcement powers that demand a distinct governance class.

**Corporate entities.** Corporations, partnerships, and other business organizations. Corporate entities serve as principals in the ownership chain, distinct from the autonomous computational entities they own and operate.

**Non-profit institutions.** Educational institutions, research organizations, standards bodies, and other non-profit entities. Governance rules may differ from corporate entities in tax treatment, regulatory classification, and data handling obligations.

**Human principals.** Individual humans who own, control, or delegate authority to autonomous entities. Human principals are the ultimate accountability anchor in any delegation chain.

### 4.3 Entity Class as a Mandatory Certificate Field

Entity class MUST be encoded in every governance-enabling identity certificate. A certificate without an entity-class OID MUST NOT be treated as a governance-enabling certificate by any validation gateway.

The entity-class OID is encoded as an Extended Key Usage extension in the X.509 v3 certificate. The EKU extension is marked critical: a recipient that does not recognize the entity-class OID namespace MUST reject the certificate. The result is that governance-enabling certificates are only processed by systems that understand entity classification.

---

## 5. Governance Scope Field

### 5.1 Governance Scope as a Mandatory Certificate Extension

Governance scope is a mandatory field in every governance-enabling identity certificate. Unlike any existing identity system, governance scope is encoded in the certificate itself, not applied as external policy at runtime.

This design decision reflects a fundamental architectural principle: if governance scope is external policy, a misconfiguration or synchronization failure can leave an entity operating outside its intended boundaries. If governance scope is a mandatory certificate field, the boundaries travel with the identity. Changing governance scope requires certificate re-issuance, which means it passes through CA-level controls rather than a configuration change.

### 5.2 Scope Structure and Encoding Approach

The governance scope field is encoded in the Subject Alternative Name (SAN) URI field as part of a structured URI format. The SAN URI MUST include as mandatory fields:

- **Entity class identifier.** Cross-references the EKU OID for validation consistency.
- **Owner identity reference.** Cross-references the OBC's principal identity.
- **Governance scope parameters.** The governance constraints that bind this entity, encoded as a set of independently evaluable parameters including but not limited to: data classification ceilings, authority limits, operational boundaries, and regulatory compliance classifications. A single entity's governance scope will typically carry multiple parameters simultaneously.
- **Jurisdictional classification.** The jurisdiction(s) whose rules apply.
- **Capability hash.** A cryptographic hash of the entity's certified capability set.
- **Audit timestamp.** The timestamp of the most recent behavioral audit.

The encoding approach uses structured URI syntax with defined field separators, supporting machine-parsing of individual parameters at validation time.

### 5.3 Governance Contract Binding

The governance scope in the certificate establishes the ceiling. The governance contract instance referenced in the OBC defines the specific rules within that ceiling.

The governance contract is not in the certificate. It is a separate governed artifact, cryptographically referenced by the OBC, that defines detailed policy rules: which actions require approval, what thresholds trigger escrow, what data classifications are permitted, what audit logging is required. The governance scope in the certificate is the immutable boundary. The governance contract is the configurable policy within that boundary.

A governance system evaluating an incoming entity reads the governance scope from the certificate to determine whether the entity is within bounds. If the governance scope does not satisfy the receiving system's minimum requirements, the entity is rejected before the governance contract is consulted.

### 5.4 Scope Verification by Counterparty Governance Systems

A receiving governance system MUST verify the governance scope of an incoming entity against its own policy requirements before processing any request. Verification proceeds as follows:

1. Extract the governance scope from the entity's BIC SAN URI field.
2. Parse the scope parameters to identify data classification ceiling, jurisdictional classification, and authority limits.
3. Compare against the receiving system's minimum requirements for the proposed interaction.
4. If the governance scope satisfies the requirements, proceed to governance contract evaluation.
5. If the governance scope does not satisfy the requirements, reject the entity. No governance contract evaluation is necessary.

---

## 6. Verification and Evaluation Fields (Requirements 1-9)

The first nine requirements address what a receiving governance system needs in order to identify, verify, and evaluate a stranger's entity. Each requirement maps to a certificate field or architectural mechanism.

### 6.1 Requirement 1: Entity Classification

Entity classification is addressed by Section 4. The entity-class OID in the EKU extension tells the receiving governance system what kind of entity is presenting. Different entity classes trigger different governance rules.

### 6.2 Requirement 2: Governance Scope

Governance scope is addressed by Section 5. The SAN URI governance scope parameter tells the receiving governance system what governance boundaries bind the entity.

### 6.3 Requirement 3: Ownership with Portable Behavioral History

Ownership binding is addressed by the OBC (Section 3.1.2). Behavioral history persistence is addressed by Section 3.3.

The behavioral history is an immutable record bound to the BIC. It includes all governance evidence produced during the entity's operation: actions taken, policy evaluations performed, compliance outcomes, audit results, and any governance violations or anomalies.

The record is structured as a chain of evidence entries, each signed by the governance system that produced it. The BIC carries a reference (hash or URI) to the current head of the behavioral history chain. A verifier follows the reference to retrieve and verify the history.

### 6.4 Requirement 4: Concurrent Delegation

Concurrent delegation is a structural property of the three-certificate chain model. The architecture supports a plurality of concurrent DCs for the same entity, each issued by the CA at the owner's request to bound operational scope for a distinct customer or operational context.

Each concurrent DC MUST encode:

- A distinct scope of delegation for this customer or context
- Independent expiry and revocability conditions
- A reference to the OBC that authorizes this delegation

Revocation of one DC MUST NOT affect any other DC for the same entity. Each delegation relationship has its own lifecycle.

This transforms entities from workers (one owner, one customer, one revenue stream) into products (one owner, many customers, each with their own delegation scope and certificate lifecycle). A specialized entity leased to a hundred customers generates a hundred concurrent DC operations: issuance, renewal, revocation, all managed by the CA, all independently revocable.

### 6.5 Requirement 5: Model and Runtime Provenance

For entities whose behavior depends on an underlying language model, the certificate MUST encode model provenance attributes. These attributes are specific to the language-model-service entity class and are encoded as additional X.509 v3 extensions.

The model provenance extension MUST encode:

- **Model version identifier.** Cryptographically bound to a specific model checkpoint hash rather than to the providing organization. This ensures the certificate identifies the exact model version, not a mutable organizational reference.
- **Training data provenance attestation.** A hash of the training data manifest and a reference to the certificate of the recognized evaluation authority that performed the audit. The auditor's certificate, issued by the CA under the appropriate entity class, is the trust anchor for the attestation.
- **Bias evaluation score.** An evaluation score from a recognized independent audit authority, encoding the evaluator's identity. The evaluation methodology is referenced, not defined, by the certificate.
- **Jurisdictional classification.** Selected from a defined enumeration comprising at minimum: INDEPENDENT (no state influence on training or operation), STATE_INFLUENCED (partial state involvement), STATE_CONTROLLED (operated or directed by a state entity), and UNAUDITED (jurisdictional independence has not been evaluated).
- **Version-bound expiry.** The certificate expires when the model version changes, requiring re-issuance. This ensures the certificate always reflects the currently deployed model version's attributes.

A governance system evaluating whether to route sensitive data to a language model reads the model's certificate. If the jurisdictional classification is STATE_CONTROLLED, the governance system applies policies restricting what data classifications are sent. If the bias evaluation is below threshold, the governance system requires additional human review. None of this is possible without governance metadata in the certificate.

### 6.6 Requirement 6: Compromise Response at Machine Speed

Push revocation is addressed by Section 9. The architectural requirement is that when an entity is compromised, the revocation signal MUST propagate to every validation gateway that trusts the issuing CA within a latency threshold substantially shorter than OCSP polling intervals.

Standard certificate revocation (CRL/OCSP) is pull-based: verifiers check periodically. In an autonomous economy where transactions happen at machine speed, the window between revocation and the last verifier's check is exposure. Push revocation closes that window by proactively delivering revocation events to every registered gateway.

The compromise response is binary and definitive: the identity is either valid or it is not. There is no "partially revoked" state for an entity that can commit financial obligations or operate physical machinery in the time it takes a human administrator to review a revocation request.

### 6.7 Requirement 7: Output Provenance

For entities that produce discrete outputs or streaming data, connection-time identity verification is insufficient. Each output MUST carry a compact attestation binding it to the producer's verified identity and the governance conditions at the moment of production.

The output provenance fingerprint is a compact, self-contained attribution block attached to any produced artifact. It answers up to three questions for any downstream consumer: who produced this output (source identity), was the production governed and by which authority (governance provenance), and optionally, was the runtime environment independently attested (hardware-environment provenance).

#### 6.7.1 Fingerprint Schema

The output provenance fingerprint comprises the following fields:

**Output binding.** A cryptographic hash of the output content (the `output_hash`), computed using a specified hash algorithm. Every attestation block signs over this hash. Any party holding the output can recompute the hash and verify that all attestations reference the same artifact. The hash is the join key: no coordination protocol between attestation producers is required.

**Producer identification.** An identity-layer entity reference for the agent or team that produced the output. This field is mechanism-agnostic: it does not encode the identity mechanism (X.509, SPIFFE, DID, or future schemes). The identity layer resolves it.

**Workflow context.** A workflow identifier spanning the full delegation tree for multi-agent productions. Present for single-agent outputs (single-node tree). Serves as the correlation key for audit-depth evidence retrieval.

**Attestation list.** An ordered list of independently produced, independently signed, independently verifiable attestation blocks. Each block is one attestation type. Initial attestation types are:

- `governance`: policy enforcement attestation, produced by the governance system that governed the production. The attestation reference points to the governance evidence record. The authority identifier identifies the governance authority (not the gateway itself).
- `runtime`: hardware-environment attestation, produced by the runtime environment's attestation infrastructure when available. The attestation reference points to the environment attestation record. The authority identifier identifies the attestation trust root for that environment.

Additional attestation types (compliance certification, data-lineage attestation, jurisdiction attestation) MAY be added without schema changes.

Each attestation block carries:

- **Type.** The attestation type identifier.
- **Reference.** A reference to the full attestation record for audit-depth verification.
- **Authority identifier.** A resolvable identifier for the signing authority, supporting trust-chain resolution at verification time.
- **Signature.** Signature over the output hash, reference, type, and any extended fields. Independently verifiable without access to any other attestation block.
- **Extended fields.** Type-specific optional fields included in the signature computation (signed as null when absent). For governance attestations, this includes a signed policy reference enabling per-unit policy-state verification at gateway speed.

**Schema metadata.** Schema version identifier and ISO 8601 production timestamp.

#### 6.7.2 Design Principles

**Output hash as join key.** Every attestation block signs over `output_hash`. Any party holding the output can recompute the hash and verify that all attestations reference the same artifact. No coordination protocol between attestation producers is required. The mathematical property (same hash input) is the binding.

**Graceful degradation.** The attestations list can contain zero, one, or many entries. A governance-only fingerprint (no hardware-environment attestation) is valid at a governance-assurance level. A hardware-environment-only fingerprint (no governance attestation) is valid at an environment-assurance level. An empty attestations list means the output is unattested. The consuming governance system decides its own minimum assurance threshold.

**Multi-authority composition.** When a workflow spans multiple organizations, each organization's governance instance produces its own attestation block on the outputs its agents produce. The consuming governance system evaluates each authority identifier against its own trust policy: accept inputs governed by authorities A, B, C; reject inputs governed by authority D; reject inputs with no governance attestation.

**Two-level verification.** Everyday verification checks the fingerprint fields (hash match, signature validity, authority trust chain, policy reference). Audit-depth verification follows the attestation references to the full governance evidence records and environment attestation records for complete forensic analysis.

### 6.8 Requirement 8: Jurisdictional and Tax Binding

Every governance-enabling identity certificate MUST encode jurisdictional and tax binding as mandatory fields. The jurisdictional binding extension MUST encode:

- **Tax residency jurisdiction array.** All jurisdictions in which the certificate holder is a tax resident. The array MUST be mandatory and non-empty for any entity class subject to tax obligations.
- **Tax authority identity reference mapping.** For each jurisdiction in the tax residency array, a mapping linking the jurisdiction identifier to the registered entity identity certificate reference of the tax authority for that jurisdiction.
- **Tax classification.** The entity's tax treatment type, selected from a defined enumeration comprising at minimum: INDIVIDUAL_INCOME, CORPORATE, ATTRIBUTED_TO_PRINCIPAL, TAX_EXEMPT, and SOVEREIGN.

A settlement protocol that consumes these fields resolves the applicable tax authorities and withholding obligations for any transaction exclusively by reference to the certificate fields of both transacting parties. Tax remittance routing becomes a deterministic function of the certificate fields rather than a separately required compliance declaration.

### 6.9 Requirement 9: Self-Identification at Network Layer

An autonomous entity acting across network boundaries is a distinct kind of network actor. It is not a human generating HTTP requests from a browser. It is not a traditional application following a fixed API contract. Existing network infrastructure has no reliable way to distinguish agent-originated traffic from human-originated or application-originated traffic. The consequences of this gap are already visible in litigation: in Amazon v. Perplexity AI (9th Cir. No. 26-1444, 2026), the court found that an AI agent's concealment of its identity through user-agent string manipulation was central to the dispute, highlighting the structural absence of a reliable agent self-identification mechanism at the network layer.

When every entity carries its governance-enabling identity certificate in every network interaction via mutual TLS (mTLS), the distinction becomes structural. Network infrastructure that can read entity identity from the TLS handshake can route agent traffic to governance infrastructure before the action reaches its destination. An entity that does not present a governance-enabling certificate is treated as conventional network traffic and handled by conventional security controls. Agent-infrastructure privileges (invoking tools, negotiating with other agents, committing resources on behalf of a principal) are available only to traffic that carries verifiable governance-enabling identity.

Validation gateways MUST require mTLS with a governance-enabling certificate bearing a recognized entity-class OID from the defined namespace. Requests without such a certificate MUST be rejected at the infrastructure level.

---

## 7. Contract Negotiation Fields (Requirements 10-15)

The first nine requirements address identification and evaluation. The remaining six address what two governance systems need from each other's entities to form a mutually binding contract autonomously.

These six fields are encoded as separate custom X.509 v3 extensions, each with its own OID in the entity-class OID namespace. This keeps the SAN URI at manageable length and allows each field to carry its own signature from its attesting authority. Custom extensions also allow critical/non-critical flagging per field, which the SAN URI does not support at the sub-field level.

### 7.1 Requirement 10: Actions Classification

The certificate MUST carry a field declaring what types of activities the entity is authorized to participate in, attested by an independent authority.

Actions classification is distinct from entity class (what the entity IS) and governance scope (what boundaries BIND it). Actions classification declares what the entity is authorized to DO.

**Defined activity categories** (minimum, extensible):

- CONSUMER: household purchases, personal services, subscription management
- COMMERCIAL: B2B procurement, contract negotiation, supply chain operations
- FINANCIAL: securities trading, banking transactions, insurance operations
- RESEARCH: data collection, model training collaboration, academic exchange
- GOVERNMENT: citizen services, regulatory administration, public procurement
- CLASSIFIED: defense, intelligence, national security workflows
- ORG_SENSITIVE: internal HR, legal, M&A, board-level operations

An entity MAY carry multiple concurrent activity classifications. A government procurement agent might carry both GOVERNMENT and COMMERCIAL.

**Encoding approach.** Custom X.509 v3 extension with a dedicated OID. Value: ASN.1 BIT STRING or SET OF ENUMERATED, allowing multiple concurrent classifications. When attested by a regulatory body, the extension carries the attesting authority's signature.

**Verification.** A receiving governance system proposing a commercial procurement workflow reads the actions classification from the incoming entity's certificate. If COMMERCIAL is not present, the entity is rejected before contract negotiation begins.

### 7.2 Requirement 11: Financial Commitment Ceiling

The certificate MUST carry the maximum financial commitment the entity is authorized to make, certified by an independent financial institution and verifiable by any counterparty from the certificate alone.

**Fields:**

- `max_per_transaction`: maximum amount per single transaction
- `max_per_contract`: maximum total value of a single contract
- `currency`: ISO 4217 currency code
- `attesting_institution`: certificate reference of the attesting financial institution
- `attestation_date`: when the ceiling was certified
- `attestation_expiry`: when the ceiling certification expires

**Responsibility model.** The receiving governance system reads the ceiling from the certificate and verifies the proposed transaction falls within it. The receiving governance system does NOT verify cumulative daily usage. That is the responsibility of the entity's own governance system. If the entity's governance system released the entity to negotiate, the entity's principal takes responsibility for cumulative limit management.

For additional certainty, the attesting financial institution can enforce cumulative ceilings through the external-party-triggered lifecycle mechanism (Section 8.3): when the entity approaches or exceeds its cumulative limit, the financial institution triggers certificate suspension.

**Encoding approach.** Custom X.509 v3 extension with a dedicated OID. Value: ASN.1 SEQUENCE containing the fields above. The attesting financial institution's signature over the extension provides independent verifiability.

### 7.3 Requirement 12: Data Handling Certification

The certificate MUST carry an independent third-party attestation that the entity and its infrastructure have been certified to handle data at specific sensitivity levels.

**Fields:**

- `certified_levels`: enumerated set of data sensitivity levels (e.g., PUBLIC, INTERNAL, CONFIDENTIAL, RESTRICTED, CLASSIFIED, or domain-specific classifications such as HIPAA, PCI-DSS, SOC2)
- `certifying_authority`: certificate reference of the certifying body
- `certification_standard`: reference to the certification standard or framework
- `certification_date`: when the certification was issued
- `certification_expiry`: when the certification expires
- `scope_limitations`: any domain or use-case limitations on the certification

**Distinction from model provenance (Section 6.5).** Model provenance certifies the model (training, bias evaluation, jurisdictional classification). Data handling certification certifies the entity AND its infrastructure: the runtime environment, the data storage, the network path, the operational controls. A model could be fully certified for provenance while running in an infrastructure that has never been evaluated for handling confidential financial data.

**Distinction from governance scope (Section 5).** Governance scope is set by the principal and says "I authorize this entity for confidential data." Data handling certification is attested by an independent certifier and says "I have evaluated this entity and its infrastructure and it meets the requirements for confidential data handling." The receiving governance system trusts the certifier, not the principal.

**Encoding approach.** Custom X.509 v3 extension with a dedicated OID. Value: ASN.1 SEQUENCE containing the fields above. Multiple certifications from different authorities coexist as a SEQUENCE OF certification entries.

### 7.4 Requirement 13: Insurance and Liability Attestation

The certificate MUST carry proof of current insurance or liability backing, attested by the insurance provider, specifying coverage type, coverage limits, and the jurisdictions where coverage applies.

**Fields:**

- `insurance_provider`: certificate reference of the insurance company
- `coverage_type`: enumerated (e.g., GENERAL_LIABILITY, PROFESSIONAL_LIABILITY, CYBER, ERRORS_AND_OMISSIONS, PRODUCT_LIABILITY)
- `coverage_limit`: maximum coverage amount
- `coverage_currency`: ISO 4217 currency code
- `covered_jurisdictions`: array of jurisdiction codes where coverage applies
- `policy_effective_date`: coverage start date
- `policy_expiry_date`: coverage end date
- `policy_conditions`: reference to terms (hash or URI, not the full terms)

**Connection to external-party-triggered lifecycle (Section 8.3).** The insurance provider that attests coverage in the certificate is the same entity that holds scoped trigger authority over the certificate. If coverage lapses, the insurance provider triggers certificate suspension. The attestation and the enforcement are two sides of the same trust relationship.

**Encoding approach.** Custom X.509 v3 extension with a dedicated OID. Value: ASN.1 SEQUENCE containing the fields above. Multiple insurance attestations (from different providers, for different coverage types) coexist as a SEQUENCE OF entries.

### 7.5 Requirement 14: Multi-Agent Workflow Authorization

The certificate MUST declare whether the entity's principal has authorized it for collaborative, multi-agent workflows or restricted it to solo, point-to-point interactions.

**Authorization levels** (defined enumeration):

- SOLO_ONLY: agent is authorized for direct bilateral interactions only. Cannot join teams, delegate sub-tasks, or participate in orchestrated multi-agent workflows.
- BILATERAL: agent can engage in bilateral negotiations and contracts but cannot participate in team assembly or multi-agent orchestration.
- MULTI_AGENT: agent is authorized to participate in multi-agent workflows, join teams assembled by other agents, and interact with peer agents within a workflow.
- TEAM_LEAD: agent is authorized to assemble and lead teams, delegate sub-tasks to other agents, and orchestrate multi-agent workflows.

**Additional fields:**

- `max_delegation_depth`: integer specifying how many layers of sub-delegation the entity may create (0 = cannot delegate, 1 = can delegate but delegates cannot sub-delegate, etc.)
- `max_team_size`: integer specifying maximum number of agents this entity can assemble into a team (applicable to TEAM_LEAD only)

**Verification.** A complex supply chain workflow requiring coordination among six agents from four organizations cannot safely include an entity authorized only for solo interactions. The risk profiles are fundamentally different. The receiving governance system reads the workflow authorization from the certificate before forming a contract that assumes collaborative capability.

**Encoding approach.** Custom X.509 v3 extension with a dedicated OID. Value: ASN.1 SEQUENCE containing the authorization level (ENUMERATED) and the optional depth/size fields (INTEGER).

### 7.6 Requirement 15: Dispute Resolution Binding

The certificate MUST declare what dispute resolution framework binds the entity's principal, verifiable by a counterparty before contract formation.

**Fields:**

- `framework`: identifier of the arbitration or dispute resolution framework (e.g., UNCITRAL, ICC, LCIA, AAA, SIAC, or a national framework identifier)
- `governing_jurisdiction`: jurisdiction whose laws govern dispute resolution
- `rules_version`: specific version of the framework's rules that apply
- `fallback_framework`: optional secondary framework if the primary is unavailable or rejected by the counterparty
- `binding_level`: MANDATORY (all disputes under this framework) or PREFERRED (default but negotiable at contract time)

**Contract formation interaction.** When two governance systems form a contract autonomously, the contract must specify how disputes are resolved. If both sides carry the same dispute resolution binding, the contract references it directly and no negotiation is needed for this term. If the bindings differ, the contract negotiation must resolve the conflict: one side accepts the other's framework, both agree on a third, or the contract cannot form. If one side carries no binding, the other's governance system must decide whether to proceed without a pre-agreed resolution mechanism.

**Encoding approach.** Custom X.509 v3 extension with a dedicated OID. Value: ASN.1 SEQUENCE containing the fields above. The framework and jurisdiction identifiers reference a registry of recognized frameworks.

---

## 8. Certificate Lifecycle

### 8.1 Automated High-Volume Issuance Pipeline

The AI-enabled economy will generate certificate volumes orders of magnitude larger than the current web PKI market. Every agent, every model version, every IoT device, every marketplace participant needs identity. Each multi-tenant entity generates concurrent DCs for each customer relationship.

The verification model is front-loaded: principals (humans and organizations) undergo rigorous CA verification once, through standard OV/EV processes. Once a principal holds a verified identity certificate, issuance of BICs, OBCs, and DCs for the autonomous entities they own and operate is automated against that trust anchor. The CA validates the requesting principal's certificate, confirms the requested scope falls within the governance contract template, and issues. No per-entity human review is required because the trust chain runs through the already-verified principal. For IoT devices, hardware attestation from the device's secure element replaces principal verification, following the same model already in production at CAs that serve IoT deployments.

The CA MUST support an automated CSR processing pipeline capable of:

- Validating the requesting principal's identity certificate before processing any entity CSR
- Receiving CSRs incorporating hardware attestation from tamper-resistant secure elements (for IoT devices and physical autonomous systems)
- Verifying the device manufacturer's root certificate and validating hardware security module attestation cryptographically
- Issuing entity identity certificates at throughput consistent with high-volume deployments
- Issuing class-appropriate certificates with class-appropriate extensions (not every entity class requires every extension)

### 8.2 Certificate Renewal and Rotation

Certificates in this schema follow standard X.509 renewal practices with one addition: version-bound expiry for language model service certificates (Section 6.5) requires re-issuance upon any model version update, not just at the expiry timestamp.

Certificate rotation for the three-certificate chain proceeds independently:

- **BIC rotation** is rare and represents a fundamental change to the entity (major capability upgrade, creator re-classification). Behavioral history references are updated to point to the new BIC.
- **OBC rotation** occurs on ownership transfer or governance contract change.
- **DC rotation** occurs frequently, driven by customer relationship changes, scope adjustments, and expiry-driven renewals.

### 8.3 External-Party-Triggered Lifecycle Events

A pre-authorized external party (a financial institution, regulator, insurance company, or tax authority) MAY hold automated API-level authority to trigger certificate lifecycle events at the issuing CA for certificates it does not own and did not issue.

The CA does not monitor the external party's business logic. It does not evaluate credit balances, insurance status, regulatory standing, or tax compliance. It receives a binary signal from a pre-authorized source (suspend, reinstate, or revoke), executes the lifecycle event, and propagates it through the push revocation network.

#### 8.3.1 Mechanism

1. An entity owner obtains identity certificates from a CA.
2. An external party (e.g., the owner's financial institution) is registered with the CA as an authorized lifecycle trigger for specific certificates. This registration is a trust relationship established at certificate issuance time or added later through a governed process.
3. A trigger condition occurs (e.g., the owner's credit balance drops below a threshold, an insurance policy lapses, a regulatory license is suspended).
4. The external party sends an automated signal to the CA: "suspend certificate [serial number], reason: [category]."
5. The CA suspends the certificate. Suspension propagates through the push revocation network to all registered gateways.
6. The entity's DC chains to the suspended certificate. Because the parent certificate is suspended, the DC fails chain validation. The entity cannot participate.
7. The condition is resolved. The external party sends: "reinstate certificate [serial number]."
8. The CA reinstates. Propagation through the push revocation network restores the entity's ability to participate.

#### 8.3.2 Scoped Trigger Authority

Trigger authority MUST be scoped to specific certificates, not to the owner's identity as a whole.

The CA MUST maintain a trigger authority registry: a set of bindings, each specifying:

- The external party's identity
- The specific certificate serial number(s) the external party has authority over
- The permitted lifecycle actions (suspend only, suspend and reinstate, or full revocation)

Each binding is independently configured. The owner (or a governance process established during service enrollment) preconfigures which external parties have trigger authority over which of their certificates, and what actions each party may trigger.

A bank that provides the credit facility backing a vehicle's financial transactions holds trigger authority over the vehicle's DC only. The bank cannot touch the owner's other entities. A utility company that bills through a home energy management agent holds trigger authority over that agent's DC. The utility cannot touch the owner's vehicle or other agents.

Each trigger relationship is independent. Multiple external parties MAY hold trigger authority over the same certificate, each for independent reasons, with independent suspend/reinstate authority. A financial hold and a regulatory hold can coexist; resolving one does not resolve the other.

The owner MUST be able to review, modify, and revoke trigger authority bindings through a governed process. Modifications to trigger authority require the same governance controls as any certificate lifecycle change.

When a certificate is renewed or re-issued, trigger authority bindings SHOULD transfer to the new certificate automatically unless explicitly removed.

#### 8.3.3 Prior Notification Protocol

Suspending a certificate that an entity uses for economic activity is an action that affects property. Most trigger events MUST follow a prior notification sequence before execution:

1. The external party sends a **trigger intent** to the CA (not an immediate suspension): "I intend to suspend certificate [serial] for reason [code]."
2. The CA validates the trigger authority binding and sends a **formal notification** to the certificate holder, specifying the requesting party, the intended action, the reason, and the cure period deadline.
3. During the **cure period**, the holder may resolve the underlying issue (top up the account, renew the insurance, provide missing documentation). If the external party or an independent verifier confirms resolution, the trigger intent is cancelled and no lifecycle event occurs.
4. If the cure period expires without resolution, the suspension executes and propagates through the push revocation network.

**Emergency override.** For fraud, imminent harm, or court-ordered emergency injunction, the notification period is zero. The CA executes immediate suspension with post-hoc notification to the holder. The trigger authority registry specifies, per binding, which reason codes qualify for emergency override.

**Per-binding notification configuration.** Each trigger authority binding specifies: whether prior notification is required, the notification period, the set of emergency override reasons, the notification method, and which party (the external party itself or an independent verifier) has authority to confirm a cure.

#### 8.3.4 Suspension vs. Revocation

The mechanism uses suspension with reinstatement capability, not permanent revocation. Suspension pauses the certificate. Reinstatement restores it. No re-issuance is required.

Existing certificate revocation is typically permanent: once revoked, a new certificate must be issued. The suspend/reinstate cycle is different. It handles transient holds (credit limit reached, insurance renewal pending, regulatory audit in progress) without the cost and disruption of full revocation and re-issuance.

### 8.4 Revocation and Re-Issuance

Permanent revocation follows standard X.509 practices. A revoked certificate MUST NOT be reinstated. Revocation of a BIC represents permanent decommissioning. Revocation of an OBC represents ownership termination. Revocation of a DC represents authorization withdrawal.

All revocation events propagate through the push revocation network (Section 9).

---

## 9. Push Revocation Network

### 9.1 Architecture Overview

The push revocation network is a publish-subscribe infrastructure that propagates certificate revocation and suspension events from the CA to all registered validation gateways proactively.

In the pull-based model (CRL and OCSP), verifiers periodically check whether a certificate has been revoked. Between checks, a revoked certificate remains accepted. At typical CRL update intervals (daily to weekly) and OCSP caching intervals (seconds to hours), that exposure window is operationally significant.

The push model inverts this. The CA publishes revocation and suspension events to all registered subscribers at the time the event occurs. Gateways receive the event within the propagation latency threshold, with no polling required and no caching interval creating exposure windows.

### 9.2 Propagation to Distributed Validation Gateways

Validation gateways register with the push revocation network as subscribers. Each gateway subscribes to specific push network nodes corresponding to its primary operational categories (entity class, jurisdiction, activity classification, or combination), so a financial-domain gateway operating in a specific jurisdiction receives revocation events only for that operational segment. At validation time, the gateway checks the presenting entity's class first; an entity class outside the gateway's operational scope triggers immediate rejection before any validity check occurs, providing an additional layer of local filtering independent of the push network.

The delivery mechanism MUST support:

- Reliable delivery with acknowledgment (events are not lost in transit)
- Ordered delivery per certificate (a reinstatement cannot arrive before the suspension it resolves)
- Node-scoped delivery organized by operational category, with gateways subscribing to nodes matching their deployment profile

### 9.3 Latency Requirements

The propagation latency from the CA's revocation decision to the last registered gateway's acknowledgment MUST be substantially shorter than OCSP polling intervals. The target is sub-second propagation for the majority of events, with a hard ceiling of seconds rather than minutes.

A specific millisecond SLA is not defined here. Latency targets are deployment-dependent, reflecting network topology, gateway count, and message infrastructure capacity. What matters architecturally is that the push model closes the exposure window that pull-based models leave open.

### 9.4 Comparison with OCSP/CRL Models

| Property | CRL | OCSP | Push Revocation |
|---|---|---|---|
| Initiation | Verifier polls | Verifier queries | CA pushes |
| Latency | Hours to days | Seconds to hours (depends on cache) | Sub-second (target) |
| Verifier load | Periodic download | Per-certificate query | Event-driven receipt |
| Exposure window | Between CRL updates | Between OCSP queries or cache expiry | None after receipt |
| Scalability concern | CRL size growth | Responder load under query volume | Fan-out to gateway population |
| Suspension support | Yes (with CRL reason codes) | Yes (via response status) | Yes (native, with reinstatement) |

Push revocation does not replace CRL or OCSP. It supplements them. Gateways that do not support push revocation fall back to OCSP queries. CRLs remain the authoritative record of revoked certificates. Push revocation is the performance layer that closes the exposure window for gateways that subscribe to it.

### 9.5 Integration with Governance Gateway Infrastructure

Validation gateways that serve as governance enforcement points are the primary consumers of push revocation. When a gateway receives a revocation or suspension event:

1. The gateway updates its local certificate status cache immediately.
2. Any in-progress request from the affected entity is terminated according to the gateway's configured policy (immediate termination or graceful completion of the current action).
3. All subsequent requests from the affected entity are rejected.
4. The event is logged in the gateway's governance evidence record.

For suspension events followed by reinstatement, the gateway restores the entity's participation upon receiving the reinstatement event. The suspension and reinstatement are both recorded in the governance evidence record.

---

## 10. Conformance and Verification

### 10.1 Minimum Conformance Requirements

A certificate that conforms to this specification MUST include:

1. An entity-class OID from the defined namespace as an EKU extension (Section 4)
2. A governance scope parameter as a mandatory field in the SAN URI (Section 5)
3. A valid chain of BIC, OBC, and DC (Section 3), with all three simultaneously valid

A conforming certificate MAY include any or all of the contract negotiation fields defined in Section 7. A certificate that includes a contract negotiation field MUST encode it according to the defined structure.

A conforming validation gateway MUST:

1. Require presentation of a conforming certificate via mTLS before processing any request
2. Verify the entity-class OID against the defined namespace
3. Verify the three-certificate chain (BIC, OBC, DC all valid and properly linked)
4. Parse and evaluate the governance scope parameter against its own policy requirements
5. Support push revocation subscription for the CA trust roots it accepts

### 10.2 Verification Procedures for Counterparty Governance Systems

A receiving governance system evaluating an incoming entity SHOULD follow this verification sequence:

1. **Certificate chain verification.** Verify the X.509 certificate chain from DC to OBC to BIC to the CA trust root.
2. **Entity class verification.** Extract the entity-class OID and confirm it is a recognized class for the proposed interaction.
3. **Governance scope verification.** Parse the SAN URI governance scope parameter and evaluate against minimum requirements.
4. **Revocation status check.** Verify the certificate has not been revoked or suspended. If the gateway subscribes to push revocation, use the local status cache. Otherwise, query OCSP.
5. **Contract negotiation field evaluation.** For each contract negotiation field present in the certificate, evaluate against the governance system's policy requirements for this interaction type.
6. **Behavioral history evaluation.** If the interaction type requires a minimum behavioral track record, follow the BIC's behavioral history reference and evaluate the record.

### 10.3 Trust Store Requirements

A governance system that accepts governance-enabling certificates MUST maintain a trust store containing:

- The CA trust roots it accepts for entity identity certificates
- The attesting authority trust roots it accepts for independently attested extensions (financial institutions, insurance providers, certifying bodies, regulatory authorities)
- The push revocation network subscription configuration for each accepted CA trust root

---

## 11. Security Considerations

### 11.1 Threat Model

The following threat categories are relevant to this architecture:

**Certificate forgery.** An attacker creates a fraudulent certificate with false governance-enabling extensions. Mitigated by the CA's issuance controls and Certificate Transparency (Section 11.3).

**Attestation forgery.** An attacker creates a fraudulent attestation from a financial institution, insurance provider, or certifying body. Mitigated by independent signature verification: each attesting authority signs its own extension, and the verifier checks the attesting authority's signature independently of the CA's signature.

**Revocation delay exploitation.** An attacker uses a compromised entity's certificate during the window between compromise and revocation propagation. Mitigated by push revocation (Section 9), which shrinks the window to sub-second latency.

**Behavioral history manipulation.** An attacker modifies an entity's behavioral history to conceal violations or inflate quality metrics. Mitigated by the structural binding of behavioral history to the BIC: the history is a chain of signed evidence entries, each signed by the governance system that produced it. Modifying any entry breaks the chain.

**Trigger authority abuse.** An authorized external party triggers unwarranted certificate suspension. Mitigated by the governance controls on trigger authority registration (Section 8.3.2): the owner configures and can revoke trigger authority, and all trigger events are logged and auditable.

### 11.2 CA Compromise Scenarios

If a CA issuing governance-enabling certificates is compromised, the attacker can issue certificates with arbitrary governance-enabling extensions. This is a catastrophic scenario equivalent to a root CA compromise in the current web PKI.

Mitigations:

- Certificate Transparency (Section 11.3) makes all issued certificates publicly auditable, enabling detection of unauthorized issuance.
- Governance systems SHOULD accept certificates from multiple independent CAs and cross-validate unusual certificates.
- The multi-authority attestation model provides a partial defense: even if the CA is compromised, independently attested extensions require separate compromise of each attesting authority.

### 11.3 Certificate Transparency Integration

All governance-enabling certificates SHOULD be logged to Certificate Transparency (CT) logs. CT logging enables public auditability of certificate issuance and detection of mis-issued certificates.

CT logging for governance-enabling certificates follows the same model as CT logging for web PKI certificates: the CA submits the pre-certificate to a CT log, receives a Signed Certificate Timestamp (SCT), and embeds the SCT in the issued certificate.

---

## 12. Privacy Considerations

### 12.1 Selective Disclosure Through Attribute Certificate Separation

Governance-enabling identity carries sensitive attributes: financial commitment ceilings, insurance coverage, data handling certifications, actions classifications. Not every counterparty needs to see every attribute.

The multi-authority attestation architecture (Section 2.4) provides a practical selective disclosure mechanism using standard X.509 tooling. Sensitive attributes are carried in separate attribute certificates, not in the identity certificate itself. The identity certificate chain (BIC, OBC, DC) is presented to every counterparty because it carries entity class, governance scope, and ownership binding. Attribute certificates are presented selectively: a counterparty that needs proof of insurance receives the insurance attribute certificate. It does not receive the financial commitment ceiling attribute certificate. The entity chooses which attribute certificates to present based on what the interaction requires.

This is credential-level selective disclosure. The counterparty sees the full content of any attribute certificate it receives, but it does not see attribute certificates that were not presented.

### 12.2 Attribute Privacy Mechanisms

When a counterparty needs to verify that an attribute satisfies a threshold without learning the actual value, multiple mechanisms exist:

- **Predicate query.** The counterparty calls the attesting authority with a yes/no question ("can this entity's financial ceiling accommodate a $100,000 commitment?"). The authority answers from its current data without disclosing the actual value. This uses standard API infrastructure, requires no new cryptography, and reflects the authority's current state rather than the state at attribute cert issuance time. The pattern is well established in payment authorization.
- **Zero-knowledge proofs.** The entity generates a cryptographic proof locally that a predicate is satisfied, without revealing the underlying value. The counterparty verifies the proof without contacting the authority. BBS+ signatures, currently under standardization at IETF, are the most mature candidate for this approach applied to attribute certificates.

Either model is valid. The choice depends on the CA's infrastructure, the counterparty's latency tolerance, and the deployment's privacy requirements. The identity schema defined in this specification supports both: the attribute certificate boundary (Section 2.4) is the integration point regardless of which privacy mechanism is used.

### 12.3 Graduated Privacy

Different interaction types require different levels of attribute disclosure. A low-value consumer transaction requires fewer attribute certificates than a high-value cross-jurisdictional financial contract. A conforming governance system SHOULD support graduated disclosure policies that match the set of requested attribute certificates to the interaction's risk profile.

---

## 13. Relationship to Existing Standards

### 13.1 X.509 v3 (Base Substrate)

This specification extends X.509 v3 with governance-enabling extensions. It does not replace X.509 v3. All existing X.509 v3 infrastructure, tooling, and operational practices remain applicable.

### 13.2 Existing Identity Standards and Protocols

The companion requirements paper [TODO: INSERT ZENODO DOI] evaluates the current identity standards landscape (including SPIFFE, AIC, AGTP, AAuth, W3C DIDs, and Verifiable Credentials) against the fifteen requirements defined for governance-enabling identity. None of the evaluated standards satisfies the full requirement set. This specification provides the construction that does.

---

## 14. Patent Notice

This specification is published by Victor Davidenko / Scarp Protocol Inc. under Creative Commons Attribution 4.0 International (CC BY 4.0). The author has filed patent applications that establish priority dates predating this publication and that cover specific mechanisms related to the subject matter described herein. Contact: victor@scarpprotocol.com.

---

## 15. References

### Normative References

- RFC 2119 — Key words for use in RFCs to Indicate Requirement Levels
- RFC 5280 — Internet X.509 Public Key Infrastructure Certificate and Certificate Revocation List (CRL) Profile (covers X.509 v3 extensions, Subject Alternative Name, Extended Key Usage, OID structure, certificate chain validation)
- RFC 5755 — An Internet Attribute Certificate Profile for Authorization
- RFC 6960 — X.509 Internet Public Key Infrastructure Online Certificate Status Protocol - OCSP
- RFC 6962 — Certificate Transparency
- ISO 3166-1 — Codes for the representation of names of countries and their subdivisions
- ISO 4217 — Codes for the representation of currencies

### Informative References

- RFC 5912 — New ASN.1 Modules for the Public Key Infrastructure Using X.509 (PKIX)
- RFC 8555 — Automatic Certificate Management Environment (ACME)
- RFC 6818 — Updates to the Internet X.509 Public Key Infrastructure Certificate and Certificate Revocation List (CRL) Profile
- ITU-T X.680 — Abstract Syntax Notation One (ASN.1): Specification of basic notation
- ITU-T X.690 — ASN.1 encoding rules: BER, CER, and DER
- Davidenko, V. (2026). "Universal Identity for the AI-Enabled Economy: Requirements for Governance-Enabling Identity in an Autonomous World." [TODO: INSERT ZENODO DOI]
- Davidenko, V. (2026). "Governable AI: Infrastructure for Accountable Autonomy." DOI: 10.5281/zenodo.21927016.

---

## Appendix A: Certificate Size Considerations

Each governance-enabling extension adds to the certificate's total size. X.509 has no hard size limit, but practical limits exist in TLS handshakes and constrained devices.

Estimated per-extension overhead: 100-300 bytes depending on field count and encoding. The six contract negotiation extensions (Section 7) add approximately 600-1,800 bytes. For comparison, a typical web certificate with SAN entries and Certificate Transparency timestamps is 1-3 KB.

An entity identity certificate with all fifteen requirements encoded might reach 4-6 KB. This is within practical limits for enterprise use. For constrained IoT deployments, the CA SHOULD issue class-appropriate certificates with class-appropriate extensions: an IoT sensor does not need financial commitment ceiling or dispute resolution binding fields.

## Appendix B: Implementation Considerations for Multi-Authority Attestation

Four of the six contract negotiation fields (financial commitment ceiling, data handling certification, insurance attestation, and actions classification when attested by a regulatory body) require signatures from authorities other than the issuing CA.

The three implementation approaches described in Section 2.4 each have trade-offs:

**Cross-signed extensions** are the simplest to implement within existing X.509 tooling but require the CA to include the attesting authority's signature in the certificate at issuance time. Updating an attestation requires certificate re-issuance.

**Nested certificates** allow attestation updates without re-issuing the main certificate (the nested certificate is replaced) but increase certificate size and verification complexity.

**Attribute certificates (RFC 5755)** provide the most flexible model: each attesting authority manages its own attribute certificates independently, linked to the holder's identity certificate. Attestation updates do not require re-issuance of the identity certificate. Multiple authorities operate independently. The trade-off is that verifiers must resolve and verify attribute certificates in addition to the identity certificate.

For deployments with frequently changing attestations (e.g., insurance coverage that renews annually, financial ceilings that adjust quarterly), attribute certificates offer the lowest operational overhead because attestation changes do not trigger identity certificate re-issuance.

---

*End of specification.*

*Version 0.1.0. September 20, 2026. Victor Davidenko, Scarp Protocol Inc.*

*This document is published under Creative Commons Attribution 4.0 International (CC BY 4.0).*
