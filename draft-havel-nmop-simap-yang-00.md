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

The following requirements are generic interface requirements and do not impact SIMAP modelling:

* Architectural Requirements:

    * REQ-CONDITIONAL: Provide capability for conditional retrieval of parts of SIMAP. The NETCONF/RESTCONF and YANG
support conditional retrieval. In the case that more advanced queries are needed, alternative query interface may be
required.
    * REQ-SCALES: The SIMAP API must be scalable.
    * REQ-PERFORMANCE: The SIMAP API must be  performant.
    * REQ-SECURITY:	The conventional NACM control access rules {{!RFC8341}} should apply

## Requirements supported by RFC8345

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

* Design Requirements:
    * REQ-TOPO-ONLY: SIMAP should contain only topological information.

* Architectural Requirements:
    * REQ-DISCOVERY: A network controller must perform the initial and on-demand discovery of a network 
    * REQ-SYNCH: The controller must perform the sync with the network.
    * REQ-USABILITY: The SIMAP API must be simple and easy to integrate with the client applications. This requirement 
is supported for the unidirectional and point2point networks.
	


We must keep these requirements in mind when proposing implementation solutions for gaps, as they are 
applicable to how we model the extensions / changes.

## Requirements not supported by RFC8345 (Gaps)

Based on the initial analysis, the following SIMAP requirements are not fully supported by the RFC8345 and require 
extensions or modifications:

* Operator Requirements:
    * REQ-PROG-OPEN-MODEL: Open and programmable SIMAP.Gap: what-if and snapshots part.
    * REQ-STD-API-BASED: Standard based SIMAP models and APIs, for multi-vendor support. Gap: links are entities, adding linkedTo relations would help
    * REQ-SNAPSHOT: Network snapshot topology.
    * REQ-POTENTIAL: Potential new network topology.
    * REQ-SEMANTIC: Network topology semantics. Gap: some semantic missing.
    * REQ-EXTENSIBLE: Extensible via metadata
    * REQ-PLUG: SIMAP must be pluggable
    * REQ-BIDIR: SIMAP must provide a mechanism to model bidirectional links. Gap: complex
    * REQ-MULTI-POINT: SIMAP must provide a mechanism to model multipoint links. Gap: complex
    * REQ-MULTI-DOMAIN: SIMAP must provide a mechanism to model links between networks
    * REQ-SUBNETWORK: SIMAP must provide a mechanism to model network decomposition into sub-networks
    * REQ-SUPPORTING: SIMAP must provide a mechanism to model supporting relationships between different types of 
topological entities (e.g., a termination point is supported by the node). Gap: between different types
    * REQ-STATUS: Links and nodes that are down must appear in the topology. Gap: optionally if status is in SIMAP, we need to model it

* Design Requirements:
    * REQ-TOPO-ONLY: SIMAP should contain only topological information. 
    * REQ-PROPERTIES: SIMAP entities should mainly contain properties used to identify topological entities at different 
layers, identify their roles, and topological relationships between them.
    * REQ-RELATIONSHIPS: SIMAP should contain all topological relationships inside each layer or between the layers 
(underlay/overlay)
    * REQ-TEMPO-HISTO: Must support geo-spatial, temporal, and historical data.


## Requirements to keep in mind when modelling gaps

Qny extensions/modifications must keep the original RFC8345 approach as simple as possible
and fully generic and technology and layer agnostic. The following requirements are already supported in RFC8345, 
but we must keep them in mind when proposing implementation solutions for gaps, as they are applicable to how we 
model the extensions / changes:

* Operator Requirements: 
    * REQ-BASIC-MODEL-SUPPORT: Basic model with network, node, link, and termination point entity types.
    * REQ-LAYERED-MODEL: Topology layers from physical layer up to service layer.
    * REQ-COMMON-API: Common SIMAP models and APIs, for multi domain.
    * REQ-LIVE: Live network topology.
    * REQ-LAYER-NAVIGATE: Navigation inside the topology layer and between the topology layers

