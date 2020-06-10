:tocdepth: 3

.. Please do not modify tocdepth; will be fixed when a new Sphinx theme is shipped.

.. sectnum::

.. note::

   **This technote is a work in progress.**

   Description of technote

Introduction
============

Document Overview
-----------------
The purpose of this document is to outline the VoIP infrastructure high-level design (HLD) for the Rubin Observatory summit and base sites. It would recommend an architecture based on the requirements set by the project, especially -but not limited to- the Tiger Team in different ICDs and in the documents mentioned in section 1.4.

The intent of this document is to provide an architecture that fulfills the requirements outlined by the project, keeping in mind current needs but also future growth of the network. The HLD does not delve into low-level details (i.e. configuration files, performance analysis, etc...).

As long as the HLD status is draft, it is susceptible to modifications and additions by the IT group or by the request of other subsystems.

After acceptance this document may or not be under change control, and regardless of that this is considered a living document that will be updated upon requirement addition or changes, experiences in the deployment, and operations process.

Scope
-----
* Deliver a recommended VoIP architecture for the project.
* Deliver enough details to allow for a Low-Level Desing (LLD) document.
* Fulfill the current VoIP needs of the project and allow for future growth.
* This document focuses only on VoIP network requirements to provide internal, inter-project (via a voice gateway) and PSTN voice calls.
* This document DOES NOT cover requirements for call managers redundancy/survival or direct SIP/H323 signaling or direct RTP/SRTP integration with other AURA projects.

Assumptions and Caveats
-----------------------
* It is assumed all the project concepts derived from other documents such as those mentioned in section 1.4 and/or the associated ICDs, are correct and remain unchanged.
* It is assumed the reader is familiar with the project VoIP requirements. Furthermore, it is also assumed the reader is familiar with VoIP technologies such as voice gateways, call managers, E1 trunk links, and protocols such as SIP, H323, RTP and SRTP, and has a basic understanding of networking concepts and the technologies that will be used to fulfill the aforementioned requirements.

Related Documents
-----------------
* LSE-78 LSST Observatory Network Design
* LSE-309 Summit to Base ITC Design

Technical Solution Overview
============

Data Gathering
--------------
Rubin Observatory in Chile from the beginning of the project did rely on the VoIP services provided by NOAO's CTIO technology group (CISS), including VoIP using Cisco devices and Videoconferencing using Polycom Codecs. As of July 2017 when Rubin Observatory decided to purchase its own collaboration platform the project did not have any kind of VoIP solution in place and all support was provided by CISS, including extension numbers. CISS solution was based in Cisco's Call Manager Express (CME) using ISR routers in different models, setups and versions, and the coverage was only limited to where (physically) CISS could get a port in the voice network (VLAN) they were using back then.

This solution worked well for several years but Rubin Observatory decided to implement its own collaboration platform and the 800s (800 to 899) extension numbers were already negotiated years before by Ron Lambert with Telefonica.

As the Rubin Observatory telescope building and the new base facilities are ready, the following summary requirements must be fulfilled by the VoIP infrastructure:

1. The solution shall use standard signaling protocols such as SIP, and media protocols such as RTP/SRTP. Proprietary signaling and data transmission is not acceptable.
2. The platform must support both physical phones and virtual phones (softphone) working with the standard IPv4/IPv6 protocols.
3. The ecosystem must be rich enough to provide models for diverse uses: offices, outdoors, receptionists, contact center, wireless phones, etc... plus a rich ecosystem of accessories such as wireless and wired headsets, extension pads, etc...
4. The solution must allow for third party phones to be registered using SIP signaling and shall allow H323 and SIP trunking to systems from other vendors.
5. The solution shall provide a method for redundancy and/or high-availability of the call managers, and if feasible mobility of the services from the primary to the secondary system, and vice versa, this at the service layer or any upper hardware/software abstraction layer available.
6. The solution must provide modularity and scalability options for their services, and allow for additional services such as videoconferencing, mailbox, paging, IVR and instant messaging if needed, provided the right licenses.

Chosen solution
---------------
The decision rationale was a technical analysis of the project requirements by several vendors and distributors held in the 2015/2016 timeframe by the Tiger Team, out of which Cisco Systems was the chosen vendor for all the LAN network infrastructure, including VoIP, from which its Cisco Unified Communications Manager (CUCM) and Cisco IP phones, packaged in a pre-deployed solution branded as Cisco Business Edition 6000 (BE6K) running on a VMware ESXi hypervisor, was the specific technical solution chosen for the project.

