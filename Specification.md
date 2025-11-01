# Signet Agreements Protocol

### Abstract

This document specifies the Agreements Protocol, a JSON-based data standard for the creation, execution, and verification of digital agreements. The protocol defines structures for metadata, variables, content representation, execution modeling, and optional verifiable credential packaging. Its purpose is to promote interoperability among legal and technical systems that manage digitally signed agreements, enabling consistent data exchange, validation, and proof verification across implementations.

### Revision History

| Version | Date | Status | Notes |
| ----- | ----- | ----- | ----- |
| 1.0.0-draft | 2025-11-01 | Draft | Initial draft converted to Community Specification format, including full schema annexes and interoperability profiles. |

### Version: 1.0.0-draft (2025-11-01)

### Status: Draft

This specification is subject to the Community Specification License 1.0.

---

## Contents

1. Scope
2. Normative references
3. Terms and definitions
4. Conformance
5. Overview 
6. Metadata
7. Variables
8. Content Representation
9. Execution Model
10. Template Structure
11. Verifiable Credential Wrapper
12. Security Considerations
Annex A (informative) Example Agreement
Annex B (normative) JSON Schemas
Annex D (informative) Interoperability Profiles
Annex E (informative) Worked JSON Examples
Annex C (informative) Change history
Bibliography

---

## Foreword

Attention is drawn to the possibility that some of the elements of this document may be the subject of patent rights. No party shall be held responsible for identifying any or all such patent rights.

Any trade name used in this document is information given for the convenience of users and does not constitute an endorsement.

This document was prepared by the Agreements Protocol community under the Legal Engineering Standards Association.

Any feedback or questions on this document should be directed to the specification repository.

THESE MATERIALS ARE PROVIDED “AS IS.” The Contributors and Licensees expressly disclaim any warranties (express, implied, or otherwise), including implied warranties of merchantability, non‑infringement, fitness for a particular purpose, or title, related to the materials. The entire risk as to implementing or otherwise using the materials is assumed by the implementer and user. IN NO EVENT WILL THE CONTRIBUTORS OR LICENSEES BE LIABLE TO ANY OTHER PARTY FOR LOST PROFITS OR ANY FORM OF INDIRECT, SPECIAL, INCIDENTAL, OR CONSEQUENTIAL DAMAGES OF ANY CHARACTER FROM ANY CAUSES OF ACTION OF ANY KIND WITH RESPECT TO THIS DELIVERABLE OR ITS GOVERNING AGREEMENT, WHETHER BASED ON BREACH OF CONTRACT, TORT (INCLUDING NEGLIGENCE), OR OTHERWISE, AND WHETHER OR NOT THE OTHER MEMBER HAS BEEN ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

---

## Introduction

This specification defines the Agreements Protocol, a JSON‑based standard for creating and executing legally binding agreements with blockchain‑derived proofs and verifiable state transitions. The protocol provides a standardized way to: (a) define agreement metadata; (b) declare typed variables; (c) represent agreement content with variable interpolation; (d) model execution as a deterministic finite state machine (DFSM); and (e) package agreements for issuance as W3C Verifiable Credentials.

The Agreements Protocol is technology‑neutral with respect to blockchain networks and cryptographic primitives, while providing bindings for widely used mechanisms such as EIP‑712 signatures and EVM transaction receipts. The goal is interoperability across tooling and implementations.

---

## 1. Scope

This document specifies:

 * a data model for agreement templates and instances;  
 * a variable model and interpolation rules;  
 * content representations based on Markdown and MDAST;  
 * an execution model based on a deterministic finite state machine (DFSM);  
 * a packaging model for issuance as a W3C Verifiable Credential; and  
 * conformance criteria for producers and consumers.

This document does not specify:

* legal enforceability requirements;  
* user interface requirements;  
* specific blockchain network requirements beyond informative bindings provided in annexes.

---

## 2. Normative references

