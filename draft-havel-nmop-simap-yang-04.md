---
title: "A YANG Data Model for SIMAP Core Topology"
abbrev: SIMAP Core Topology YANG Model 
docname: draft-havel-nmop-simap-yang-04
category: info

submissiontype: IETF 
number:
date:
consensus: true
v: 4
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
Maps (SIMAP) Core Topology. It extends the RFC8345 YANG modules to 
support the subset of core SIMAP requirements. This document will only 
focus on modelling proposal for each of the core SIMAP topology 
requirements not supported by RFC8345. Any related terminology, 
concepts, use cases and requirements are defined outside of this 
draft and this draft will only refer to them, analyze how to model 
and propose the implementation solutions.

--- middle

# Introduction

{{!I-D.ietf-nmop-simap-concept}} defines Service & Infrastructure 
Maps (SIMAP) as a data model that provides a topological view of the 
operator's networks and services, including how it is connected to other 
models (e.g., inventory), and external data (e.g., observability data, 
and operational knowledge). This model represents a multi-layered 
topology and offers mechanisms to navigate 
amongst layers and correlate between them, including layers from 
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

This document will address the core SIMAP topology requirements. First 
we identified which requirements are supported by RFC 8345 and which 
are the gaps. The requirements that are supported by RFC8345 will 
just need clarification in this document. The requirements identified as 
gaps will be modelled in this document in the YANG module 
ietf-simap-topology.

The other requirements that are not classified as Core SIMAP Topology 
will be addressed in the separate draft.

# Terminology

Terminology for SIMAP, SIMAP modelling, SIMAP data, topology, 
topology layer, multi-layered topology is defined in {{!RFC8345}}

# Why RFC8345 is a Good Approach for SIMAP Modelling

The primary reason for selecting RFC 8345 as the modeling framework 
is its simplicity and its ability to partially support the majority of 
core requirements.

RFC 8345 defines an abstract, generic, and foundational model for 
representing network and service topologies. It offers a straightforward 
mechanism for modeling networks and services as layered topologies, 
using four core entity types at each layer: network, node, link, and 
termination point. The model supports common relationships within the 
same layer, as well as underlay/overlay relationships across layers.

The following relationships are defined by RFC 8345:

* Relationships inside each layer are:

    * contains (cardinality 1:0..n)
 
        * a network contains nodes
        * a network contains links
        * a node contains termination points

    * source (cardinality: 1:0..1)
        * a link has one source termination point

    * destination (1:0..1)
        * a link has one destination termination point

* Relationships between layers, where underlay nodes, 
links and termination points must match underlay network

    * supporting for underlay (cardinality 0..*:0..*)
        * a network has supporting networks
        * a node has supporting nodes
        * a link has supporting links
        * a termination point has supporting termination points

Please note the following characteristics of the RFC 8345 topology model:

* Network type is used to identify the layer; sub-layers are not 
supported.

* Instantiation is flexible: a single network instance may represent 
one or multiple layers.

* The core entities — network, node, termination point, and link — are 
defined as abstract concepts and do not support role 
designation within the topology (e.g., root, leaf).

* The entities node, termination point and link are contained in a single
network; they cannot be shared between the networks; they cannot 
connect different networks.

* Only unidirectional links are modeled, each with one source and one 
destination. Bidirectional connectivity must be represented using 
two separate unidirectional links.

* Layering relationships are expressed solely through the supporting 
construct, without additional semantics such as underlay, 
primary, backup, load-sharing, path, sequential, or parallel roles.

* The supporting relationship is restricted to same-type entities only:
(network->network, node->node, link->link, termination point -> 
termination point)

* The model focuses exclusively on topology layering and does not 
support partitioning, such as sub-networks or sub-domains. 

# Requirements Categorization
The requirements from {{!I-D.ietf-nmop-simap-concept}} were 
classified into features. The requirements identified as core SIMAP 
topology requirements are split into 2 categories:

* {{supported-requirements}}: Requirements supported by RFC8345
    * these are SIMAP requirements already supported by {{!RFC8345}}
that do not require and extensions or modifications in RFC8345 for
SIMAP modelling

* {{gap-requirements}}: Requirements not supported by RFC8345 (Gaps)
    * these are SIMAP requirements that are not supported by RFC8345
and are identified as RFC8345 gaps, they require extensions/changes to
ietf-network and ietf-network-topology YANG modules.

This document will focus on modelling the core SIMAP topology RFC8345 
gaps and ensuring that the modelling changes do not break any 
requirements already supported by {{!RFC8345}}.

## Requirements supported by RFC8345 {#supported-requirements}

