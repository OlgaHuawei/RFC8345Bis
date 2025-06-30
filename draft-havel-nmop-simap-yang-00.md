---
title: "A YANG Data Model for SIMAP"
abbrev: SIMAP YANG Model
docname: draft-havel-nmop-simap-yang-00
category: info

submissiontype: IETF 
number:
date:
consensus: true
v: 0
area: "Operations and Management"
workgroup: "Network Management Operations"
keyword:
- SIMAP
- Topology
- SIMAP requirements

author:
  -
    fullname: Olga Havel
    org: Huawei
    email: olga.havel@huawei.com

  -
    fullname: Nigel Davis
    org: Ciena
    email: ndavis@ciena.com


contributor:

normative:

informative:


--- abstract

This document defines a YANG data model for Service & Infrastructure Maps (SIMAP). It extends the RFC8345 
YANG modules to support all SIMAP requirements.
This document will only focus on modelling proposal for each of the requirements not supported by RFC8345. 
Any related terminology, concepts, use cases and requirements are defined outside of this draft and this draft will 
only refer to them and propose the implementation solutions.

--- middle

# Introduction

{{!I-D.ietf-nmop-simap-concept}} defines Service & Infrastructure Maps (SIMAP) as a data model that provides a view of the 
operator's networks and services, including how it is connected to other models/data (e.g., inventory, observability 
sources, and operational knowledge). It specifically provides an approach to model multi-layered topology
and an appropriate mechanism to navigate amongst layers and correlate between them.
This includes layers from physical topology to service topology.
This model is applicable to multiple domains (access, core, data center, etc.) and
technologies (Optical, IP, etc.).

{{!I-D.ietf-nmop-simap-concept}} defines the requirements for SIMAP, based on the Operator use cases. 
Some of the non-functional requirements should be supported by the NETCONF {{!RFC6241}} or RESTCONF {{!RFC8040}} 
protocols, by Network Configuration Access Control Model {{!RFC8431}} and other current drafts in NETCONF WG.
Some of the Operators' requirements are already supported by the {{!RFC8345}} ietf-network and 
ietf-network-topology YANG modules. Some of the Operators' requirements were identified the gaps in these 
2 modules and this document provides the solution how to implement these gaps.

# Terminology

This document makes use of all terms defined in {{!I-D.ietf-nmop-simap-concept}}.

# SIMAP Requirements Analysis

The requirements from {{!I-D.ietf-nmop-simap-concept}} were further analyzed and split into 3 categories: 

* generic requirements for all YANG modules,
* requirements already supported by {{!RFC8345}},
* requirements identified as {{!RFC8345}} gaps, they require extensions/changes to ietf-network and 
ietf-network-topology YANG modules.

This document will focus on modelling the RFC8345 gaps

## Generic requirements

The following requirements are generic interface requiremenst and do not impact SIMAP modelling:

* Architectural Requirements:

    * REQ-CONDITIONAL: Provide capability for conditional retrieval of parts of SIMAP.
    * REQ-SCALES: The SIMAP API must be scalable.
    * REQ-PERFORMANCE: The SIMAP API must be  performant.
    * REQ-SECURITY:	The conventional NACM control access rules {{!RFC8341}} should apply

## Requiremenst supported by RFC8345

Based on our initial analysis, the following SIMAP requirements are already supported by RFC8345 and do not require any 
modelling extensions/modifications to ietf-network and ietf-network-topology:

* Operator Requirements: 
    * REQ-BASIC-MODEL-SUPPORT: Basic model with network, node, link, and termination point entity types.
    * REQ-LAYERED-MODEL: Topology layers from physical layer up to service layer.
    * REQ-PASSIVE-TOPO: Topology includes passive topology.
    * REQ-COMMON-API: Common SIMAP models and APIs, for multi domain.
    * REQ-LIVE: Live network topology.
    * REQ-LAYER-NAVIGATE: Navigation inside the topology layer and between the topology layers
    * REQ-DATA-PLANE-FLOW: Provider data plane (Flow) needs to be correlatable to underlay and customer data plane to 
overlay topology
    * REQ-CONTROL-PLANE: Underlay control plane routing state needs to be correlatable to underlay L3 topology. 
Overlay control-plane routing state needs to be correlate-able to overlay L3 network topology.

* Architectural Requirements:
    * REQ-DISCOVERY: A network controller must perform the initial and on-demand discovery of a network 
    * REQ-SYNCH: The controller must perform the sync with the network.
    * REQ-USABILITY: The SIMAP API must be simple and easy to integrate with the client applications. This requirement 
is supported for the unidirectional and point2point networks.
	