On a high-level, the Cisco Business Edition 6000 (BE6K) solution provides pre-deployed virtual machines for Cisco Unified Communications Manager (CUCM), IM and Presence Service, Cisco Unity Connection (CUC), Cisco Prime Collaboration Provisioning and Cisco Paging Server, out of which CUCM is the main VoIP system that will enable the IP phones to register and make voice calls between them. The CUCM service handles signaling for calling to the outside using direct SIP or H323 trunks, or using additional services such as Cisco Unified Border Element (CUBE) which is a dedicated voice gateway that bridges separate VoIP networks, which for our use-case would be the rest of the AURA projects and the PSTN. Videoconferencing is also supported by the BE6K using the Cisco Telepresence Server virtual machine, but as the project already uses BlueJeans as its standard videoconferencing software, no efforts will be made to enable videoconferencing using the BE6K, and any Cisco videoconferencing system will only be used to connect to the BlueJeans rooms provided by their cloud Multi Control Unit (MCU) system.

The specific device models are the following:

* **Cisco Business Edition 6000-M:** Main collaboration hypervisor, based on a Cisco UCS 220 M4 server and a specific CPU/RAM/HDD setup to support all the pre-loaded virtual machines with up to 1000 users in total.

.. figure:: /_static/be6k.JPG
    :name: BE6K
    :width: 400 px

* **Cisco ISR 4321:** Voice gateway to implement the Cisco CUBE services.

.. figure:: /_static/isr4321.jpg
    :name: CUBE
    :width: 400 px
    
* **Cisco IP Phone 7811:** Basic single line phone with monochromatic display. To be used indoors inside a NEMA IP67 enclosure for additional protection (i.e. industrial, enclosed and exposed areas)

.. figure:: /_static/7811.jpg
    :name: 7811
    :width: 400 px

* **Cisco IP Phone 8821:** Advanced multi-line wireless phone with color display. To be used indoors for the azimuth level of the main telescope and the auxiliary telescope platforms.

.. figure:: /_static/8821.jpg
    :name: 8821
    :width: 400 px
    
* **Cisco IP Phone 8841:** Advanced multi-line phone with color display. To be used indoors for most of the staff, on desks or on wall-mounts. (e.g. office areas, industrial but enclosed areas, etc..).

.. figure:: /_static/8841.jpg
    :name: 8841
    :width: 400 px
    
* **Cisco IP phone 8845:** Advanced multi-line phone with color display and integrated camera. To be used indoors for the summit and managerial staff. (i.e. office areas).

.. figure:: /_static/8845.jpg
    :name: 8845
    :width: 400 px
    
* **Cisco IP phone 8865 + KEM:** Advanced multi-line phone with color display, integrated camera, and Key Expansion Module (KEM). To be used indoors for the base admin staff.

.. figure:: /_static/8865.JPG
    :name: 8865
    :width: 400 px
    
* **Cisco Telepresence SX10:** all-in-one videoconference unit for small spaces. To be used indoors for small meeting rooms or manager offices.

.. figure:: /_static/sx10.jpg
    :name: SX10
    :width: 400 px
    
* **Cisco IP phone 7962G:** Basic multi-line phone with monochromatic display. To be used indoors for temporary installations or for spares.*

.. figure:: /_static/7962.jpg
    :name: 7962
    :width: 400 px
    
*These phones were purchased as refurbished.

Proposed Network Architecture
=============================

Logical Design
--------------

.. figure:: /_static/VoIP-phydiag%20-HLD-Logical.jpg
    :name: Logical Design
    :width: 800 px

Physical Design
---------------

.. figure:: /_static/VoIP-phydiag%20-HLD-Physical.jpg
    :name: Physical Design
    :width: 800 px

IP-to-IP gateways - Expressway Core and Edge (optional)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

As shown in the diagram above, there are 2 optional services the project may be interested in the future, these are Expressway Core (expressway-c) and Expressway Edge (expressway-e). These are IP-to-IP gateways offering encrypted traversal firewalling for SIP and RTP traffic, from the outside of the network to our internal collaboration services, mainly CUCM. The use-cases for these 2 services -which could be implemented as virtual machines for convenience- are many, but the best one for the project is to use softphones (Cisco Jabber) from outside the network without the need for a VPN connection.

.. figure:: /_static/expressway.JPG
    :name: Expressway
    :width: 800 px

Scalability
-----------

Scalability is cost-effectively achieved by increasing the number of call processing engines (CUCM) as virtual machines. This can be done within the local BE6K hypervisor using the resources of the pre-deployed virtual machines that won't be used or in any other VMware ESXi hypervisor. Mobility between the BE6K and other ESXi hypervisors is also feasible (VMotion or manual cloning) as the pre-deployed virtual machines are VMware standard (.vmdk).

When adding more capacity to the system management is a factor to consider and therefore a hierarchical configuration replication schema must be implemented, we will expand on that in section 3.5, but scalability in CUCM is achieved using clusters, with the maximum number of end-points depending on how many extra call processing engines are added, its hardware resources, and the number of end-points registered with each of them. If we need to scale that much, we may end up with a summit and a base cluster.