The following core SIMAP topology requirements are already supported by 
RFC8345 and do not require any modelling extensions/modifications to 
ietf-network and ietf-network-topology:

* Operator Requirements:
    * REQ-BASIC-MODEL-SUPPORT: Basic model with network, node, link,
and termination point entity types.
    * REQ-LAYERED-MODEL: Topology layers from physical layer up to
service layer.
    * REQ-VIEWPOINTS: Different viewpoints provide capability to have
different views to different stakeholders.
    * REQ-PASSIVE-TOPO: Topology includes passive topology.
    * REQ-TOPOLOGY-ABSTRACTION: Navigation across the abstraction
levels inside a single network layer
    * REQ-LIVE: Live network topology. TODO: Check if createTime /
updatedTime / deletedTime is here or somewhere else.
    * REQ-LAYER-NAVIGATE: Navigation inside the topology layer and
between the topology layers
    * REQ-DATA-PLANE-FLOW: Provider data plane (Flow) needs to be
correlatable to underlay and customer data plane to overlay topology
    * REQ-CONTROL-PLANE: Underlay control plane routing state needs
to  be correlatable to underlay L3 topology. Overlay control-plane
routing state needs to be correlate-able to overlay L3 network topology.

* Design Requirements:
    * REQ-TOPO-ONLY: SIMAP should contain only topological information.

* Architectural Requirements:
    * REQ-USABILITY: The SIMAP API must be simple and easy to
integrate  with the client applications. This requirement
is supported for the unidirectional and point2point networks.

We must keep these requirements in mind when proposing implementation
solutions for gaps, as they are applicable to how we model the
extensions / changes. We also need some implementation guidelines to 
ensure that the coe topology is not broken via extensions. The RFC8345 
instantiation guidelines are specified as follows:

### Core topology entities (REQ-BASIC-MODEL-SUPPORT, REQ-TOPO-ONLY)
A SIMAP topology uses the RFC 8345 network, node, termination-point,
and link entities and their existing containment and
supporting\relationships. A network is the scope for its nodes and
links; a node contains termination points; links describe connectivity
between termination points. SIMAP does not require an additional generic
topology entity or generic relationship type to instantiate its core
topology. Technology- or layer-specific semantics can be supplied by
augmentations or references to other models, without treating unrelated
operational data as topology.

### Layers and sublayers (REQ-LAYERED-MODEL)
A SIMAP layer can be instantiated as an RFC 8345 network topology.
Separate network instances can represent different layers, technologies,
or sublayers where their distinction is operationally meaningful.
The network-types mechanism identifies the kind of topology;
it does not by itself prescribe a universal hierarchy of layers or
sublayers. Supporting-network, supporting-node, supporting-link,
and supporting-termination-point references express applicable
relationships to underlying topology instances. Where a sublayer needs
explicit semantics beyond the network type and supporting references,
that need is separate from this clarification and requires a modelling
decision.

### Viewpoints and topology abstraction (REQ-VIEWPOINTS,
REQ-TOPOLOGY-ABSTRACTION)
Different stakeholder viewpoints can be represented by separate 
RFC 8345 topology instances, including abstracted views of more 
detailed topologies. Abstract nodes, links, and termination points 
remain RFC 8345 entities; their relationship to underlying entities is 
represented using the appropriate supporting references. 
The supported and supporting objects need not represent different 
technology layers: an abstract view and a detailed view may belong
to the same conceptual layer. A viewpoint is not a new generic
entity type. This guidance does not define a mechanism for
dynamically selecting or authorizing a viewpoint.

###Passive topology (REQ-PASSIVE-TOPO)
Passive infrastructure can be represented using the same network,
node, termination-point, and link constructs. The interpretation
of these entities depends on the passive technology and topology type.
Passive components do not require a separate generic SIMAP entity
category; inventory identifiers and equipment attributes can be
correlated through existing or separately specified inventory mappings.
This section does not prescribe a particular physical inventory to 
topology mapping.

### Live topology (REQ-LIVE)
The current or live topology is an instance of the RFC 8345 core
topology model, rather than a separate set of generic entity types.
Whether that instance is exposed as operational state, and how it is
synchronized or discovered, depends on the server and the relevant
datastore and protocol arrangements. Historical snapshots, potential
topologies, intended topologies, and their relationships are handled
by the companion SIMAP Additional Features draft; this section does not
introduce lifecycle metadata into the core topology.

