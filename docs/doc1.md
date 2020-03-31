---
id: doc1
title: How to Use This Playbook
sidebar_label: How to Use This Playbook
---

This document is a compilation of configuration notes and techniques used for setting up the Veeva Network-CRM Bridge integration. This describes what the “standard bridge” configuration would entail; some variance is expected for any given customer implementation because not all features are relevant to all customers.

Diligent design focus and thorough testing are mandatory for any bridge configuration. If you do not understand WHY you are making a configuration change, then go do the research.

It is possible for there to be an error in this document.  Or for a product feature to change and a corresponding update to be missed herein.  Always test your configuration.  If you find a problem in this document, make the update.


## Understanding the Bridge

The Network-CRM Bridge, aka “the bridge,” is the mechanism that manages data flow between Veeva Network and Veeva CRM.  New and updated records from OpenData (VOD) flow over the bridge into CRM via Network.  Data Change Requests from CRM flow back through Network to OpenData for resolution by data stewards who confirm the changes and either accept or reject the data change request. Records that are mastered by Veeva OpenData are referred to as “orange” records, whereas records that are in CRM not mastered by VOD are referred to as “gray” records.

## A Note on Governance

A Customer Master application is used to facilitate matching multiple sources of data and enforcing governance policies on that data. The Customer Master produces the official version of the Customer Universe, commonly referred to as the golden record.  In the Bridge context, Veeva Network is a customer master application.

Implementing the bridge is synonymous with placing the customer data (Profile, Location, Licensure, Relationships) from the CRM application under governance control of the Customer Master.  That means that the Master is the only source for customer data (i.e. records and fields which are under master control), and changes to any master data must be requested and processed by the Master.  Veeva CRM supports DCR processing for both Veeva Network and a 3rd Party Customer Master.

In practice, Customer Master governance controls the core Customer Profile (name, address, hierarchy, etc.).  There will be additional attributes in CRM that are CRM-specific; the Master controls the common Customer fields for all systems in the environment, and local systems can maintain supplemental data attributes which are needed only in that system.  E.g. Corporate Name and Address are mastered data attributes, whereas preferred call address and targeting flags would be CRM-specific, as would be all the transactional data (Calls, Events, etc.).  

If the mastered data in CRM are updated independently of the Master, then the client loses their consensus view of their universe, and new problems will arise related to data quality and accuracy.  The Data Change Request functionality is the mechanism for capturing changes to mastered attributes and feeding them into the change verification process.  Note: the DCR mechanism controls at the UI/Business layer of Veeva CRM, NOT the data layer.  It is possible to update mastered fields via SFDC API call or Data Loader job without triggering a DCR.  This will lead to bad user experience and confusion; do not allow this.

Some clients use their CRM salesforce.com platform for additional, non-Veeva CRM, applications which will share common data objects.  E.g. Service Cloud, SAP order process, etc.  In these situations, the applications may not have been designed to work with master governance process. These conflicts can be complex and need thorough design analysis.  Master governance is applied by RecordType; if the competing CRM applications do not share a common data RecordType, then the risk is mitigated.

When planning a Bridge project, you need to revisit the CRM and Integration design with this understanding of Master governance. The Bridge “controls” master data on the Account, Address_vod, and Child_Account_vod objects. 

## Additional Resources

Network Services did a webinar in 2016 to outline the bridge processes and approach for implementation.  If you are unfamiliar with the bridge, start here.  The webinar materials are available on Share at:

[Presentation](https://veeva.veevashare.com/0314269f99c00000)

[Recording with Commentary](https://veeva.veevashare.com/031268e529c00000/)

The CRM product manual for Bridge configuration is a good source of information.  If you have a question that isn’t covered by the manual, look in Share, and/or use the network_help@veeva.com mailing list.

This document describes the technical steps needed for setting up the bridge.  There is another entire conversation needed with the customer regarding Governance and CRM process changes.  That is not in scope here.