.. figure:: /_static/cluster.JPG
    :name: Cluster
    :width: 800 px

Design Considerations
---------------------

* The access to the PSTN as shown in section 3.5 depends on CTIO CISS for cost-effectiveness as of mid-2019. The current volume call from the time CTIO CISS provided the VoIP service to the project has not increased to the point to collapse the available TDM channels of such trunk, therefore having our own E1 access is feasible but not cost-effective yet. This can be done in the future as the Cisco CUBE gateway is equipped with the proper E1 module already.
* Considering the point above, the CUBE gateway must point its dial-peers and external call patterns to the CTIO CISS Call Manager and vice-versa. Telefonica provides access to the +565122058XX range over this E1 trunk.
* The NOIR Lab operations group does bill the external PSTN calls made by the project extensions using the already existing billing setup and software implemented by CTIO CISS in the past. The charge accounts for that billing were provided by our local admin manager Carol Chirino to Patricia Valencia.
* Our 8xx extension range is limited, being 800 reserved for IVR purposes, and 801 to 899 the usable range. following the same approach CTIO CISS had for dial planning (i.e. each person had a dedicated extension number in the base and the summit) would starve the range quickly -if not immediately- as we only have a 99 usable numbers. Therefore the feature "Extension Mobility" must be implemented, which deploys IP phones with base extensions in a non-routable range, and then users are able to log into the phones with their assigned 8XX extension, regardless of the site, their extension moves with them.
* Users must be instructed how to properly use "Extension Mobility" and a transition period of 3 months shall be given to each user to migrate their old extension number (provided by CTIO CISS) to the new 8XX extension. This was determined as a 3-month period along with Carol Chirino and Chris Morrison from NOIR Lab IT. The start of that period must be defined and broadly announced by Carol Chirino to the local staff.
* All IP phones and Key Extension Modules must be powered by Power Over Ethernet (PoE) from the closest local switch. The use of AC/DC 48v power supplies is reserved contingency uses and/or places where no PoE is available due to a defective and/or old switch, or reduced port density in the area. The use of PoE plus a power supply is prohibited as it could damage the device.
* IP phones must be identified in CUCM by their mac-addresses and an inventory must be kept. Pre-deploying spare IP phones is acceptable and good practice to speed up service delivery by the IT service desk agents.
* IP phones must be configured to mark their traffic at the origin with DSCP EF and if not available, the closest switch must mark the voice traffic on its behalf. The maximum acceptable parameters for audio quality is 150ms one way, 300ms round-trip, with up to 30ms of jitter. This must be warranted internally using QoS matching the EF marked packets to the priority queues of each switch in the path. These parameters cannot be warranted outside our network and that's acceptable, as it's outside our administrative control.
* If from the CUCM to the PSTN gateway (CTIO CISS CME) the above-mentioned parameters are okay but still audio quality issues are detected with landline and mobile calls, Telefonica must be contacted for support through the VoIP responsible at NOIR Lab.
* Call tiers must be implemented for controlled access to VoIP resources. Each extension and/or phone must be assigned one of these Tiers depending on the use-case. Observe caution on the assignation of tiers 2 and 3, as mobile call rates may be high and the project is billed for each PSTN call. Ask for advice from the admins team if needed.
 * Tier 0: Only internal calls.
 * Tier 1: Internal and landline calls.
 * Tier 2: Internal, landline, and mobile calls
 * Tier 3: All calls.

Redundancy and High-Availability
--------------------------------

As briefly mentioned in section 3.3, redundancy is achieved by adding more call processing units to the network as virtual machines in this case. Redundancy can be implemented as 2:1, meaning for every 2 active call processing units there is at least 1 backup unit or 1:1, meaning for every active call processing unit there is 1 backup one; we will implement the latter of those, with one unit at the base and the other at the summit as shown in the diagram in section 3.2.

.. figure:: /_static/redundancy.JPG
    :name: Redundancy
    :width: 400 px

These redundancy schemes are facilitated by the built-in registration failover mechanism within the CUCM cluster architecture, which enables endpoints to re-register to a backup call processing subscriber node when the endpoint's primary call processing subscriber node fails. The registration failover mechanism can achieve failover rates for Skinny Client Control Protocol (SCCP) IP phones of approximately 125 registrations per second. The registration failover rate for Session Initiation Protocol (SIP) phones is approximately 40 registrations per second.

High-availability must consider several factors to be effective and complementary to the 1:1 redundancy scheme:

* Each BE6K hypervisor must have redundant power supplies connected to circuits feed on different sources and at least one of them backed up by a UPS.
* Each BE6K must be connected with redundant network links to the local switch. If the hypervisor license allows for it, this can be an LACP bonded channel, otherwise, it must be an active/standby setup.