* Design Requirements:
    * REQ-TOPO-ONLY: SIMAP should contain only topological information.

* Architectural Requirements:
    * REQ-USABILITY: The SIMAP API must be simple and easy to integrate with the client applications. This requirement 
is supported for the unidirectional and point2point networks.
	

## Requirements for further analysis

The following requirements need to be analyzed further to determine if they can be suppoorted by RFC8345:

* Operator Requirements:
    * REQ-VIEWPOINTS: Different viewpoints provide capability to have different views to different stakeholders.
    * REQ-TOPOLOGY-ABSTRACTION: Navigation across the abstraction levels inside a single network layer
    * REQ-SHARED: Share nodes, links, and termination points between different networks.

The following requirements have to be analyzed further to capture all generic networking semantics missing from
RFC8345 and from additional requirements defined as gaps.
* Operator Requirements:
    * REQ-SEMANTIC: Network topology semantics. 

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

## REQ-PROG-OPEN-MODEL: Open and Programmable

### Analysis
RFC8345 already supports the open and programmable API, but this requirements also mentions the what-if scenarios
and different snapshots that may have some modelling implications 
(e.g. the need to switch between different snapshots, relation to the real network that the snapshot is for, etc).

### Implementation Proposal

## REQ-STD-API-BASED: Standard based

### Analysis
RFC8345 already supports the standard model and API for the SIMAP requirements supported by RFC8345. 
We also need a standard model for the SIMAP requirements identified as RFC8345 gaps.
This requirement also mentions that these APIs must also provide the capability to retrieve the 
links to external data/models.

### Implementation Proposal
The solution for the external links is proposed in a separate draft {{!I-D.vivek-simap-external-relationship}}.

## REQ-GRAPH-TRAVERSAL: Graph Traversal

### Analysis
RFC8345 states that one of the reasons for modelling only unidirectional and point to point links 
is to allows the model to be very easily subjected to applications that make use of graph algorithms.

If we add support for bidirectional and multipoint links, this will make the graph traversal more difficult
according to RFC8345. But based on the discussions with some Operators, having links as nodes and 
not direct relations is not graph traversal friendly in any case and having the capability to have the direct link 
relations between the termination points or nodes is needed.

### Implementation Proposal