### Navigation within and between layers (REQ-LAYER-NAVIGATE)
Within a topology, clients can navigate the RFC 8345 containment
structure from network to node and termination point, and follow link
source and destination references to identify connected termination
points. Across topology instances or layers, clients can follow
supporting references between corresponding network, node, link, and
termination-point entities. This is core topology navigation. 
Optional direct termination-point-to-termination-point or node-to-node
graph traversal shortcuts, as discussed under REQ-GRAPH-TRAVERSAL,
are treated in the companion draft and are not prerequisites for
navigating RFC 8345 instances.

### Data-plane and control-plane topologies (REQ-DATA-PLANE-FLOW, 
REQ-CONTROL-PLANE)
Data-plane and control-plane views can be instantiated as distinct
RFC 8345 topology instances or layers. When an entity in one view
is supported by an entity of the corresponding type in another view,
the existing supporting relationship can express that correlation.
The topology represents the relevant connectivity and layering,
while detailed flow records, protocol state, and routing information
can remain in their respective models and be correlated externally.
This does not imply that every control-plane-to-data-plane correlation
can be expressed by an RFC 8345 same-type supporting relationship;
cases needing other relationships remain outside this clarification.

### Tunnel and path instantiation; RFC 8795 context
When a tunnel or path is represented as a topological connectivity
abstraction in the RFC 8345 SIMAP core, it may be instantiated as
a link with appropriate endpoint termination points and
supporting links where applicable. This is an instantiation convention,
not a claim that every tunnel or path must be an RFC 8345 link.
RFC 8795 provides TE-specific constructs and semantics that do not
map one-to-one to RFC 8345 core link instances, including TE
termination points and underlay/path information.
Interoperability therefore requires explicit identification
of what a particular SIMAP link represents and which TE semantics
remain in the RFC 8795 model. No new generic tunnel,
path, or TE termination-point entity is introduced here.

### Simplicity and usability (REQ-USABILITY)
To retain the simplicity of RFC 8345, a SIMAP core instance should
use the existing four entity types and supporting relationships
where they are sufficient. Additional semantics should be scoped
to optional augmentations or references rather than creating parallel
generic topology constructs. This guideline concerns modelling
simplicity; it does not establish performance, query, or
API usability guarantees.


## Requirements not supported by RFC8345 (Gaps) {#gap-requirements}

Based on the initial analysis, the following core SIMAP topology 
requirements are not fully supported by the RFC8345 and require 
extensions or modifications:

* Operator Requirements:

    * REQ-SEMANTIC: Network topology semantics. Gap: some semantic  
missing. Analysis and solution presented in {{REQ-SEMANTIC}}.
    * REQ-BIDIR: SIMAP must provide a mechanism to model 
bidirectional  links. Gap: complex. Analysis and solution 
presented in {{REQ-BIDIR}}.
    * REQ-MULTI-POINT: SIMAP must provide a mechanism to model 
multipoint  links. Gap: complex. Analysis and solution presented in 
{{REQ-MULTI-POINT}}.
    * REQ-MULTI-DOMAIN: SIMAP must provide a mechanism to model links 
and nodes between networks. Analysis and solution presented in 
{{REQ-MULTI-DOMAIN-LINK}} and {{REQ-MULTI-DOMAIN-NODE}}.
    * REQ-SUBNETWORK: SIMAP must provide a mechanism to model network 
decomposition into sub-networks. Analysis and solution presented in 
{{REQ-SUBNETWORK}}.
    * REQ-SUPPORTING: SIMAP must provide a mechanism to model 
supporting  relationships between different types of topological 
entities (e.g., a termination point is supported by the node). Gap: 
between different types. Analysis and solution presented in 
{{REQ-SUPPORTING}}.

* Design Requirements:
    * REQ-PROPERTIES: SIMAP entities should mainly contain properties
used to identify topological entities at different layers, identify 
their roles, and topological relationships between them. Analysis and 
solution presented in {{REQ-PROPERTIES}}.
    * REQ-RELATIONSHIPS: SIMAP should contain all topological 
relationships inside each layer or between the layers 
(underlay/overlay). Analysis and solution presented in 
{{REQ-REL}}.

The changes to the RFC8345 model for these requirements would be
specified inside: if-feature simap-core-topology.


# Modelling approach in this draft version

There are multiple options where the modelling changes not supported 
by RFC8345 can be done. The following are candidate approaches:

* via new SIMAP YANG module, augmenting RFC8345 YANG modules.
* via RFC8345 bis YANG modules.
* via new SIMAP model that does not augment RFC8345 YANG modules 
(either new YANG module or some other way).
* different approach for different groups of requirements.