* Design Requirements:
    * REQ-TOPO-ONLY: SIMAP should contain only topological information.

## Requirements not supported by RFC8345 (Gaps)

Based on the initial analysis, the following SIMAP requirements are not fully supported by the RFC8345 and require 
extensions or modifications:

* Operator Requirements:
    * REQ-BIDIR: SIMAP must provide a mechanism to model bidirectional links
    * REQ-MULTI-POINT: SIMAP must provide a mechanism to model multipoint links
    * REQ-MULTI-DOMAIN: SIMAP must provide a mechanism to model links between networks
    * REQ-SUBNETWORK: SIMAP must provide a mechanism to model network decomposition into sub-networks

    * REQ-PROG-OPEN-MODEL: Open and programmable SIMAP.
    * REQ-STD-API-BASED: Standard based SIMAP models and APIs, for multi-vendor support.
    * REQ-GRAPH-TRAVERSAL: Topology graph traversal
    * REQ-SNAPSHOT: Network snapshot topology.
    * REQ-POTENTIAL: Potential new network topology.
    * REQ-SEMANTIC: Network topology semantics
    * REQ-EXTENSIBLE: Extensible via metadata
    * REQ-PLUG: SIMAP must be pluggable
    * REQ-SHARED: Share nodes, links, and termination points between different networks.
    * REQ-SUPPORTING: SIMAP must provide a mechanism to model supporting relationships between different types of 
topological entities (e.g., a termination point is supported by the node)
    * REQ-STATUS: Links and nodes that are down must appear in the topology. 

* Design Requirements:
    * REQ-PROPERTIES: SIMAP entities should mainly contain properties used to identify topological entities at different 
layers, identify their roles, and topological relationships between them."
    * REQ-RELATIONSHIPS: SIMAP should contain all topological relationships inside each layer or between the layers 
(underlay/overlay)
    * REQ-TEMPO-HISTO: Must support geo-spatial, temporal, and historical data.  

* Architectural Requirements:
    * REQ-USABILITY: The SIMAP API must be simple and easy to integrate with the client applications.
This requirement means that any extensions/modifications must keep the original RFC8345 approach as simple as possible
and fully generic and technology and layer agnostic.

## Requirements to keep in mind when modelling gaps

The following requirements are already supported in RFC8345, but we must keep them in mind when proposing
implementation solutions for gaps.

## Requirements for further analysis

The following requirements need to be analyzed further to determine if they can be suppoorted by RFC8345:

* Operator Requirements:
    * REQ-VIEWPOINTS: Different viewpoints provide capability to have different views to different stakeholders.
    * REQ-TOPOLOGY-ABSTRACTION: Navigation across the abstraction levels inside a single network layer


# Modelling options for RFC8345 Gaps 

There are multiple options how the requirements not supported by RFC8345 can be modelled, the following are identified
candidate approaches:
* APPROACH-AUGMENT: via new SIMAP YANG module, augmenting RFC8345 YANG modules.
* APPROACH-BIS: via RFC8345 bis YANG modules.
* APPROACH-NEW: via new SIMAP model that does not augment RFC8345 YANG modules.
* APPROACH-COMBO: Different approach for different groups of requirements.

## APPROACH-AUGMENT
This candidate solution introduces the new ietf-simap-topology YANG module that defines augmentation for ietf-network 
and ietf-network-topology nodes to support all SIMAP requirements. As this is the simplest way to analyze the gaps and
review the modelling, the current version of this draft uses this approach. This does not mean that this is the proposed 
approach, it will be used for review of the propose modelling changes until the final approach has been agreed.

## APPROACH-BIS
This candidiate solution changes the original ietf-network and ietf-network-topology YANG modules and is fully backward 
compatible with RFC8345 for ietf-network and ietf-network-topology modules. This was the original favorite for the 
authors of {{!I-D.ietf-nmop-simap-concept}}, but it may be suitable only for a subset of the requirements. Therefore 
APPROACH-COMBO may be more suitable. 

## APPROACH-NEW
There are 2 options here:

* APPROACH-NEW-TREE: use similar approach to RFC8345 modules, but dont keep backward compatibility and 
optimize the modelling.

* APPROACH-NEW-GRAPH: use different approach (e.g. not a tree but graph approach), may not even be YANG.

### APPROACH-NEW-TREE

### APPROACH-NEW-GRAPH

## APPROACH-COMBO
The solution may have a subset of requirements implemented as APPROACH-BIS, subset of requirements implemented as 
APPROACH-AUGMENT and subset of requirements implemented as APPROACH-NEW (e.g links to external modules).

# Solution Proposal for the RFC8345 Gaps

