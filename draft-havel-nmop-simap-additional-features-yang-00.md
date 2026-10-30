---
title: "A YANG Data Model for SIMAP Additonal Features"
abbrev: SIMAP Additonal Fetaures YANG Model
docname: draft-havel-nmop-simap-additional-features-yang-00
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
- Multi-layered Topology
- RFC8345 Gaps

author:
  -
    fullname: Olga Havel
    org: Huawei
    email: olga.havel@huawei.com

  -
    fullname: Nigel Davis
    org: Ciena
    email: ndavis@ciena.com

  -
    fullname: Oscar Gonzalez de Dios
    org: Telefonica
    email: oscar.gonzalezdedios@telefonica.com

  -
    fullname: Thomas Graf
    org: Swisscom
    email: thomas.graf@swisscom.com

contributor:

normative:

informative:


--- abstract

This document defines a YANG data model for Service & Infrastructure 
Maps (SIMAP) Additional Fetaures, including optional graph 
navigation, lifecycle and external-link capabilities intended to be 
reusable with RFC 8345 topology models, including implementations using
RFC 8795. 

--- middle

# Introduction

{{!I-D.ietf-nmop-simap-concept}} defines Service & Infrastructure 
Maps (SIMAP) as a data model that provides a view of the operator's 
networks and services, including how it is connected to other 
models (e.g., inventory), and data (e.g., observability data, 
operational knowledge). It specifically provides an approach to model 
multi-layered topology and an appropriate mechanism to navigate 
amongst layers and correlate between them. This includes layers from 
physical topology to service topology. This model is applicable to 
multiple domains (access, core, data center, etc.) and
technologies (Optical, IP, etc.).

{{!I-D.ietf-nmop-simap-concept}} defines the requirements for SIMAP, 
based on the Operator use cases. We split the requirements into the 
following features:

* Core SIMAP topology
* SIMAP graph navigation
* SIMAP lifecycle
* SIMAP external links
* SIMAP extensibility / metadata
* API / server / operational

This document will address the SIMAP graph navigation, SIMAP 
lifecycle, SIMAP external links and SIMAP extensibility / metadata 
requirements. These requirements will be modelled in 
this document in the YANG module ietf-simap-features.

The Core SIMAP Topology requirements will be addressed in the separate 
draft.

# Terminology

Terminology for SIMAP, SIMAP modelling, SIMAP data, topology, 
topology layer, multi-layered topology is defined in {{!RFC8345}}


## Requirements not supported by RFC8345 (Gaps) {#gap-requirements}

The core SIMAP topology requirements have been addressed in another 
document. The following are other additional features SIMAP requirements 
are not fully supported by the RFC8345 and require extensions or 
modifications:

* Operator Requirements:
    * REQ-PROG-OPEN-MODEL: Open and programmable SIMAP.Gap: what-if 
and snapshots part. Analysis and solution presented in 
{{REQ-PROG-OPEN-MODEL}}.
    * REQ-STD-API-BASED: Standard based SIMAP models and APIs, for  
multi-vendor support. Gap: link to external models. Analysis and 
solution presented in {{REQ-STD-API-BASED}}.
    * REQ-GRAPH-TRAVERSAL: Graph Traversal. Gap: links are entities, 
adding linkedTo relations would help. Analysis and solution 
presented in {{REQ-GRAPH-TRAVERSAL}}.
    * REQ-SNAPSHOT: Network snapshot topology. Analysis and solution 
presented in {{REQ-SNAPSHOT}}.
    * REQ-POTENTIAL: Potential new network topology. Analysis and 
solution presented in {{REQ-POTENTIAL}}.
    * REQ-INTENDED: Intended topology. Analysis and solution 
presented in {{REQ-INTENDED}}.
    * REQ-EXTENSIBLE: Extensible via metadata. Analysis and solution 