This draft introduces the new ietf-simap-topology YANG module that 
defines augmentation for ietf-network and ietf-network-topology nodes 
to support the core SIMAP topology requirements. As this is the simplest 
way to analyze the gaps and review the modelling, the current version of 
this draft uses this approach. This does not mean that this is the 
proposed approach, it will be used for review of the proposed 
modelling changes until the final approach has been agreed in the 
NMOP. The final approach can be decided at the later stage, after the 
modelling is reviewed and proposed. The modelling can then be moved 
to the agreed module.

By proposing YANG models to address SIMAP requirements, we do not 
suggest that this is the only approach to modeling SIMAP. SIMAP can 
also be represented using ontological frameworks; however, that 
alternative is beyond the scope of this document.

# Solution Proposal for the RFC8345 Gaps

## SIMAP Features {#SIMAP-FEATURES-SOLUTION}

We are introducing a set of SIMAP features that will be used to group 
requirements and optionally include them in the SIMAP module if the 
feature is implemented. Definitions will be tagged with the feature 
name and are only valid on a SIMAP server that supports that feature.

The following is the initial set of SIMAP features:

{: #SIMAP-FEATURES}
~~~~
module ietf-simap-topology {
    /* … other statements … */

	feature simap-core-topology {
		description
		    "This feature indicates that the SIMAP server supports those
             SIMAP requirements not supported by RFC8345, but related 
             to core live topology";
	}

    /* … other statements … */
}
~~~~
{: #SIMAP-FEATURES title="The SIMAP Features"}


## Feature simap-core-topology

The solution is proposed for the following requirements:

* REQ-PROPERTIES
* REQ-BIDIR
* REQ-MULTI-POINT
* REQ-MULTI-DOMAIN
* REQ-SUBNETWORK
* REQ-SEMANTIC
* REQ-SUPPORTING
* REQ-RELATIONSHIPS

The changes to the RFC8345 model for these requirements would be 
specified inside: if-feature simap-core-topology.

### REQ-PROPERTIES: Properties significant for topology {#REQ-PROPERTIES}

#### Analysis
This design requirement states that SIMAP entities should contain  
properties used to identify topological entities at different layers, 
identify their roles, and topological relationships between them.  
Multiple operator requirements are already covering some specifics, 
like REQ-BIDIR, REQ-MULTI-POINT and we will analyze and propose  
implementation for any missing semantic as part of REQ-SEMANTIC.

Nevertheless, sometimes the topological information is in the textual 
form and cannot be done via the model and is communicated via name, 
description, labels. We can also see that many current  augmentations 
of RFC8345 add some common properties that are generic and needed at 
all layers and technologies (e.g. name).

Therefore, we propose to add name, labels and description into the  
core model for all SIMAP entities but keep them optional for backward 
compatibility.

#### Implementation Proposal

{: #REQ-PROPERTIES-YANG}
~~~~
  /*
   * Common SIMAP groupings for optional RFC8345 extensions
   * Addressing requirements REQ-PROPERTIES as name, label and
   * description may be important properties that
   * clarify the topological roles for different layers and technologies
   */

  grouping simap-common {
    description "A reusable set of optional extensions for network,
                 node, termination point and link";
    leaf name {
	  if-feature simap-core-topology;
      type string;
      description
        "The user friendly name, if required.
        It is optional, for backward compatibility";
    }
    leaf-list label {
	  if-feature simap-core-topology;
      type string;
      description
        "Used for optionally adding any labels to the instances,
         if required";
    }
    leaf description {
	  if-feature simap-core-topology;
      type string;
      description
        "Used for optionally adding any description to the instances,
        if required";
    }

	TODO: Check if createTime / updatedTime / deletedTime is here or 
somewhere else. 

    :
    :


  }
~~~~
{: #REQ-PROPERTIES-YANG title="The proposal for bidirectional links"}



### REQ-BIDIR: Bidirectional Links {#REQ-BIDIR}

#### Analysis

One of the core characteristics of any network topology is the link 
directionality. While data flows are unidirectional, the 
bidirectional links are also common in networking. Examples are 
Ethernet cables, bidirectional SONET rings, socket connection to the 
server. We also encountered requirements for simplified service layer 
topology, where we want to model link as bidirectional to be 
supported by unidirectional links at the lower layer.

* RFC8345 supports only unidirectional links, it was done 
intentionally to keep the model as simple as possible
* RFC8345 suggests to model bidirectional links via multiple 
instances of unidirectional links
* while keeping the model simpler in RFC8345, the APIs and data 
become more complex and error prone. There is increase of number of 
instances / data transferred over API, stored in the DB, or 
managed/monitored and some errors may occur during creation of the 
entities
* while keeping the model simpler in RFC8345, we lack the semantics  
for the bidirectional links and capability to expose and create them 
in the unified manner


#### Implementation Proposal

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
	  if-feature simap-core-topology;
      type identityref {
        base st:link-direction;
      }
      description
        "The direction of the link: unidirectional (link-direction-uni)
        or bidirectional (link-direction-bi). It can also be any other
        custom identity defined with base link-direction. It is
        optional, so the model supports the solution without the link
        direction information, either if not known or for backward
        compatible case.";
    }

    list tp {
	  if-feature simap-core-topology;
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

### REQ-MULTI-POINT: Multipoint Links {#REQ-MULTI-POINT}

#### Analysis
One of the core characteristics of any network topology is its type 
and  link cardinality. Any topology model should be able to model any 
topology type in a simple and explicit way, including point to 
multipoint, bus, ring, star, tree, mesh, hybrid and daisy chain. 
Any topology model should also be able to model any link cardinality 
in a simple and explicit way, including point to point, point to 
multipoint, multipoint to multipoint or hybrid.

* RFC8345 defines all links as point to point and unidirectional,
it does not support multi-point links (hub and spoke, full mesh,  
hybrid). It was done intentionally to keep the model as simple as 
possible.
* RFC8345 suggests to model the multi-point networks via pseudo nodes.
* while keeping the model simpler in RFC8345, the APIs and data 
become more complex and error prone. There is increase of number of 
instances / data transferred over API, stored in the DB, or  
managed/monitored.
* while keeping the model simpler in RFC8345, we lack the semantics 
for multi-point links and capability to expose and create them 
in the unified manner

Clarification on hybrid links: The multi-point link is an abstraction of 
underlying structure detail. The underlying structure is currently 
expressed in terms of a simple identity which essentially references  
a pattern of underlying flow where that pattern describes the effect  
(another abstraction) of that flow between each point of the link.  
There are currently a few well understood patterns, but the list will 
expand. The list of patterns, not surprisingly, will be the same as 
for  service, for connectivity and for flow (as the link arises from  
them). The approach of introdutcion of hybrid links allows for a 
solution to express a complex arrangement in abstraction without 
needing, for each instance, to lay out the detail. It also allows the 
client application to “understand” the effect without having to probe 
that detail.


#### Implementation Proposal

{: #REQ-MULTI-POINT-YANG}
~~~~
  identity link-type {
      description
        "Base identity for the internal structure of the link";
  }

  identity link-type-p2p {
      base "st:link-type";
      description "Point to point link";
  }

  identity link-type-p2mp {
      base "st:link-type";
      description "Point to multi-point link";
  }

  identity link-type-mp2mp {
      base "st:link-type";
      description "Multi-point to multi-point link";
  }

  identity link-type-hybrid {
      base "st:link-type";
      description 
        "Hybrid links, combination of the other three.  
        The multi-point link is an abstraction of underlying structure
        detail. The underlying structure is currently expressed in 
        terms of a simple identity which essentially references
        a pattern of underlying flow where that pattern describes the 
        effect (another abstraction) of that flow between each point 
        of the link. There are currently a few well understood 
        patterns, but the list will expand. The list of patterns, not 
        surprisingly, will be the same as for  service, for 
        connectivity and for flow (as the link arises from them). The 
        approach of introduction of hybrid links allows for a 
        solution to express a complex arrangement in abstraction without 
        needing, for each instance, to lay out the detail. It also 
        allows the client application to “understand” the effect 
        without having to probe that detail.";
  }

  augment "/nw:networks/nw:network/nt:link" {

    :
    :
    leaf link-type {
	  if-feature simap-core-topology;
      type identityref {
        base st:link-type;
      }
      description
        "The reference to the specification for the internal structure
        of the link. It can be point to point (link-type-p2p), point to
        multipoint (link-type-p2mp) or multipoint to multipoint
        (link-type-mp2mp). It can also be any other custom identity
        defined with base link-type. It is optional, so the model
        supports the solution without the link type information,
        either if not known or for backward compatible case.";
    }
    list tp {
	  if-feature simap-core-topology;
      key "network-ref node-ref tp-ref";
      description "List of termination points in the link";
      uses nt:tp-ref;

      leaf tp-role {
        type identityref {
          base st:tp-role;
        }
        description
          "The role of the termination point in the link defined in the
           link-type spec.";
        }
    :
    :
    }

  }

~~~~
{: #REQ-MULTI-POINT-YANG title="The proposal for multi-point links"}

On the stand-alone point to point unidirectional case (the case 
supported by RFC8345 currently), we recommend that, in a new deployment, 
this is represented simultaneously in terms of the existing approach and 
the new approach to bridge between old and new solutions. We can explore 
this evolution in more detail as it is very important that we get this 
right. In the longer term, it would be ideal if all new solutions used 
the proposed multi-point approach for point to point (where point to 
point is simply a represented with a list of two points). Clearly, 
as always, there will be a long tail on the legacy in the network, 
so some adaptation will be required to deal with older deployments, 
but this is usual and expected. It should not prevent advancement.

Open Issue: Comment received to address: To improve clarity, would it 
make sense to define rules? For example:

* point-to-point: 1-to-1
* point-to-multipoint: 1-to-2+
* multipoint-to-multipoint: 2+-to-2+
* And possibly multipoint-to-point: 2+-to-1 (if such cases are to be 
supported)

### REQ-MULTI-DOMAIN: Multi-domain Links {#REQ-MULTI-DOMAIN-LINK}

#### Analysis

Link between multiple networks, sub-networks, or domains is the common 
concept in network topology. SIMAP must provide a mechanism to model 
links between networks.

* RFC8345 defines all links as belonging to one network instance and 
having the source and destination as node and termination point only, 
not allowing to link to termination point of another network.
* This does not allow for links between networks in the case of  
multi-domains or partitioning.
* The only way would be to model each domain as node and have links 
between them

#### Implementation Proposal
Allows the link to terminate on the termination point that is
on another network.

{: #REQ-MULTI-DOMAIN-LINK-YANG}
~~~~

  augment "/nw:networks/nw:network/nt:link" {

    list tp {
	  if-feature simap-core-topology;
      key "network-ref node-ref tp-ref";

    }
  }
~~~~
{: #REQ-MULTI-DOMAIN-LINK-YANG 
title="The proposal for multi-domain links"}

This way we can model the links in the following ways:
1. link belongs to one network (e.g. IS-IS Area A) but pointing to remote 
point of another network (e.g. IS-IS Area B)
2. link belongs to the parent domain (e.g. IS-IS AS Domain), but 
points to termination points of children domains (e.g.  
IS-IS Area A and IS-IS Area B)

### REQ-MULTI-DOMAIN: Multi-domain Nodes {#REQ-MULTI-DOMAIN-NODE}

#### Analysis
Nodes shared between multiple domains is the common 
concept in network topology (e.g. Area Border Routers that connect 
different areas in OSPF). 
SIMAP must provide a mechanism to model nodes shared between networks.

* RFC8345 defines all nodes as belonging to one network instance, 
not allowing to have a node inside the 2 networks.
* This does not allow for sharing a node between networks in the case of  
multi-domains or partitioning.
* Current approach is to either:
	* model a node with 2 instances, one per domain, which in the case 
of OSPF would not reflect the topology
 	* have the same instance in 2 networks, only if node-id is unique 
on all nodes in all networks

#### Implementation Proposal
For further study, implement different candidates via hackathon

### REQ-SUBNETWORK: Subnetworks and partitioning {#REQ-SUBNETWORK}

#### Analysis
RFC8345 does not model networks being part of other networks, 
therefore cannot model subnetworks and network partitioning. 
We encountered this problem with modelling IS-IS and OSPF domains  
and areas. The goal is to model AS/domain with multiple areas so that 
the SIMAP model contains information about how the AS is first split 
into different IGP domains and how each IGP domain is split into 
different areas. This is a common problem for both IS-IS and OSPF.

#### Implementation Proposal

The following are the identified implementation candidates:

* Candidate 1 - network->subnetwork direction (currently proposed in 
the YANG module):

{: #REQ-SUBNETWORK-YANG}
~~~~

  augment "/nw:networks/nw:network" {
    list subnetwork {
	  if-feature simap-core-topology;
        key "network-ref";
           description
             "A subnetwork of the network, supports partitioning";
           leaf network-ref {
             type leafref {
               path "/nw:networks/nw:network/nw:network-id";
             require-instance false;
             }
             description
               "References the subnetworks";
           }
    }
  }

~~~~
{: #REQ-SUBNETWORK-YANG title="The proposal for subnetworks}

* Candidate 2 - subnetwork->network direction:

{: #REQ-SUBNETWORK-YANG-2}
~~~~

  augment "/nw:networks/nw:network" {
    leaf parent-ref {
	  if-feature simap-core-topology;
      type leafref {
        path "/nw:networks/nw:network/nw:network-id";
        require-instance false;
      }
      description "References the parent network from subnetworks";
    }
  }

~~~~
{: #REQ-SUBNETWORK-YANG-2 title="The proposal for subnetworks}

### REQ-SEMANTIC: Network Topology Semantic {#REQ-SEMANTIC}

#### Analysis

Many RFC8345 augmentations add the additional topological semantics,  
with same concepts modelled in a different way in different 
augmentations. We already mentioned that some semantic is missing 
from the RFC8345 topology model, like bidirectional and multi-point.  
The following is also missing from the model:

   *  Relationship Properties.  The supporting relationship could have
      additional attributes that give more information about the
      supporting relationship.  That way we could use it for
      aggregation, underlay with primary/backup, load balancing, hop,
      sequence, etc.

   *  Termination point roles.  We are missing semantics for the common
      topology roles: uni, i-nni,e-nni, primary, backup, hub, spoke,

   *  Node roles.  We are missing semantics for the common node roles:
      access, core, metro

   *  Link roles.  We are missing semantics for the common link roles:
      uni, i-nni, e-nni

   *  Layers / Sublayers.  We need further analysis to determine in
      network types are sufficient to support all scenarios for layers/
      sublayers.  Alternatevely, some sub-layer info needs to be added.

   *  Tunnels and paths.  We can model tunnels and paths via RFC8345
      but we loose some semantics that is in RFC8795.


#### Implementation Proposal
For further study, implement different candidates via hackathon

###REQ-SUPPORTING: Extension to supporting {#REQ-SUPPORTING}

#### Analysis
RFC8345 defines supporting relationships only between the same type
of entities.  Networks can only be supported by networks, nodes can
only be supported by nodes, termination points can only be supported
by terminations points and links can only be supported by links.

During the hackathons, we had a scenario where at one layer of 
topology we had a link with termination points where the termination 
points are logical without the underlay termination point, but the only 
underlay connection we were able to define was to the underlay nodes. 
The same happened with nodes and networks.

Therefore, we encountered the need to have termination points 
supported by nodes and nodes supported by networks. The {{?RFC8795}} 
also adds additional underlay relationship between node and topology 
and link and topology, but via a new underlay topology and not via 
the core supporting relationship.

#### Implementation Proposal
For further study, implement different candidates via hackathon

### REQ-RELATIONSHIPS: Relationships significant for topology {#REQ-REL}

#### Analysis
The following relations are already supported by RFC8345, but only 
within the same layer:

* containment

	* networks contain nodes
	* nodes contain termination points

The SIMAP relationships should also provide information required
to define semantics for layered network topologies, such as providing:

      *  additional information that helps with navigation inside a
         layer and between the layers, for example, easy identification
         of resources at the physical layer in primary versus backup
         paths, if the underlay resources are used for load balancing or
         for backup,

      *  capability to model nodes, termination points, and links
         contained in a network, but also nodes and links shared between
         networks, and


#### Implementation Proposal
For further study, implement different candidates via hackathon


# Model Structure Details

The SIMAP data model is defined in the "ietf-simap-topology" module.  
Its structure is shown in Figure 1. The notation syntax follows the 
syntax used in {{!RFC8340}}.

{: #ietf-simap-topology-tree}
~~~~
module: ietf-simap-topology

  augment /nw:networks/nw:network:
    +--rw name?          string
    +--rw label*         string
    +--rw description?   string
  augment /nw:networks/nw:network/nw:node:
    +--rw name?          string
    +--rw label*         string
    +--rw description?   string
  augment /nw:networks/nw:network/nw:node/nt:termination-point:
    +--rw name?          string
    +--rw label*         string
    +--rw description?   string
  augment /nw:networks/nw:network/nt:link:
    +--rw name?          string
    +--rw label*         string
    +--rw description?   string
  augment /nw:networks/nw:network/nt:link:
    +--rw link-type?        identityref
    +--rw link-direction?   identityref
    +--rw tp* [network-ref node-ref tp-ref]
       +--rw tp-ref          -> /nw:networks/
network[nw:network-id=current()/../network-ref]/
node[nw:node-id=current()/../node-ref]/nt:termination-point/tp-id
       +--rw node-ref        -> /nw:networks/
network[nw:network-id=current()/../network-ref]/node/node-id
       +--rw network-ref     -> /nw:networks/network/network-id
       +--rw tp-role?        identityref
       +--rw tp-direction?   identityref
  augment /nw:networks/nw:network:
    +--rw subnetwork* [network-ref]
       +--rw network-ref    -> /nw:networks/network/network-id

~~~~
{: #fig-ietf-simap-topology-tree 
title="The Structure of the SIMAP Data Model"}


# YANG Module

{: #ietf-simap-topology-yang}
~~~~
module ietf-simap-topology {
  yang-version 1.1;
  namespace "urn:ietf:params:xml:ns:yang:ietf-simap-topology";
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
    "This module defines the SIMAP core topology model based on the
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

  revision 2026-27-10 {
    description
      "Initial revision.";
    reference
      "XXX: A YANG Data Model for SIMAP Core Topologies";
  }



  identity tp-role {
    description "Base identity from which all tp roles in the link are
                 derived.";
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
    leaf name {
      type string;
      description
        "The user friendly name, if required.
        It is optional, for backward compatibility";
    }
    leaf-list label {
      type string;
      description
        "Used for optionally adding any labels to the instances,
         if required";
    }
    leaf description {
      type string;
      description
        "Used for optionally adding any description to the instances,
        if required";
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
   * Adding bidirectional and multi-point capabilities to the link
   */

  /*
   * Identities related to link directionality and cardinality of points
   */

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

  /*
   * Identities related to link type and cardinality of points
   */
  identity link-type {
      description 
        "Base identity for the internal structure of the link";
  }

  identity link-type-p2p {
      base "st:link-type";
      description "Point to point link";
  }

  identity link-type-p2mp {
      base "st:link-type";
      description "Point to multi-point link";
  }

  identity link-type-mp2mp {
      base "st:link-type";
      description "Multi-point to multi-point link";
  }

  identity link-type-hybrid {
      base "st:link-type";
      description
        "Hybrid links, combination of the other three"
        The multi-point link is an abstraction of underlying structure
        detail. The underlying structure is currently expressed in
        terms of a simple identity which essentially references
        a pattern of underlying flow where that pattern describes the
        effect (another abstraction) of that flow between each point
        of the link. There are currently a few well understood
        patterns, but the list will expand. The list of patterns, not
        surprisingly, will be the same as for  service, for
        connectivity and for flow (as the link arises from them). The
        approach of introduction of hybrid links allows for a
        solution to express a complex arrangement in abstraction without
        needing, for each instance, to lay out the detail. It also
        allows the client application to “understand” the effect
        without having to probe that detail.";
  }

  /*
   * Identities related to termination point directionality and role
   * in the link
   */

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
    description
      "Models the link that can be unidirectional, bidirectional,
      point-to-point,point-to-multi-point, multipoint-to-multipoint,
      therefore addressing requirements:
      - REQ-BIDIR
      - REQ-MULTI-POINT

      with augmenting we keep source and destination for backward
      compatibility.

      Allows for the link to terminate on the termination point that is
      on another network, therefore also addressing the gap:
      - REQ-MULTI-DOMAIN
      ";

    leaf link-type {
      type identityref {
        base st:link-type;
      }
      description
        "The reference to the specification for the internal structure
        of the link. It can be point to point (link-type-p2p), point to
        multipoint (link-type-p2mp) or multipoint to multipoint
        (link-type-mp2mp). It can also be any other custom identity
        defined with base link-type. It is optional, so the model
        supports the solution without the link type information,
        either if not known or for backward compatible case.";
    }

    leaf link-direction {
      type identityref {
        base st:link-direction;
      }
      description
        "The direction of the link: unidirectional (link-direction-uni)
        or bidirectional (link-direction-bi). It can also be any other
        custom identity defined with base link-direction. It is
        optional, so the model supports the solution without the link
        direction information, either if not known or for backward
        compatible case.";
    }

    list tp {
      key "network-ref node-ref tp-ref";
      description "List of termination points in the link";
      uses nt:tp-ref;

      leaf tp-role {
        type identityref {
          base st:tp-role;
        }
        description
          "The role of the termination point in the link defined in the
           link-type spec.";
        }
      leaf tp-direction {
        type identityref {
          base st:tp-direction;
        }
        description
          "The direction of the point in the link";
      }
    }
  }

  /*
   * Subnetworks, requirement REQ-SUBNETWORK
   */
  augment "/nw:networks/nw:network" {
    list subnetwork {
        key "network-ref";
           description
             "A subnetwork of the network, supports partitioning";
           leaf network-ref {
             type leafref {
               path "/nw:networks/nw:network/nw:network-id";
             require-instance false;
             }
             description
               "References the subnetworks";
           }
    }
  }

}
~~~~
{: #fig-ietf-simap-topology-yang title="The YANG Data Model for SIMAP"}


# Security Considerations

# IANA Considerations

This document has no actions for IANA.

--- back

# Acknowledgments
{:numbered="false"}