## REQ-BIDIR: Bidirectional Links

Move any relevant text from 2 drafts (draft-davis-nmop-some-refinements-to-rfc8345
and draft-havel-nmop-digital-map) about the current RFC8345 solution for bidir and 
why this is not the right solution.

### Implementation Proposal

Add 
  identity link-direction {
    description "Base identity for the directionality of the link";
  }

  identity link-direction-uni {
    base "st:link-direction";
    description "Unidirectional link";
  }

  identity link-direction-bi {
    base "st:link-direction";
    description "Bidirectional link";
  }

## REQ-MULTI-POINT: Multipoint Links

Move any relevant text from 2 drafts (draft-davis-nmop-some-refinements-to-rfc8345
and draft-havel-nmop-digital-map) about the current RFC8345 solution for multipoint and 
why this is not the right solution.

Propose the implementation and approach.

## REQ-MULTI-DOMAIN: Multi-domain Links

Move any relevant text from draft draft-havel-nmop-digital-map about the current RFC8345 
solution for multidomain and why this is not the right solution.

Propose the implementation and approach.

## REQ-SUBNETWORK: Subnetworks and partitioning

Move any relevant text from draft draft-havel-nmop-digital-map about the current RFC8345 
solution for multidomain and why this is not the right solution.

Propose the implementation and approach.

## REQ-PROG-OPEN-MODEL: Open and programmable API
RFC8345 already supports the open and programmable API, but this requirements also mentions the what-if scenarios
and different snapshots that may have some modelling implications 
(e.g. the need to switch between different snapshots, relation to the real network that the snapshot is for, etc).

## REQ-STD-API-BASED: Standard based SIMAP models and APIs, for multi-vendor support
RFC8345 already supports the standard model and API for the SIMAP requirements supported by RFC8345. 
We also need a standard model for the SIMAP requirements identified as RFC8345 gaps.
This requirement also mentions that these APIs must also provide the capability to retrieve the 
links to external data/models.

## REQ-GRAPH-TRAVERSAL: Topology graph traversal
RFC8345 states that one of the reasons for modelling only unidirectional and point to point links 
is to allows the model to be very easily subjected to applications that make use of graph algorithms.

If we add support for bidirectional and multipoint links, this will make the graph traversal more difficult
according to RFC8345. But based on the discussions with some Operators, having links as nodes and 
not direct relations is not graph traversal friendly and having the capability to have the direct link relations 
between the termination points or nodes is needed.

Therefore, we propose to add read only linkedTo unidirectional relations for optimizing path graph traversal 
at single layer.

# Model Structure Details

The SIMAP data model is defined in the  
   "ietf-simap-topology" module.  Its structure is shown in Figure 1.  The
   notation syntax follows the syntax used in {{!RFC8340}}.

{: #ietf-simap-topology-tree}
~~~~
module: ietf-simap-topology

  augment /nw:networks/nw:network:
    +--rw name?                       string
    +--rw label*                      string
    +--rw description?                string
    +--ro extension?                  <anydata>
    +--rw snapshot-info?              identityref
    +--rw (snapshot-info)?
       +--:(historical)
          +--rw snapshot-timestamp?   yang:date-and-time
  augment /nw:networks/nw:network/nw:node:
    +--rw name?          string
    +--rw label*         string
    +--rw description?   string
    +--ro extension?     <anydata>
  augment /nw:networks/nw:network/nw:node/nt:termination-point:
    +--rw name?          string
    +--rw label*         string
    +--rw description?   string
    +--ro extension?     <anydata>
  augment /nw:networks/nw:network/nt:link:
    +--rw name?          string
    +--rw label*         string
    +--rw description?   string
    +--ro extension?     <anydata>
  augment /nw:networks/nw:network/nt:link:
    +--rw link-type?        identityref
    +--rw link-direction?   identityref
    +--rw tp* [network-ref node-ref tp-ref]
       +--rw tp-ref          -> 
            /nw:networks/network[nw:network-id=current()/
            ../network-ref]/
            node[nw:node-id=current()/../node-ref]/
            nt:termination-point/tp-id
       +--rw node-ref        -> 
            /nw:networks/network[nw:network-id=current()/
            ../network-ref]/node/node-id
       +--rw network-ref     -> 
            /nw:networks/network/network-id
       +--rw tp-role?        identityref
       +--rw tp-direction?   identityref

~~~~
{: #fig-ietf-simap-topology-tree title="The Structure of the SIMAP Data Model"}


# YANG Module



# Security Considerations

# IANA Considerations

This document has no actions for IANA.

--- back

# Acknowledgments
{:numbered="false"}