The following documents are normatively referenced and are indispensable for application of this specification. For dated references, only the edition cited applies; for undated references, the latest edition applies.

 * RFC 2119, *Key words for use in RFCs to Indicate Requirement Levels* (IETF, March 1997).  
 * W3C Recommendation, *Verifiable Credentials Data Model 1.1*, 2022‑03‑03.  
 * EIP‑712, *Ethereum Typed Structured Data Hashing and Signing*, final (2023‑07).  
 * IETF Draft, *JSON Schema: Core and Validation Specifications* (draft‑2020‑12).  
 * CommonMark, *The CommonMark Spec v0.30* (2022‑04).  
 * Unified Collective, *MDAST Specification v3* (2023‑05).  
 * Unified Collective, *Unist Specification v3* (2023‑05).

---

## 3. Terms and definitions

For the purposes of this document, the following terms and definitions apply.

3.1. agreement instance  
 concrete instantiation of an agreement template populated with variable values and an execution state

3.2. agreement template  
 parameterized specification of an agreement, including metadata, variables, content, execution model, and optional credential packaging

3.3. consumer  
 software that reads, validates, or processes an agreement instance

3.4. producer  
 software that creates agreement templates or instances

3.5. DFSM (deterministic finite state machine)  
 finite state machine in which transitions are uniquely determined by inputs and current state

3.6. variable  
 named, typed input to an agreement template

3.7. content  
 textual representation of the agreement, in Markdown or MDAST, with variable interpolation

3.8. verifiable credential (VC)  
 credential as defined by the W3C Verifiable Credentials Data Model

---

## 4. Conformance

4.1. General

An implementation conforms to this specification if it satisfies all MUST‑level requirements that apply to its declared role(s).

4.2. Roles

 * Producer: A conforming producer MUST generate agreement templates and instances that satisfy the schema requirements defined in Clauses 6 through 11\.  
 * Consumer: A conforming consumer MUST validate agreement instances according to Clauses 6 through 11 and the conformance statements in this clause.

4.3. Document processing

 * A conforming processor MUST treat the key words “MUST”, “MUST NOT”, “REQUIRED”, “SHALL”, “SHALL NOT”, “SHOULD”, “SHOULD NOT”, “RECOMMENDED”, “MAY”, and “OPTIONAL” as described in RFC 2119\.  
 * A conforming processor MUST reject inputs that fail schema validation.  
 * Interpolation of variables in content SHALL NOT change the underlying variable definitions.

---

## 5. Overview

5.1. Components

The protocol consists of the following components:

* Metadata (Clause 6);  
* Variables (Clause 7);  
* Content representation (Clause 8);  
* Execution model (Clause 9);  
* Template structure (Clause 10);  
* Verifiable Credential wrapper (Clause 11).

5.2. Example

Annex A provides an informative example illustrating the mapping from a simple consulting agreement to the protocol components.

---

## 6. Metadata

6.1. Purpose

Metadata provides identification and context for agreement templates and instances.

6.2. Requirements

6.2.1. A template or instance metadata object SHALL be a JSON object with the properties defined in Table 1\.

6.2.2. Properties identified as REQUIRED in Table 1 MUST be present.

6.2.3. Values MUST satisfy the associated JSON Schema for each property.

6.3. Properties

Table 1 — Metadata properties

| Property | Requirement | Type | Description |
| ----- | ----- | ----- | ----- |
| id | REQUIRED | string | unique identifier for the agreement instance; SHOULD be a DID (e.g., `did:example:…`). |
| templateId | REQUIRED for instances | string | identifier for the template; SHOULD be a DID with method `template`. |
| version | REQUIRED | string | semantic version identifier of the template or instance. |
| createdAt | REQUIRED | date‑time | creation timestamp in ISO 8601\. |
| name | REQUIRED | string | human‑readable name of the template. |
| author | REQUIRED | string | author or organization name. |
| description | REQUIRED | string | textual description of scope and purpose. |

6.4. Examples

See Annex B for the complete JSON Schema and Annex A for an example instance.

---

## 7. Variables

7.1. Purpose

Variables provide typed inputs that can be referenced throughout an agreement.

7.2. Requirements

7.2.1. A variables array SHALL contain objects with the properties defined in Table 2\.  
7.2.2. Each variable id MUST be unique within a template.  
7.2.3. A variable type MUST be one of: string, number, address, dateTime. Additional types MAY be defined by profiles.  
7.2.4. Validation rules MAY be provided using the validation object (e.g., required, min, max, pattern).

7.3. Properties

Table 2 — Variable properties