presented in {{REQ-EXTENSIBLE}}.
    * REQ-PLUGG: SIMAP must be pluggable. Analysis and solution 
presented in {{REQ-PLUGG}}.
    * REQ-STATUS: Links and nodes that are down must appear in the 
topology. Gap: optionally if status is in SIMAP, we need to model it.  
Analysis and solution presented in {{REQ-STATUS}}.

* Design Requirements:
    * REQ-TEMPO-HISTORY: Must support geo-spatial, temporal, and 
historical data. Analysis and solution presented in 
{{REQ-TEMPO}}.

### Features for RFC8345 Gaps {#gap-requirements-features}

Based on the initial analysis, we identified that the requirements can 
be grouped into the following features

* SIMAP Navigation. These are the requirements for navigation of SIMAP
	* REQ-GRAPH-TRAVERSAL

* SIMAP Lifecycle. These are the requirements for modelling the 
lifecycle of the topology, including intended topology, potential 
topology, historical snapshots and relations between them.
	* REQ-PROG-OPEN-MODEL
	* REQ-SNAPSHOT
	* REQ-POTENTIAL
	* REQ-INTENDED
	* REQ-STATUS
	* REQ-TEMPO-HISTO

* SIMAP External Link. These are the requirements for linking the 
external models and data
	* REQ-STD-API-BASED
	* REQ-PLUGG

* SIMAP Extensibility / metadata. These are the requirements for 
extensing the meta data
    * REQ-EXTENSIBE

Please note that the SIMAP core feature may be further split
during the further analysis and reviews of the SIMAP module.
Analysis and solution presented in {{SIMAP-FEATURES-SOLUTION}}.


# API Scope {#API-SCOPE}
It has been identified from the use cases that there is need for the 
following:

* read access to the live topology (REQ-LIVE)
* read access to historical topology (REQ-SNAPSHOT)
* read and write access for potential topology (REQ-POTENTIAL)
* read and write access for intended topology (REQ-INTENDED)

Traditionally, separate API endpoints are used for accessing the live 
network and potential network views. If we follow this approach, the 
following consequences apply:

* The same topology model can be reused across live, potential, and 
intended network representations.
* Distinct API endpoints are maintained for each of the three categories.
* Access control (e.g., read-only for live and read/write for 
potential networks) can be managed externally via NACM access control 
rules as defined in  {{!RFC8341}}, eliminating the need to use config 
true/false within the model itself.
* The responsibility for tracking relationships between live, 
potential, intended networks, and historical snapshots is delegated 
to applications. These applications must orchestrate across multiple 
APIs to compute differences between snapshots.