The call processing units are highly available as the service is always running for the phones registered to it, therefore the voice service is always available in a 1:1 redundancy scheme but while downtime and loss of services to devices can be minimized with this scheme, there will still be some period of time in which call processing services are not available to some or all users upon failure since the phones must register with the backup unit.

.. figure:: /_static/cucmHA.png
    :name: CUCM HA
    :width: 400 px

The clustering implemented by the 1:1 redundancy scheme is implemented by a hierarchical node configuration, where the main node has the main database configuration (known as the PUBLISHER) and a secondary node(s) that replicate the database (know as the SUBSCRIBER). Both PUBLISHER and SUBSCRIBER are active and highly available call processing nodes but configuration changes are only applied on the PUBLISHER. Once a node is set up as SUBSCRIBER it can't be moved to PUBLISHER without resetting the node, please be mindful of this when implementing the services.

Security
--------

The chosen solution supports a broad range of security mechanisms for each of its provided services but for the sake of simplicity, these are the baseline security requirements to be considered for a low-level design.

* IP Phones, just like any other wired device in the network, must be authenticated. Rogue IP phones registering with any of the active CUCM nodes is not acceptable.
* RTP is considered safe within our perimeter while contained in the voice VLAN, but if SRTP can be enabled for SIP signaled calls using TLS, it is advisable to do so. The same principle applies to SIP trunks.
* Each user must be assigned a username/password/Pincode to use "Extension Mobility" and the "Self-Service Portal". These passwords must be unique and users encouraged to change the initial passwords after the IT service desk delivers the phone or enables the features for any given extension.
* End-user credentials SHALL NOT have administration or even read-only rights into the CUCM nodes or any of its services.
* Authentication for the CUCM administration interface must be done via AD LDAP if feasible and avoid using local administrator accounts. AD LDAP may enforce special AD groups to allow this access.
* If Expressway EDGE and CORE are implemented, TLS must be enforced between these services for signaling and media traffic.
* If SIP trunks to Tucson cannot be encrypted using TLS, then such traffic must be encrypted in transit at the edge of our network crossing an IPsec or equivalent VPN tunnel.
* CUCM services SHALL NOT be exposed to other AURA call managers directly punching holes in the firewall for signaling and media, even if IP reachability is available for the end-points between the projects. Any call signaling and media traffic must go through the Cisco CUBE gateway when leaving our network, either for other AURA projects or towards the PSTN.
* IP Phones implement local SSH services. The default SSH credentials must be replaced by a strong local credential in every IP phone deployed, no exceptions allowed. If the SSH service is not needed and can be disabled on the phones, please do so.

Monitoring
----------

CUCM and its services implement local logs and monitoring for relevant events that can be queried via the web interfaces of each service. An SNMP service can also query the services using the Cisco-provided MIBs for basic hardware parameters such as CPU, RAM and HDD load, plus additional parameters such as extension numbers, phone models, etc... depending on the level of granularity needed by the System Administrator in conjunction with the Network Engineer.

Logging is done via Syslog at debugging level to the local Syslog collector of the site and alerts may be configured for specific messages such as phones down, hardware issues, etc... as a backup of the SNMP monitoring.

The IP phones don't need to be monitored directly via SNMP or send Syslog to the collector as that's managed by CUCM, but simple ICMP monitoring to its local IP address can be an option if needed, however, this requires DHCP reservations for each IP phone to always get the same IP address, which adds overhead to the network administrator duties. There's a trade-off in this approach given the configuration and administration overhead versus the slightly faster notifications we can get with ICMP over SNMP or even Syslog to trigger an alert.

Custom queries using AXL requests to the CUCM are also acceptable given the use-case, as well as the use of software abstraction layers that can query the units via SSH and present that information to other services or databases.

Management
----------

Several types of management are available for the chosen solution but the following must be implemented as the baseline:

In-band management access to the CUCM and its services must be based on TACACS+ provided by a Cisco ISE cluster, synchronized with the local domain controller of the site, and implementing differentiated levels of access if feasible. For IT network administrators, the regular domain account shall provide read-only access and the admin domain account shall provide full access to the controller.
IPMI/BMC access is provided by the onboard Cisco CIMC hardware, also placed in a different and more protected network than in-band management. It may or not be in the same as the OOB management segment.
The IP Phones must be pushed with a specific local account for direct SSH access if needed. It's advisable that this account is local to the controller and not a domain account, to avoid lock-down scenarios when multiple network failures occur (e.g. the Radius servers not being reachable over the network).

Appendix
========
Terminology and acronyms
------------------------

.. include:: acronyms.rst 
.. .. rubric:: References

.. Make in-text citations with: :cite:`bibkey`.

.. .. bibliography:: local.bib lsstbib/books.bib lsstbib/lsst.bib lsstbib/lsst-dm.bib lsstbib/refs.bib lsstbib/refs_ads.bib
..    :style: lsst_aa