| Property | Requirement | Type | Description |
| ----- | ----- | ----- | ----- |
| id | REQUIRED | string | unique identifier. |
| type | REQUIRED | string | enumerated type. |
| name | REQUIRED | string | display name. |
| description | REQUIRED | string | description of purpose. |
| validation | OPTIONAL | object | constraints. |

7.4. Referencing variables

Variables MAY be referenced using any of the following forms:

 * MDAST node: `{ "type": "variable", "id": "…", "property": "…" }`.  
 * Markdown directive: `:variable{id='…' property='…'}`.  
 * JSON template substitution: `${variableId}`.

---

## 8. Content Representation

8.1. General

Content SHALL be represented as either Markdown (MD) or Markdown Abstract Syntax Tree (MDAST).

8.2. Requirements

8.2.1. A content object SHALL contain `type` and `data` properties.  
8.2.2. If `type` equals `mdast`, `data` SHALL conform to the MDAST schema.  
8.2.3. If `type` equals `md`, `data` SHALL be a string encoded in UTF‑8.  
8.2.4. Implementations SHOULD support variable interpolation using the forms specified in 7.4.

---

## 9. Execution Model

9.1 General

The execution model defines the lifecycle of an agreement instance using a DFSM.

9.2. Requirements

9.2.1. An execution object SHALL contain `type` and `data` properties.  
9.2.2. The `type` property SHALL be `dfsm`.  
9.2.3. The `data` property SHALL conform to the DFSM schema defined in Annex B.  
9.2.4. The DFSM SHALL define states, inputs, and transitions.

9.3. States

States SHALL be an array of strings naming each lifecycle state (e.g., `PENDING_SIGNATURE`, `SIGNED`).

9.4. Inputs

Inputs SHALL be a map of input definitions, each including: id, type, displayName, description; and MAY include: schema, value, signer.

9.5. Transitions

Transitions SHALL define `from`, `to`, and a non‑empty array of conditions. A condition type of `isValid` over specified inputs is RECOMMENDED.

---

## 10. Template Structure

10.1. General

A template encapsulates metadata, variables, content, and execution model in a single JSON structure.

10.2. Requirements

10.2.1. A template object SHALL validate against the normative JSON Schema referenced in Annex B.  
10.2.2. A template object MAY be identified by a DID with method `template`.

---

## 11. Verifiable Credential Wrapper

11.1. General

An agreement template or instance MAY be packaged within a W3C Verifiable Credential.

11.2. Requirements

11.2.1. When packaged as a VC, the credentialSubject SHALL carry the agreement payload.  
11.2.2. Proofs MAY use EIP‑712; issuers and verification methods SHALL be expressed according to the VC Data Model.

---

## 12. Security Considerations

12.1. General

Implementers SHOULD review signature validation, credential verification, and transaction provenance.

12.2. Signatures

Signatures SHALL be validated according to the algorithm and chain context declared in metadata. If EIP‑712 is used, the message domain and type definitions MUST match those provided in the agreement template.

12.3. Replay protection

Implementations SHOULD bind agreement instances to a specific network or execution context to prevent replay of proofs across unrelated systems.

12.4. Credential integrity

When using W3C Verifiable Credentials, the `proof` object MUST include cryptographic integrity protection. Consumers MUST validate issuer identifiers and revocation status where available.

---

## Annex A. (informative) Example Agreement

Examples of Markdown and MDAST content, variables, and a complete JSON template are provided.

---

## Annex B. (normative) JSON Schemas

This annex is normative. All schemas use JSON Schema draft‑2020‑12.

### B.1. Common definitions

```JSON
{  
  "$id": "https://example.org/schemas/common.defs.json",  
  "$schema": "https://json-schema.org/draft/2020-12/schema",  
  "title": "Agreements Protocol — Common Definitions",  
  "type": "object",  
  "$defs": {  
    "did": { "type": "string", "pattern": "^did:\[a-z0-9\]+:.\[^  
\]\*$" },  
    "didTemplate": { "type": "string", "pattern": "^did:template:.\[^  
\]\*$" },  
    "isoDateTime": { "type": "string", "format": "date-time" },  
    "address": { "type": "string", "pattern": "^0x\[a-fA-F0-9\]{40}$" },  
    "mdastNode": {  
      "type": "object",  
      "required": \["type"\],  
      "properties": {  
        "type": { "type": "string" },  
        "value": { "type": "string" },  
        "depth": { "type": "integer", "minimum": 1 },  
        "children": {  
          "type": "array",  
          "items": { "$ref": "\#/$defs/mdastNode" }  
        },  
        "id": { "type": "string" },  
        "property": { "type": "string" }  
      },  
      "additionalProperties": true  
    }  
  }  
}
```