To support more advanced solutions and enable SIMAP to be positioned 
across various interfaces, as discussed in (Chapter 5 
of  {{!I-D.ietf-nmop-simap-concept}}, the current draft proposes the 
following enhancements:

* Define a default behavior that allows separation between live, 
potential, and intended topologies without embedding this semantic 
distinction in the model itself. Instead, semantics are determined by 
the selected API endpoint, and the model remains agnostic to the 
category of topology.
* Optionally enable a unified API that introduces model-level 
semantics to distinguish between live, potential, and intended 
topologies. This unified model also captures relationships among live,
potential, historical, and intended instances. Such an approach 
supports advanced use cases that require the model to explicitly 
represent these semantics.

# Solution Proposal for the RFC8345 Gaps

## SIMAP Features {#SIMAP-FEATURES-SOLUTION}

We are introducing a set of SIMAP features that will be used to group 
requirements and optionally include them in the SIMAP module if the 
feature is implemented. Definitions will be tagged with the feature 
name and are only valid on a SIMAP server that supports that feature.

The following is the initial set of SIMAP features:

{: #SIMAP-FEATURES}
~~~~
module ietf-simap-features {
    /* … other statements … */


	feature simap-navigation {
		description
		    "This feature indicates that the SIMAP server supports 
             those SIMAP requirements not supported by RFC8345, but 
             related to connecting SIMAP navigation";
	}

	feature simap-lifecycle {
		description
		    "This feature indicates that the SIMAP server supports 
             those SIMAP requirements not supported by RFC8345, but 
             related to the overall SIMAP lifecycle, including intent,
             potential (for what-if) and historical snapshots";
	}

	feature simap-external {
		description
		    "This feature indicates that the SIMAP server supports 
             those SIMAP requirements not supported by RFC8345, but 
             related to connecting SIMAP to external models and data";
	}

	feature simap-extensibility {
		description
		    "This feature indicates that the SIMAP server supports 
             those SIMAP requirements not supported by RFC8345, but 
             related to extensing the model and adding meta data";
	}

    /* … other statements … */
}
~~~~
{: #SIMAP-FEATURES title="The SIMAP Features"}


## Feature simap-extensibility

The solution is proposed for the following requirements:
    * REQ-EXTENSIBLE

The changes to the RFC8345 model for these requirements would be 
specified inside: if-feature simap-extensibility.


### REQ-EXTENSIBLE: Extensible via metadata {#REQ-EXTENSIBLE}

#### Analysis
SIMAP must be extensible with metadata. This metadata can be added 
via  augmentation, but the model could also allow the approach of 
adding the metadata via the core topology model. This approach can be 
used for adding any semantics not explicitely implemented in the model.

#### Implementation Proposal

Add the following to network, node, termination-point and link, and 
to  supporting relations:

{: #REQ-EXTENSIBLE-YANG-1}
~~~~
  grouping simap-common {
    description "A reusable set of optional extensions for network,
                 node, termination point and link";

    anydata extension {
	  if-feature simap-extensibility;
      config false;
      description
        "The extension point for any other meta info or data or any
        unknown extensions. Proposed solution for SIMAP requirement
        REQ-EXTENSIBLE. Any additional info that is topologically
        significant can also be added this way for requirement
        REQ-SEMANTIC or REQ-PROPERTIES.";
    }
  }
~~~~
{: #REQ-EXTENSIBLE-YANG title="The proposal for extensions for meta 
data"}


## Feature simap-navigation

The solution is proposed for the following requirements:
    * REQ-GRAPH-TRAVERSAL

The changes to the RFC8345 model for these requirements would be 
specified inside: if-feature simap-navigation.

### REQ-GRAPH-TRAVERSAL: Graph Traversal {#REQ-GRAPH-TRAVERSAL}

#### Analysis
RFC8345 states that one of the reasons for modelling only 
unidirectional and point to point links is to allows the model to be 
very easily subjected to applications that make use of graph algorithms.

If we add support for bidirectional and multipoint links, this will 
make the graph traversal more difficult according to RFC8345. But 
based on the discussions with some Operators, having links as nodes and 
not direct relations is not graph traversal friendly for paths in any 
case and having the capability to have the direct link relations 
between the termination points or nodes is needed.

The declarative graph queries will require the graph query language  
like Cypher or SPARQL. The current IETF modelling and protocols can 
only provide the limited graph navigation.

#### Implementation Proposal

Add read only linked-termination-point and linked-node for  
optimizing path graph traversal at single layer, keep them optional 
for backward compatibility.

{: #REQ-GRAPH-TRAVERSAL-YANG}
~~~~
  augment "/nw:networks/nw:network/nw:node/nt:termination-point" {
    description
      "Adding optional read only link relations between connected 
termination points for optimized graph traversal for paths";

    list linked-termination-point {
	  if-feature simap-navigation;
      config false;
      key "network-ref node-ref tp-ref";
      description
            "This list identifies any linked termination points, added
             optionally for read only for optimized path graph 
             traversal";

      uses nt:tp-ref;
  }

  augment "/nw:networks/nw:network/nw:node" {
    description
      "Adding optional read only link relations between connected 
nodes for optimized graph traversal for paths";

    list linked-node {
	  if-feature simap-navigation;
      config false;
      key "network-ref node-ref";
      description
            "This list identifies any linked nodes, added
             optionally for read only for optimized path graph 
             traversal";

      uses nw:node-ref;
  }
~~~~
{: #REQ-GRAPH-TRAVERSAL-YANG title="The proposal for optimized  path 
graph traversal"}

## Feature simap-lifecycle

The solution is proposed for the following requirements:
	* REQ-PROG-OPEN-MODEL
	* REQ-SNAPSHOT
	* REQ-POTENTIAL
	* REQ-INTENDED
	* REQ-STATUS
	* REQ-TEMPO-HISTO

The changes to the RFC8345 model for these requirements would be 
specified inside: if-feature simap-lifecycle

### REQ-PROG-OPEN-MODEL: Open and Programmable {#REQ-PROG-OPEN-MODEL}

#### Analysis
RFC8345 already supports the open and programmable API, but this 
requirements also mentions the what-if scenarios
and different snapshots that may have some modelling implications 
(e.g. the need to switch between different snapshots, relation to the 
real network that the snapshot is for).

#### Implementation Proposal
The implementation for the model changes for this requirement will be 
covered by the implementation proposals for:

* REQ-POTENTIAL in {{REQ-POTENTIAL}} for write access to the 
potential new topology.
* REQ-INTENDED in {{REQ-INTENDED}} for write access for 
intended topologies.

### REQ-SNAPSHOT: Different snapshots {#REQ-SNAPSHOT}

#### Analysis
SIMAP must enable retrieval of multi-layered topology of different 
historical snapshots, where a snapshot is the view of the network at 
any given point in time. This requires the implementation proposal to 
take into account the following:

* how to retrieve historical snapshot for the network using time 
stamp (recorder time and/or validity time)
* how to retrieve historical snapshots for the current topology
* how to model and retrieve historical topology: either full topology 
or only changes from another snapshot
* how to retrieve the external models via links in the historical  
context 
* is this SIMAP requirement or generic requirement for any YANG  
retrieval via NETCONF/RESTCONF. The {{!RFC8342}} 
defines different datastores: configuration datastores 
(startup, candidate, running, intended) and operational datastore. It 
does not support the historical datastores. 

#### Implementation Proposal
The following are the identified implementation candidates:

* Candidate 1 - Generic Solution:
    * this is generic requirement, not just for SIMAP, therefore 
implementation should not be implemented by SIMAP but by the 
NETCONF/RESTCONF (in NMDA?) for historical system state + conf. For 
conf changes via API there is history of transactions, but nothing we 
are aware of for historical of any data model.

* Candidate 2- SIMAP Specific Solution via historical files (currently 
proposed in the YANG module):
    * implemented via some historical files retrieval of the 
historical files for specified snapshot-id, network-id and timestamp, 
and the file itself can have the same structure as a member of 
the list network:

{: #REQ-SNAPSHOT-YANG-1}
~~~~
  container networks-history {
    if-feature simap-lifecycle;
    config false;
    list network-history {
      key "live-network-ref timestamp";
      description
        "Historical network. Snapshot is generated for each historical 
         instance, network-ref is the reference for the live network";
      leaf live-network-ref {
         type leafref {
           path "/nw:networks/nw:network/nw:network-id";
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
{: #REQ-SNAPSHOT-YANG-1 title="The candidate 2 for historical snapshots"}

* Candidate 3 - SIMAP Specific Solution via separate containers" 
     * implemented via separate container, for read only. Instead of 
referencing the file, the whole yang structure for the network from 
the list-network is duplicated, except that all supporting references 
must also point to snapshot id as well. This implementation is not 
proposed in the yang file in this draft version.

{: #REQ-SNAPSHOT-YANG-2}
~~~~
container networks-history {
  if-feature simap-lifecycle;
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
{: #REQ-SNAPSHOT-YANG-2 title="The candidate 3 for historical snapshots"}

The candidate 2 with file reference is currently implemented in the YANG 
module, but our preference would be to use a generic mechanism for 
historical retrieval, as this is not SIMAP only requirement but also 
applicable to inventory and other modules.

### REQ-POTENTIAL: Potential new network topology {#REQ-POTENTIAL}

#### Analysis
SIMAP must enable both retrieval and write access to the potential 
new network. A potential new network is the view at a given point 
with modifications from the snapshot. This view may contain either 
the full topology or just differences from the snapshot. Running a 
"what-if" analysis requires the ability to take snapshots and to 
switch easily between them.

This requires the implementation proposal to take into account the 
following:

* SIMAP write is used for either what-if or intended scenarios, but  
we have to ensure that we can create multiple snapshots for what-if 
scenario
* how to retrieve different what-if network instances
* how to connect what-if snapshot with the historical snapshot, as 
the current topology may change and what-if snapshot was created 
based on the historical context
* how to connect to the current network instance
* how to model and retrieve potential network: either full topology 
or only changes from the snapshot
* how to retrieve the external models via links in the what-if context 
* is this SIMAP requirement or generic requirement for any YANG  
retrieval via NETCONF/RESTCONF. The {{!RFC8342}} defines different 
datastores: configuration datastores (startup, candidate, running,  
intended) and operational datastore. It can potentially be used for 
one candidate, but it does not support multiple candidates
with links to different running. We have private candidates that can 
be potentially used.

Please refer to {{API-SCOPE}} for analysis of multiple  
endpoints (sematics determined by the selection of the endpoint)
versus single endpoint (semantics in the model).

#### Implementation Proposal
The following is the initial proposal:

* provide a solution that would allow the live versus potential to 
be modeled via different endpoints(please see {{API-SCOPE}})

* optionally, provide the solution for adding the semantics to the model:
  * use list network for potential as well 
  * network-id is generated for each potential candidate
  * additional info needed: category (potential), timestamp,  
relation to the live network network-id
  * relation to historical snapshot may be determined based on the 
live  network-id and timestamp

Preferably, the generic solution with private candidates would be used, 
but for now we have the following modelled.

{: #REQ-POTENTIAL-YANG-1}
~~~~

  identity network-category {
    description "Base identity for the network category";
  }

  identity network-category-none {
    description "This is used when endpoint selection identifies if 
                 live, potential or intended. This is the default";
  }

  identity network-category-potential {
    base "st:network-category";
    description "Potential topology. There may be optionally one or 
                 multiple instances of the potential network, related 
                 to the current network. Timestamp determines what 
                 historical snapshot has been used when
                 creating the potential network instance";
  }

  augment "/nw:networks/nw:network" {

    if-feature simap-lifecycle;
    description
      "Adding optional capability for modelling of potential 
       and intended network";

    leaf network-category {
      type identityref {
        base st:network-category;
      }
      description
        "The network category: none, live, potential, intended. 
         Default is none.";
    }
    leaf live-network-ref {
      description
        "Required for potential and intended only to connect 
         to the live network instance";
       type leafref {
         path "/nw:networks/nw:network/nw:network-id";
       require-instance false;
    }
    leaf timestamp {
      description
        "Required for potential only to connect to the 
         historical snapshot";
      type yang:date-and-time;
    }
  }

~~~~
{: #REQ-POTENTIAL-YANG title="The proposal for potential snapshots"}

Check if this proposal has an issue with backward compatibility,
as the read would return potential instances as well. Alternative is  
to have a separate list networks-potential.

### REQ-INTENDED: Intended topology {#REQ-INTENDED}

#### Analysis
SIMAP must enable both retrieval and write access to the intended  
network topology that cannot be discovered from the real network (for 
example target L2 Topology, L3 Topology, passive topology that cannot 
be discovered).

This requires the implementation proposal to take into account the  
following:

* how to connect intended to the current network instance

Please refer to {{API-SCOPE}} for analysis of multiple endpoints 
(sematics determined by the selection of the endpoint) versus single 
endpoint (semantics in the model).

Preferably we would use NMDA and one of the datastores if possible, 
needs further analysis.

#### Implementation Proposal
The following is the initial proposal:

* provide a solution that would allow the live versus intended to be  
modeled via different endpoint (please see {{API-SCOPE}})
* optionally, provide the solution for adding the semantics to the model:
  * use list network for intended as well 
  * network-id is generated for intended network, different 
network-id from the live network
  * additional info needed: type: intended, relation to the live  
network network-id

Preferably, the generic solution with standard datastored would be 
used if possible, but for now we have the following modelled.

{: #REQ-INTENDED-YANG-1}
~~~~

  identity network-category-none {
    description "This is used when endpoint selection identifies if 
                 live, potential or intended. This is the default";
  }

  identity network-category-intended {
    base "st:network-category";
    description "Intended topology, there is optionally 1 instance of
                 intended network related to the live network instance";
  }

  augment "/nw:networks/nw:network" {
    if-feature simap-lifecycle;

    description
      "Adding optional capability for modelling of potential and
      intended network";

    leaf network-category {
      type identityref {
        base st:network-category;
      }
      description
        "The network category: live, potential, intended.
        Default is live.";
    }
    leaf live-network-ref {
      description
        "Required for potential and intended only to connect to the
        live network instance";
       type leafref {
         path "/nw:networks/nw:network/nw:network-id";
       require-instance false;
       }
    }
    :
    :
  }

~~~~
{: #REQ-INTENDED-YANG title="The proposal for intended snapshots"}

Check if this proposal has an issue with backward compatibility,
as the read would return potential instances as well. Alternative is  
to have a separate list networks-intended.


### REQ-STATUS: Links and nodes down in topology {#REQ-STATUS}

#### Analysis
Links and nodes that are down must appear in the topology. The status 
of the nodes and links must be either implemented in the SIMAP or 
accessible from the SIMAP. Whether the status is included as part of  
the SIMAP or accessible from the SIMAP is left to the solutions.

Therefore, based on this requirement we must optionally support the  
status in SIMAP. 

#### Implementation Proposal

Options for showing links in the topology:
* rely on the intended network for the target and the live network  
only shows the current topology
* real network should include both intended network and the state that 
reflects the current status

This draft proposes to use intended for the target state and that 
application can retrieve live and intended and compute the diff and 
merge them if needed.

The draft also proposes to add status, if required, via 
REQ-EXTENSIBLE in {{REQ-EXTENSIBLE}}.

### REQ-TEMPO-HISTORY: Geo-spatial, temporal, historical {#REQ-TEMPO}

#### Analysis
Move any relevant text from draft-havel-nmop-digital-map.

TODO: Do we need to add timestamps here for temporal.

Proposal for historical is done in REQ-SNAPSHOT.

In regards to geo-spatial, there are 2 options:
* refer to existing YANG module
* implement here
This draft proposes to link to the external

#### Implementation Proposal
Proposal for historical is done via REQ-SNAPSHOT
Proposal for geo-spacial is done via REQ-PLUGG
TODO: Proposal for temporal requires further analysis


## Feature simap-external

The solution is proposed for the following requirements:
	* REQ-STD-API-BASED
	* REQ-PLUGG

### REQ-STD-API-BASED: Standard based {#REQ-STD-API-BASED}

#### Analysis
RFC8345 already supports the standard model and API for the SIMAP 
requirements supported by RFC8345. We also need a standard model and 
API for the SIMAP requirements identified as RFC8345 gaps. This 
requirement also mentions that these APIs must also provide the 
capability to retrieve the links to external data/models.

#### Implementation Proposal
The implementation for the model changes for this requirement will be 
covered by the implementation proposal for REQ-PLUGG in {{REQ-PLUGG}}.


### REQ-PLUGG: Pluggable {#REQ-PLUGG}

#### Analysis
This requirement states that SIMAP must be pluggable and connect to  
other YANG modules and other nodelling mechanisms.

#### Implementation Proposal
The solution for the external links is proposed in a separate draft 
{{!I-D.vivek-simap-external-relationship}}.

TODO - Check if there is alternative proposal with some simple
references 




# Model Structure Details

The SIMAP data model is defined in the "ietf-simap-feature" module.  
Its structure is shown in Figure 1. The notation syntax follows the 
syntax used in {{!RFC8340}}.

{: #ietf-simap-features-tree}
~~~~
module: ietf-simap-topology
  +--ro networks-history
     +--ro network-history* [live-network-ref timestamp]
        +--ro live-network-ref    -> /nw:networks/network/network-id
        +--ro timestamp           yang:date-and-time
        +--ro file-id?            inet:uri

  augment /nw:networks/nw:network:
    +--ro extension?     <anydata>
  augment /nw:networks/nw:network/nw:node:
    +--ro extension?     <anydata>
  augment /nw:networks/nw:network/nw:node/nt:termination-point:
    +--ro extension?     <anydata>
  augment /nw:networks/nw:network/nt:link:
    +--ro extension?     <anydata>
  augment /nw:networks/nw:network:
    +--rw network-category?   identityref
    +--rw live-network-ref?   -> /nw:networks/network/network-id
    +--rw timestamp?          yang:date-and-time

~~~~
{: #fig-ietf-simap-features-tree 
title="The Structure of the SIMAP Data Model"}


# YANG Module

{: #ietf-simap-features-yang}
~~~~
module ietf-simap-features {
  yang-version 1.1;
  namespace "urn:ietf:params:xml:ns:yang:ietf-simap-features";
  prefix st;

  import ietf-network {
    prefix nw;
    reference
      "RFC 8345: A YANG Data Model for Network Topologies";
  }

  import ietf-network-topology {
    prefix nt;
    reference
      "RFC 8345: A YANG Data Model for Network Topologies";
  }

  organization
    "Network Management Operations (NMOP) Working Group";

  contact
    "WG Web:    <https://datatracker.ietf.org/group/nmop/>
     WG List:   <mailto:nmop@ietf.org>

     Editor:    Olga Havel
                <mailto:havel.olga@huawei.com>

     Editor:    Nigel Davis
                <mailto:ndavis@ciena.com>

     Editor:    TODO
                <mailto:TODO>";


  description
    "This module defines the SIMAP features model based on the
     requirements defined in
     https://datatracker.ietf.org/doc/draft-ietf-nmop-simap-concept/.

     Copyright (c) 2025 IETF Trust and the persons identified as
     authors of the code.  All rights reserved.

     Redistribution and use in source and binary forms, with or
     without modification, is permitted pursuant to, and subject to
     the license terms contained in, the Simplified BSD License set
     forth in Section 4.c of the IETF Trust's Legal Provisions
     Relating to IETF Documents
     (https://trustee.ietf.org/license-info).

     This version of this YANG module is part of RFC XXXX
     (https://www.rfc-editor.org/info/rfcXXXX); see the RFC itself
     for full legal notices.

    ";

  revision 2025-27-10 {
    description
      "Initial revision.";
    reference
      "XXX: A YANG Data Model for SIMAP Features";
  }


  /*
   * Common SIMAP groupings for optional RFC8345 extensions
   * Addressing requirements REQ-PROPERTIES as name, label and
   * description may be important properties that
   * clarify the topological roles for different layers and technologies
   */
  grouping simap-common {
    description "A reusable set of optional extensions for network,
                 node, termination point and link";
     anydata extension {
      config false;
      description
        "The extension point for any other meta info or data or any
        unknown extensions. Proposed solution for SIMAP requirement
        REQ-EXTENSIBLE. Any additional info that is topologically
        significant can also be added this way for requirement
        REQ-TOPO-ONLY or REQ-PROPERTIES.";
    }
  }

  augment "/nw:networks/nw:network" {
    description
      "Adding optional common simap extensions";
    uses st:simap-common;
  }

  augment "/nw:networks/nw:network/nw:node" {
    description
      "Adding optional common simap extensions";
    uses st:simap-common;
  }

  augment "/nw:networks/nw:network/nw:node/nt:termination-point" {
    description
      "Adding optional common simap extensions";
    uses st:simap-common;
  }

  augment "/nw:networks/nw:network/nt:link" {
    description
      "Adding optional common simap extensions";
    uses st:simap-common;
  }

  /*
   * Candidate for potential (REQ-POTENTIAL) and intended (REQ-INTENDED)
   * topology requirement
   */

   /*
   * Identities related to type of the network: 
   *  live, potential, intended
   */
  identity network-category {
    description "Base identity for the network category";
  }

  identity network-category-none {
    description "This is used when endpoint selection identifies if 
                 live, potential or intended. This is the default";
  }

  identity network-category-live {
    base "st:network-category";
    description "Live topology, there is only 1 instance of the live
                 network";
  }

  identity network-category-potential {
    base "st:network-category";
    description "Potential topology. There may be optionally one or
                 multiple instances of the potential network,
                 related to the current network";
  }

  identity network-category-intended {
    base "st:network-category";
    description "Intended topology, there is optionally 1 instance of
                 intended network related to the live network instance";
  }

  augment "/nw:networks/nw:network" {

    description
      "Adding optional capability for modelling of potential and
      intended network";

    leaf network-category {
      type identityref {
        base st:network-category;
      }
      description
        "The network category: live, potential, intended.
        Default is live.";
    }
    leaf live-network-ref {
      description
        "Required for potential and intended only to connect to the
        live network instance";
       type leafref {
         path "/nw:networks/nw:network/nw:network-id";
       require-instance false;
       }
    }
    leaf timestamp {
      description
        "Required for potential only to find related  historical
        snapshot";
      type yang:date-and-time;
    }
  }


  /*
   * Candidate for graph traversal (REQ-GRAPH-TRAVERSAL)
   * topology requirement (tp->tp, node->node)
   */
  augment "/nw:networks/nw:network/nw:node/nt:termination-point" {
    description
      "Adding optional read only link relations between connected
termination points for optimized graph traversal for paths";

    list linked-termination-point {
      config false;
      key "network-ref node-ref tp-ref";
      description
            "This list identifies any linked termination points, added
             optionally for read only for optimized path graph
             traversal";

      uses nt:tp-ref;
  }

  augment "/nw:networks/nw:network/nw:node" {
    description
      "Adding optional read only link relations between connected
nodes for optimized graph traversal for paths";

    list linked-node {
      config false;
      key "network-ref node-ref";
      description
            "This list identifies any linked nodes, added
             optionally for read only for optimized path graph
             traversal";

      uses nw:node-ref;
  }

  /*
   * Networks history, requirement REQ-SNAPSHOT
   */

  container networks-history {
    config false;
    list network-history {
      key "live-network-ref timestamp";
      description
        "Historical network. Snapshot is generated for each historical
        instance, network-ref is the reference for the live network";
      leaf live-network-ref {
         type leafref {
           path "/nw:networks/nw:network/nw:network-id";
         }
      }
      leaf timestamp {
        type yang:date-and-time;
      }
      leaf file-id {
        type inet:uri;
      }
    }
  }
}
~~~~
{: #fig-ietf-simap-topology-yang title="The YANG Data Model for SIMAP 
Features"}


# Security Considerations

# IANA Considerations

This document has no actions for IANA.

--- back

# Acknowledgments
{:numbered="false"}
