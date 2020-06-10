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

.. .. rubric:: References

.. Make in-text citations with: :cite:`bibkey`.

.. .. bibliography:: local.bib lsstbib/books.bib lsstbib/lsst.bib lsstbib/lsst-dm.bib lsstbib/refs.bib lsstbib/refs_ads.bib
..    :style: lsst_aa