### B.2. Metadata schema

```JSON
{  
  "$id": "https://example.org/schemas/metadata.schema.json",  
  "$schema": "https://json-schema.org/draft/2020-12/schema",  
  "title": "Agreements Protocol — Metadata",  
  "type": "object",  
  "required": \["id", "templateId", "version", "createdAt", "name", "author", "description"\],  
  "properties": {  
    "id": { "$ref": "common.defs.json\#/$defs/did", "description": "Unique identifier for the agreement instance" },  
    "templateId": { "$ref": "common.defs.json\#/$defs/didTemplate", "description": "Identifier for the template type" },  
    "version": { "type": "string" },  
    "createdAt": { "$ref": "common.defs.json\#/$defs/isoDateTime" },  
    "name": { "type": "string" },  
    "author": { "type": "string" },  
    "description": { "type": "string" }  
  },  
  "additionalProperties": true  
}
```

### B.3. Variables schema

```JSON
{  
  "$id": "https://example.org/schemas/variables.schema.json",  
  "$schema": "https://json-schema.org/draft/2020-12/schema",  
  "title": "Agreements Protocol — Variables",  
  "type": "array",  
  "items": {  
    "type": "object",  
    "required": \["id", "type", "name", "description"\],  
    "properties": {  
      "id": { "type": "string", "description": "Unique identifier for the variable" },  
      "type": { "type": "string", "enum": \["string", "number", "address", "dateTime"\], "description": "Data type of the variable" },  
      "name": { "type": "string" },  
      "description": { "type": "string" },  
      "validation": {  
        "type": "object",  
        "properties": {  
          "required": { "type": "boolean" },  
          "min": { "type": "number" },  
          "max": { "type": "number" },  
          "pattern": { "type": "string" },  
          "minLength": { "type": "integer", "minimum": 0 },  
          "maxLength": { "type": "integer", "minimum": 0 }  
        },  
        "additionalProperties": true  
      }  
    },  
    "additionalProperties": true  
  }  
}
```

### B.4. Content schema

```JSON
{  
  "$id": "https://example.org/schemas/content.schema.json",  
  "$schema": "https://json-schema.org/draft/2020-12/schema",  
  "title": "Agreements Protocol — Content",  
  "type": "object",  
  "required": \["type", "data"\],  
  "properties": {  
    "type": { "type": "string", "enum": \["mdast", "md"\], "description": "Content format type" },  
    "data": {  
      "oneOf": \[  
        {  
          "type": "string",  
          "description": "Markdown source",  
          "deprecated": false  
        },  
        {  
          "$ref": "common.defs.json\#/$defs/mdastNode",  
          "description": "MDAST root node",  
          "examples": \[ { "type": "root", "children": \[\] } \]  
        }  
      \]  
    }  
  },  
  "additionalProperties": false  
}
```

### B.5. Execution (DFSM) schema

```JSON
{  
  "$id": "https://example.org/schemas/execution-dfsm.schema.json",  
  "$schema": "https://json-schema.org/draft/2020-12/schema",  
  "title": "Agreements Protocol — DFSM Execution",  
  "type": "object",  
  "required": \["states", "inputs", "transitions"\],  
  "properties": {  
    "states": { "type": "array", "items": { "type": "string" }, "minItems": 1 },  
    "inputs": {  
      "type": "object",  
      "additionalProperties": {  
        "type": "object",  
        "required": \["id", "type", "displayName", "description"\],  
        "properties": {  
          "id": { "type": "string" },  
          "type": { "type": "string" },  
          "schema": { "type": "string" },  
          "displayName": { "type": "string" },  
          "description": { "type": "string" },  
          "value": { "type": "object" },  
          "signer": { "type": "string" }  
        },  
        "additionalProperties": true  
      }  
    },  
    "transitions": {  
      "type": "array",  
      "minItems": 1,  
      "items": {  
        "type": "object",  
        "required": \["from", "to", "conditions"\],  
        "properties": {  
          "from": { "type": "string" },  
          "to": { "type": "string" },  
          "conditions": {  
            "type": "array",  
            "minItems": 1,  
            "items": {  
              "type": "object",  
              "required": \["type"\],  
              "properties": {  
                "type": { "type": "string", "enum": \["isValid"\] },  
                "inputs": { "type": "array", "items": { "type": "string" }, "minItems": 1 }  
              },  
              "additionalProperties": true  
            }  
          }  
        },  
        "additionalProperties": true  
      }  
    }  
  },  
  "additionalProperties": false  
}
```