Add read only linked-termination-point and linked-node for optimizing path graph traversal at single layer.

  augment "/nw:networks/nw:network/nw:node/nt:termination-point" {
    description
      "Adding optional read only link relations";

    list linked-termination-point {
      config false;
      key "network-ref node-ref tp-ref";
      description
            "This list identifies any linked termination points, added
             optionally for read only for optimized path graph traversal";

      uses nt:tp-ref;
  }

  augment "/nw:networks/nw:network/nw:node" {
    description
      "Adding optional read only link relations";

    list linked-node {
      config false;
      key "network-ref node-ref";
      description
            "This list identifies any linked termination points, added
             optionally for read only for optimized path graph traversal";

      uses nw:node-ref;
  }

## REQ-SNAPSHOT: Different snapshots

### Analysis
SIMAP must enable retrieval of multi-layered topology of different historical snapshots, where a 
snapshot is the view of the network at any given point in time.
This requires the implementation proposal to take into account the following:

* how to retrieve historical snapshot for the network using time stamp (recorder time and/or validity time)
* how to retrieve historical snapshots for the current topology
* how to model and retrieve historical topology: either full topology or only changes from another snapshot
* how to retrieve the external models via links in the historical context 
* is this SIMAP requirement or generic requirement for any YANG retrieval via NETCONF/RESTCONF. The {{!RFC8342}} 
defines different datastores: configuration datastores (startup, candidate, running, intended) 
and operational datastore. It does not support the historical datastores. 

### Implementation Proposal
The following are the identified implementation candidates:
* this is generic requirement, not just for SIMAP, therefore implementation should not be implemented by SIMAP but 
by the NETCONF/RESTCONF for historical system state + conf

* implemented via some historical files retrieval of the historical files for specified snapshot-id, 
network-id and timestamp, and the file itself can have the same structure as a member of 
the list network:

{: #REQ-SNAPSHOT-YANG-1}
~~~~
  container networks-history {
    config false;
    list network-history {
      key "live-network-ref timestamp";
      description
        "Historical network. Snapshot is generated for each historical instance, network-ref
        is the reference for the live network";
      leaf live-network-ref {
         type leafref {
           path "/nw:networks/nw:network/nw:network-id";
         require-instance false;
      }
      leaf timestamp {
        type yang:date-and-time;
      }
      leaf file-id {
        type inet:uri;
      }
    }
  }

~~~~
{: #REQ-SNAPSHOT-YANG-1 title="The proposal 1 for historical snapshots"}

* implemented as a separate container in the ietf-network, for read only. Instead of referencing the file,
the whole yang structure for the network from the list-network is duplicates, except that all supporting
references must also point to snapshot id as well

{: #REQ-SNAPSHOT-YANG-2}
~~~~
container networks-history {
  config false;
  list network-history-snapshot {
    key "network-ref snapshot-id"
    uses nt:network-ref;
    leaf snapshot-id {
      type inet:uri;
    }
    leaf timestamp {
      type yang:date-and-time;
    }
    container network-types {
    }
    list supporting-network {
        key "network-ref snapshot-id"
        leaf network-ref {}
        leaf snapshot-id {}
    }
    list node {
        leaf node-id {..}
        list supporting-node {
          key "network-ref snapshot-id node-ref";
          ...
        }
        list termination-point {
          leaf tp-id {}
          list supporting-termination-point {
            key "network-ref snapshot-id node-ref tp-ref";
            ...
          }
        }
    }
    list link {
        leaf link-id {..}
        list supporting-link {
          key "network-ref snapshot-id link-ref";
          ...
        }
    }
}
~~~~
{: #REQ-SNAPSHOT-YANG-2 title="The proposal 2 for historical snapshots"}

The option with file reference is currently proposed in the YANG module.

## REQ-POTENTIAL: Potential new network topology

### Analysis
SIMAP must enable both retrieval and write access to the potential new network.
A potential new network is the view at a given point with modifications from the snapshot.
This view may contain either the full topology or just differences from the snapshot.

This requires the implementation proposal to take into account the following:
* SIMAP write is used for either what-if or intended scenarios, but we have to ensure that we can create multiple 
snapshots
* how to retrieve different what-if network instances
* how to connect what-if snapshot with the historical snapshot, as the current topology may change and
what-if snapshot was created based on the historical context
* how to connect to the current network instance
* how to model and retrieve potential network: either full topology or only changes from the snapshot
* how to retrieve the external models via links in the what-if context 
* is this SIMAP requirement or generic requirement for any YANG retrieval via NETCONF/RESTCONF.
The {{!RFC8342}} defines different datastores: configuration datastores (startup, candidate, running, intended) 
and operational datastore. It can potentially be used for one candidate, but it does not support multiple candidates
with links to different running.

### Implementation Proposal
The following is the initial proposal:
* use list network for potential as well 
* network-id is generated for each potential candidate
* additional info needed: type: potential, timestamp, relation to the live network network-id
* relation to historical snapshot may be determined based on the live network-id and timestamp

{: #REQ-POTENTIAL-YANG-1}
~~~~

  identity network-category {
    description "Base identity for the network category";
  }

  identity network-category-potential {
    base "st:network-category";
    description "Potential topology. There may be optionally one or multiple instances of the potential network,
                 related to the current network. Timestamp determines what historical snapshot has been used when
                 creating the potential network instance";
  }

  augment "/nw:networks/nw:network" {

    description
      "Adding optional capability for modelling of potential and intended network";

    leaf network-category {
      type identityref {
        base st:network-category;
      }
      description
        "The network category: live, potential, intended. Default is live.";
    }
    leaf live-network-ref {
      description
        "Required for potential and intended only to connect to the live network instance";
       type leafref {
         path "/nw:networks/nw:network/nw:network-id";
       require-instance false;
    }
    leaf timestamp {
      description
        "Required for potential only to connect to the historical snapshot
      type yang:date-and-time;
    }
  }

~~~~
{: #REQ-POTENTIAL-YANG title="The proposal for potential snapshots"}

This proposal has an issue with backward compatibility as the read would return potential 
instances as well. Alternative is to have a separate list networks-potential.

## REQ-INTENDED: Intended topology

### Analysis
SIMAP must enable both retrieval and write access to the intended network topology that cannot be
discovered from the real network (for example target L2 Topology, L3 Topology, passive topology that
cannot be discovered, etc).

This requires the implementation proposal to take into account the following:
* how to connect intended to the current network instance

### Implementation Proposal
The following is the initial proposal:
* use list network for potential as well 
* network-id is generated for each potential candidate
* additional info needed: type: intended, timestamp, relation to the live network network-id
* relation to historical snapshot may be determined based on the live network-id and timestamp

{: #REQ-INTENDED-YANG-1}
~~~~

  identity network-category {
    description "Base identity for the network category";
  }

  identity network-category-intended {
    base "st:network-category";
    description "Intended topology, there is optionally 1 instance of intended network related to the live network
                 instance";
  }

  augment "/nw:networks/nw:network" {

    description
      "Adding optional capability for modelling of potential and intended network";

    leaf network-category {
      type identityref {
        base st:network-category;
      }
      description
        "The network category: live, potential, intended. Default is live.";
    }
    leaf live-network-ref {
      description
        "Required for potential and intended only to connect to the live network instance";
       type leafref {
         path "/nw:networks/nw:network/nw:network-id";
       require-instance false;
    }
    leaf timestamp {
      description
        "Required for potential only to connect to the historical snapshot
      type yang:date-and-time;
    }
  }

~~~~
{: #REQ-INTENDED-YANG title="The proposal for intended snapshots"}

This proposal has an issue with backward compatibility as the read would return intended 
instances as well. Alternative is to have a separate list networks-intended.

## REQ-BIDIR: Bidirectional Links

### Analysis

Move any relevant text from 2 drafts (draft-davis-nmop-some-refinements-to-rfc8345
and draft-havel-nmop-digital-map) about the current RFC8345 solution for bidir and 
why this is not the right solution.

### Implementation Proposal

{: #REQ-BIDIR-YANG}
~~~~
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

  identity tp-direction {
    description "Base identity for the directionality of the tp";
  }

  identity tp-direction-symmetric {
    base "st:tp-direction";
    description "TP in the bidirectional link";
  }

  identity tp-direction-source {
    base "st:tp-direction";
    description "TP is the source tp in the link";
  }

  identity tp-direction-destination {
    base "st:tp-direction";
    description "TP is the destination tp in the link";
  }

  augment "/nw:networks/nw:network/nt:link" {

    :
    :
    leaf link-direction {
      type identityref {
        base st:link-direction;
      }
      description
        "The direction of the link: unidirectional (link-direction-uni) or bidirectional (link-direction-bi).
         It can also be any other custom identity defined with base link-direction.
         It is optional, so the model supports the solution without the link direction information,
         either if not known or for backward compatible case.";
    }
    list tp {
      key "network-ref node-ref tp-ref";
      description "List of termination points in the link";
      uses nt:tp-ref;
        :
        :
      leaf tp-direction {
        type identityref {
          base st:tp-direction;
        }
        description
          "The direction of the point in the link";
      }
  }

~~~~
{: #REQ-BIDIR-YANG title="The proposal for bidirectional links"}

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