### B.6. Execution wrapper schema

```JSON
{  
  "$id": "https://example.org/schemas/execution.schema.json",  
  "$schema": "https://json-schema.org/draft/2020-12/schema",  
  "title": "Agreements Protocol — Execution Wrapper",  
  "type": "object",  
  "required": \["type", "data"\],  
  "properties": {  
    "type": { "type": "string", "enum": \["dfsm"\] },  
    "data": { "$ref": "execution-dfsm.schema.json" }  
  },  
  "additionalProperties": false  
}
```

### **B.7. Template schema**

```JSON
{  
  "$id": "https://example.org/schemas/template.schema.json",  
  "$schema": "https://json-schema.org/draft/2020-12/schema",  
  "title": "Agreements Protocol — Template",  
  "type": "object",  
  "required": \["metadata", "variables", "content", "execution"\],  
  "properties": {  
    "metadata": { "$ref": "metadata.schema.json" },  
    "variables": { "$ref": "variables.schema.json" },  
    "content": { "$ref": "content.schema.json" },  
    "execution": { "$ref": "execution.schema.json" }  
  },  
  "additionalProperties": false  
}
```

### B.8. Verifiable Credential wrapper schema (optional)

```JSON
{  
  "$id": "https://example.org/schemas/vc-wrapper.schema.json",  
  "$schema": "https://json-schema.org/draft/2020-12/schema",  
  "title": "Agreements Protocol — Verifiable Credential Wrapper",  
  "type": "object",  
  "required": \["@context", "type", "issuer", "issuanceDate", "credentialSubject"\],  
  "properties": {  
    "@context": { "type": "array", "items": { "type": "string" }, "minItems": 1 },  
    "type": { "type": "array", "items": { "type": "string" }, "minItems": 1 },  
    "issuer": { "type": "string" },  
    "issuanceDate": { "$ref": "common.defs.json\#/$defs/isoDateTime" },  
    "credentialSubject": {  
      "type": "object",  
      "required": \["agreement"\],  
      "properties": {  
        "agreement": { "$ref": "template.schema.json" }  
      },  
      "additionalProperties": true  
    },  
    "proof": { "type": "object" }  
  },  
  "additionalProperties": true  
}
```

### B.9. Conformance note

A conforming producer or consumer MUST validate objects against the schemas above. Implementations MAY host these schemas at stable URIs and reference them using `$ref`.

---

## Annex D. (informative) Interoperability Profiles

This annex is informative. Profiles constrain options in Clauses 6–11 to promote interoperability. A profile identifies mandatory algorithms, encodings, and identifier methods. The keyword MUST in this annex applies within the scope of a given profile.

### D.1. EVM \+ EIP‑712 Profile (AP-evm-712)

Scope: agreements whose inputs and proofs are produced on EVM-compatible chains, using EIP‑712 signing.

D.1.1. Identifiers  
 * `metadata.id` and instance identifiers SHOULD be DIDs.  
 * `metadata.templateId` SHOULD be a DID using `did:web` or a registry-specific method.  
 * Chain identification SHOULD use EIP‑155 numeric `chainId` values.

D.1.2. Cryptographic primitives  
 * Signatures MUST use secp256k1 over Keccak‑256 hashed EIP‑712 typed data.  
 * Transaction receipts, when referenced, MUST include block hash, transaction hash, and log index.

D.1.3. Inputs

Inputs that represent signatures MUST provide a canonical EIP‑712 domain (`name`, `version`, `chainId`, `verifyingContract`, `salt` where applicable) and typed `message` as defined by the contract or off‑chain schema.

D.1.4. Verification

Consumers MUST reconstruct the EIP‑712 digest from the declared domain and types and recover the signer address. If `inputs.<id>.signer` is present, the recovered address MUST match.

### D.2. DID Methods Profile (AP-did)

Scope: DID usage for templates and issuers.

D.2.1. Supported methods  
 * `did:web` for web‑hosted identifiers.  
 * `did:key` for key‑derived identifiers.  
 * `did:ethr` for Ethereum‑anchored identifiers.

D.2.2. Requirements  
 * Producers SHOULD publish template metadata at a stable URL resolvable from `did:web`.  
 * Consumers SHOULD resolve DID Documents to obtain verification methods and key material for proof validation.

### D.3. VC Packaging Profile (AP-vc)

Scope: packaging an agreement as a W3C Verifiable Credential.

D.3.1. Contexts and types  
 * `@context` MUST include `https://www.w3.org/2018/credentials/v1`.  
 * `type` SHOULD include `VerifiableCredential` and a domain‑specific type such as `AgreementCredential`.

D.3.2. Proof suites  
 * Recommended: `EcdsaSecp256k1RecoverySignature2020` for EVM ecosystems; `Ed25519Signature2020` for Ed25519 ecosystems.  
 * Issuers MUST include `verificationMethod` identifiers that resolve under the issuer DID.

D.3.3. Revocation and status
 * If revocation is supported, credentials SHOULD include a `credentialStatus` property resolvable to a status list or endpoint.

---

## **Annex E (informative) Worked JSON Examples**

This annex is informative. The following examples are consistent with Annex B schemas.

### E.1. Minimal template

```JSON
{  
  "metadata": {  
    "id": "did:web:agreements.example.org:contracts:basic-consulting:instance-001",  
    "templateId": "did:web:agreements.example.org:templates:basic-consulting",  
    "version": "1.0.0",  
    "createdAt": "2025-10-15T12:34:56Z",  
    "name": "Basic Consulting Agreement",  
    "author": "Legal Engineering Standards Association",  
    "description": "One-hour consulting in exchange for USDC payment."  
  },  
  "variables": \[  
    { "id": "partyB", "type": "string", "name": "Party B", "description": "Counterparty name", "validation": { "required": true, "minLength": 1 } },  
    { "id": "amount", "type": "number", "name": "USDC Amount", "description": "Payment amount in USDC", "validation": { "required": true, "min": 1 } },  
    { "id": "partyBAddress", "type": "address", "name": "Party B Address", "description": "Ethereum address", "validation": { "required": true, "pattern": "^0x\[a-fA-F0-9\]{40}$" } }  
  \],  
  "content": {  
    "type": "md",  
    "data": "\# Agreement

I, Jane Doe, agree to provide :variable{id='partyB'} with one hour of startup business advice. In exchange, :variable{id='partyB'} agrees to transfer :variable{id='amount'} USDC to my Ethereum address 0x1234567890abcdef1234567890abcdef12345678."  
  },  
  "execution": {  
    "type": "dfsm",  
    "data": {  
      "states": \["PENDING\_SIGNATURE", "SIGNED"\],  
      "inputs": {  
        "sigA": {  
          "id": "sigA",  
          "type": "eip712-signature",  
          "displayName": "Signature Party A",  
          "description": "EIP-712 signature from Party A",  
          "schema": "eip712://agreements.example.org/schemas/sigA",  
          "signer": "0x1234567890abcdef1234567890abcdef12345678"  
        },  
        "sigB": {  
          "id": "sigB",  
          "type": "eip712-signature",  
          "displayName": "Signature Party B",  
          "description": "EIP-712 signature from Party B",  
          "schema": "eip712://agreements.example.org/schemas/sigB"  
        }  
      },  
      "transitions": \[  
        { "from": "PENDING\_SIGNATURE", "to": "SIGNED", "conditions": \[ { "type": "isValid", "inputs": \["sigA", "sigB"\] } \] }  
      \]  
    }  
  }  
}
```

### E.2. Example MDAST content

```JSON
{  
  "type": "root",  
  "children": \[  
    { "type": "heading", "depth": 1, "children": \[ { "type": "text", "value": "Agreement" } \] },  
    { "type": "paragraph", "children": \[  
      { "type": "text", "value": "I, Jane Doe, agree to provide " },  
      { "type": "variable", "id": "partyB" },  
      { "type": "text", "value": " with one hour of startup business advice." }  
    \] }  
  \]  
}
```

### E.3. Verifiable Credential wrapper

```JSON
{  
  "@context": \[  
    "https://www.w3.org/2018/credentials/v1",  
    "https://agreements.example.org/contexts/agreement-v1.json"  
  \],  
  "type": \["VerifiableCredential", "AgreementCredential"\],  
  "issuer": "did:web:issuer.example.org",  
  "issuanceDate": "2025-10-15T12:45:00Z",  
  "credentialSubject": {  
    "id": "did:web:agreements.example.org:holders:partyB",  
    "agreement": {  
      "$ref": "https://agreements.example.org/examples/basic-consulting.template.json"  
    }  
  }  
}
```

---

## Annex F. (informative) Implementer’s Guide

This annex is informative. It provides recommended implementation practices, validation steps, and conformance testing guidance.

### F.1. Overview

This guide outlines the minimum checks a producer or consumer implementation SHOULD perform to ensure conformance with the Agreements Protocol schemas and normative clauses.

### F.2. Producer implementation checklist

1. **Schema validation** — Validate the full template JSON against the schema in Annex B before publication.

2. **Unique identifiers** — Ensure `metadata.id` and `metadata.templateId` are globally unique and follow DID patterns.

3. **Variable references** — Verify every variable used in Markdown or MDAST content exists in the `variables` array.

4. **Execution model integrity** — Confirm that every transition references valid states and that all states appear in at least one transition path.

5. **Versioning** — Increment `metadata.version` according to semantic versioning when schema or content changes.

6. **Digital signatures** — Use EIP‑712 or equivalent proof mechanisms consistent with the selected interoperability profile.

### F.3. Consumer implementation checklist

1. **Schema validation** — Validate incoming instances and templates using JSON Schema draft‑2020‑12.

2. **Integrity checks** — Compute a deterministic hash (e.g., SHA‑256 over canonical JSON) to detect tampering.

3. **State transitions** — Evaluate DFSM transitions deterministically; reject any execution trace with undefined states or missing inputs.

4. **Proof verification** — Verify signatures using declared algorithm and signer identifiers; ensure recovered identifiers match the expected DID or address.

5. **Credential validation** — When using VC packaging, perform full VC proof verification per W3C guidelines, including checking revocation and status endpoints.

6. **Replay protection** — Bind proofs to `metadata.id` and `createdAt` and reject replays in incompatible contexts.

### F.4. Conformance testing approach

Implementers MAY create automated test suites using JSON Schema validation and scenario tests. Recommended structure:

 * **Schema tests:** validate all examples in Annex E; verify negative tests with intentionally invalid data.  
 * **Execution tests:** simulate transitions for example DFSMs; confirm that invalid input sets are rejected.  
 * **Credential tests:** verify that VC wrappers pass JSON Schema validation and signature verification.  
 * **Profile tests:** ensure compliance with selected interoperability profiles (Annex D) using specific key types and DID methods.

### F.5. Reference tooling

The following open-source tools are recommended:

 * `ajv` (JavaScript) or `jsonschema` (Python) for JSON Schema validation.  
 * `ethers.js` or `web3.py` for EIP‑712 signature creation and verification.  
 * `vc-js` or `did-jwt-vc` for Verifiable Credential issuance and validation.

---

## Annex C. (informative) Change history (informative) Change history

 * IP‑001: DFSM to ASM (completed)  
 * IP‑002: MDAST Content Representation (in progress)  
 * IP‑003: Proofs and Execution Flow specifications (completed)  
 * IP‑004: Standardized Metadata, Flat Variables, Schemas, and Content Types (completed)

---

## Bibliography

 \[1\] RFC 2119: Key words for use in RFCs to Indicate Requirement Levels.  
 \[2\] W3C Verifiable Credentials Data Model 1.1, W3C Recommendation, 2022‑03‑03.  
 \[3\] CommonMark Specification v0.30.  
 \[4\] MDAST Specification v3.  
 \[5\] Unist Specification v3.  
 \[6\] EIP‑712: Typed Structured Data Hashing and Signing, final (2023‑07).  
 \[7\] JSON Schema Draft‑2020‑12.

