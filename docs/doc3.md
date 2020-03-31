---
id: doc3
title: Initial Configuration of CRM for Network
sidebar_label: Initial Configuration of CRM for Network
---

## Custom Settings

There are several Custom Settings which control bridge behavior. Review each of these settings and set appropriately per your solution design. Review the CRM Manual if more information is needed on these settings. If you have not had a requirements gathering yet, use the recommended starting point, and modify the data change request mode and network customer mode as necessary to fit client needs.  

## Network Settings 

Step 1: Navigate to Setup > Custom Settings > Network Settings > Manage

For each of the following network settings, set the value to appropriately in accordance with requirements gathered. Use the recommended configuration if requirements are not gathered yet (below).


|    Scenario                                                                           |    Data Change Request Mode (Veeva Settings)    |    Network Customer Master Mode (Network Settings)    |
|---------------------------------------------------------------------------------------|-------------------------------------------------|-------------------------------------------------------|
|    Standard   CRM (Network not Implemented)                                           |    0                                            |    0                                                  |
|    NAS   Enabled, No DCRs                                                             |    0                                            |    1                                                  |
|    Disconnected   DCR                                                                 |    1                                            |    0                                                  |
|    Network   Integration for Search & DCR   **Recommendation for Getting Started**    |    1                                            |    1                                                  |
|    Network   Integration with Create Unverified                                       |    2                                            |    1                                                  |


![Network Settings](/VeevaNetworkPlaybook/img/doc3_001.png)


## Veeva Settings 

For each of the following Veeva settings, set the value to appropriately in accordance with requirements gathered. Use the recommended configuration if requirements are not gathered yet (below).

Step 2: Navigate to Setup > Custom Settings > Veeva Settings > Manage


| Veeva Settings           | Recommendation for Getting Started | Possible Values       |
|--------------------------|------------------------------------|-----------------------|
| Data Change Request Mode | 1                                  | 0-2 (See Above Table) |
| DCR Alert Message Days   | 7                                  | Varies                |
| DISABLE_MASS_UPDATE      | TRUE                               | TRUE/FALSE            |


##  Network Field Creation and Verification

It can be rather overwhelming for a customer to ask them for a list of all fields on Network / OpenData Mastered objects that they wish to be DCR-enabled, thus start with a core set of Network and commonly mastered fields (table below). During the Alpha, show the customer the fields that are DCR-enabled and let them provide feedback from there. This streamlines the process by giving the customer a baseline of DCR-enabled fields to work from.

Before any additional configuration can really begin, it is necessary to have all the appropriate components – namely, the appropriate fields. Navigate to each of the following objects and ensure the list of fields below exist in your environment. Note, Veeva Network / OpenData only masters the following objects and associated data in CRM: Account_vod__c, Child_Account_vod__c, and Address_vod__c.

| Object                                     | CRM Label                    | CRM API                         | Data Type / Details                         |
|--------------------------------------------|------------------------------|---------------------------------|---------------------------------------------|
| Account                                    | Network External ID          | Network_External_ID__c          | Text (18), Unique, External Id, Insensitive |
|                                            | Veeva Mastered               | Network_Is_Veeva_Master__c      | Checkbox (Unchecked)                        |
|                                            | Email                        | Network_Email_1__c              | Email                                       |
|                                            | Specialty*                   | Specialty_1_vod__c              | Picklist                                    |
|                                            | Specialty 2*                 | Specialty_2_vod__c              | Picklist                                    |
|                                            | Group Specialty*             | Group_Specialty_1_vod__c        | Picklist                                    |
|                                            | Group Specialty 2*           | Group_Specialty_2_vod__c        | Picklist                                    |
|                                            | Major Class of Trade*        | Network_Major_Class_of_Trade__c | Picklist                                    |
|                                            | Status*                      | Network_Account_Status__c       | Picklist                                    |
|                                            | Credentials*                 | Network_Credentials__c          | Picklist                                    |
|                                            | Primary Country**            | Network_Primary_Country__c      | Picklist                                    |
| Address                                    | Network External ID          | Network_External_ID__c          | Text (18), Unique, External Id, Insensitive |
|                                            | Veeva Mastered               | Network_Is_Veeva_Master__c      | Checkbox (Unchecked)                        |
|                                            | Network Address Rank         | Network_Address_Ordinal__c      | Number (3,0)                                |
|                                            | Network Address Type*        | Network_Address_Type__c         | Picklist                                    |
|                                            | Network Address Status*      | Network_Address_Status__c       | Picklist                                    |
|                                            | Network Postal Code          | Network_Postal_Code__c          | Text(20)                                    |
| Child Account                              | Network External ID          | Network_External_ID__c          | Text (18), Unique, External Id, Insensitive |
|                                            | Veeva Mastered               | Network_Is_Veeva_Master__c      | Checkbox (Unchecked)                        |
|                                            | Network Relationship Status* | Network_Relationship_Status__c  | Picklist                                    |
|                                            | Network Relationship Type*   | Network_Relationship_Type__c    | Picklist                                    |

*List of Values from Network for the given field must exist in the corresponding CRM field

**Ensure two characters codes are used (Example: US and CA, versus United States and Canada)


For each field that does not exist, create a unique network field. Make sure to utilize the same label and API as above. For custom made fields to support Network, do not prefix the API name with the company’s unique identifier, alternatively place the word ‘Network’ in front as shown in the table above.


## Picklist Value Changes and Verification

Later on, when reference mapping you will map all picklist values in CRM via a unique yet standard network code to a corresponding Veeva Network value. For example, for Specialty 1, you will map the potential specialty value of Oncology to Oncology in Network. Thus, every “reference” value in Network must be in CRM for the given field. 

For each of the fields in the table above where the data type is picklist, identify the given list of values (LOVs) in Network and update the available list of values for the field in CRM so both Network and CRM have the LOVs for the given field.

 If not already available, reach out to either your project manager, solution architect, or configuration specialist working on the Network side of the project and ask to receive the reference values for the picklist fields below

Each country could have a different list of values for a given field, if there is more than one country utilizing the same org, pull both lists of values for each country, compare, and load all unique values to the field. Use field dependencies to restrict access to country specific values by making the Primary Country field created earlier a controlling field, and your restricted field dependent. For example:


| Network_Primary_Country_c: | US                     | CA                     |
|----------------------------|------------------------|------------------------|
| Specialty_1_vod__c:        | Allergy                | Allergy                |
|                            | Hepatology             | Hepatology             |
|                            | Naprapathy             | Naprapathy             |
|                            | Advanced Heart Failure | Advanced Heart Failure |

In the example above, all specialty values (US and CA) were loaded to the same field. Because of field dependencies, we don’t have to have a unique specialty field just for Canada to hold values like ‘Advanced Heart Failure’ that the US might not have. Instead, we simply restrict country-unique value based on the primary country field we created earlier.

Now that all necessary fields are created in Veeva CRM, it is essential that both the integration user and field-user have the appropriate levels of access to those fields as well as various objects, tabs and VisualForce pages.


## Updating Organizational-Wide Settings

These CRM settings must be disabled when Network governance is imposed. Changes to Network-controlled data outside of the DCR process will create many problems. If a change occurs outside of DCR, the CRM and Master data are out of sync.  The user will not be aware of this and may make erroneous decisions as a result.

### Disabling Inline Editing 

Step 1: Navigate to Setup > Customize > User Interface
Step 2: Uncheck ‘Enable Inline Editing’ and Click ‘Save’ at the bottom of the page

![Inline Editing](/VeevaNetworkPlaybook/img/doc3_002.png)


### Disabling CRM Address Inheritance

Step 1: Navigate All Tabs > Veeva Messages
Step 2: Search and find the Veeva Message: ACCT_HIER_ADDR_COPY_FIELDS
Step 3: Inactivate the Veeva Message
Step 4: Clone the Veeva Message, and append the “External ID’ field with a unique abbreviation
Step 5: Modify the text in the Veeva Message to include the following:

`Id,Name,City_vod__c,Address_line_2_vod__c,State_vod__c, Network_Postal_Code__c`

Step 6: Check the ‘Active’ Checkbox and Click ‘Save.’

![ACCT_HIER_ADDR_COPY_FIELDS](/VeevaNetworkPlaybook/img/doc3_003.png)

What is being done here is the original Veeva Message is being deprecated and preserved for future CRM releases, while the cloned Veeva Message is being modified to support the new Network implementation (it now supports the custom postal code field).

## Custom Tab Creation
Later, when working on the mapping of data between CRM and Network, it will be necessary to have several tabs available. Create the followings tabs for later use.

| Name                    | Tab Type    | Default Visibility                | Custom Apps |
|-------------------------|-------------|-----------------------------------|-------------|
| DCR Field Type          | Object      | All Profiles (Tab Hidden)   <br/> System Administrator (Default On)     | Veeva CRM   |
| Data Change Request     | Object      | All Profiles (Default Off)  <br/> System Administrator (Default On)     | Veeva CRM   |
| Network Admin           | VisualForce | All Profiles (Tab Hidden)   <br/> System Administrator (Default On)     | Veeva CRM   |
| Veeva Process Scheduler | VisualForce | All Profiles (Tab Hidden)   <br/> System Administrator (Default On)     | Veeva CRM   |

For instructions/specifics on how to create tabs in Veeva CRM, click [here](https://help.salesforce.com/articleView?id=dev_tabdef.htm&type=5).

## Permission Set Creation

For the purposes of a Veeva Network-CRM integration, it is best practice to hold all field-level security, object-level security, VisualForce page access, record type access, and tab access in Network permission sets. For a Network-CRM project, it is necessary to have two Network permission sets: one for the “integration user” and an additional permission set for the field user (the user who will be submitting DCRs). It is necessary to have two separate permission sets because the integration user and field user require different levels of permission in the system.

Begin by navigating to Setup > Manage Users > Permission Sets

On the list of permission sets, review the environment to identify if Network Permission sets pre-exist. Newer orgs will come out-of-the-box with Network Permission Sets available, however some older environments may not. If your org does not have Network Permission sets available, create the permission sets from scratch. If there are pre-existing Network permission sets, it is Veeva best practice to clone the out-of-the box permission set and a unique company identifier as the suffix in the permission set name. Both steps are outlined below


### Scenario 1: Out-of-the-box Permission Sets Exist

Step 1: For each permission set, click the ‘clone’ button.

![Clone Permission Set](/VeevaNetworkPlaybook/img/doc3_004.png)

Step 2: Ensure both the Field Label and API name are appended with the company’s unique identifier - commonly the company’s stock ticker if the company is public. Click ‘Save.’

![Rename Permission Set](/VeevaNetworkPlaybook/img/doc3_005.png)

### Scenario 2: Out-of-the-box Permission Sets Do Not Exist

Step 1: From the list of permission sets, click the ‘New’ button.

![Create New Permission Set](/VeevaNetworkPlaybook/img/doc3_006.png)

Step 2: Ensure both the Field Label and API name are appended with the company’s unique identifier - commonly the company’s stock ticker if the company is public. Do not specify a license type for the permission set. Click ‘Save.’

![Name New Permission Set](/VeevaNetworkPlaybook/img/doc3_007.png)

### Setting the Appropriate Permissions

Now that the appropriate fields and tabs have been created, appropriate access to those fields, tabs, objects, record types, and VisualForce pages must be given for both the field user and the integration user. Use the permission matrix below to set the following permissions at the profile and permission set level for both the field users and integration users.

The integration permission set needs to provide edit FLS to all fields that are being updated in the bridge. The matrix is just a starting point. As you go through the requirements gathering there may be more OpenData and custom fields that the customer wants to manage in Network. 


### Network CRM Permissions Matrix

![Permission Set Matrix](/VeevaNetworkPlaybook/img/doc3_008.jpg)

### Account Object FLS

![Permission Set Matrix - Account](/VeevaNetworkPlaybook/img/doc3_009.jpg)

### Other Object FLS

![Permission Set Matrix - Other Objects](/VeevaNetworkPlaybook/img/doc3_010.jpg)

## Child Account Object Modifications 

In order for Network to integrate well with CRM, structural changes must be made to the child_account_vod__c object in CRM.

Step 1: Navigate to Setup > Create > Object > Child_Account_vod__c

Step 2: Find the Parent_Account_vod__c field and click ‘Edit.’

![Edit Parent_Account_vod__c](/VeevaNetworkPlaybook/img/doc3_011.png)

Step 3: Click ‘Change Field Type.’

![Change Field Type](/VeevaNetworkPlaybook/img/doc3_012.png)

Step 4: Change from ‘Master-Detail Relationship’ to ‘Lookup Relationship’

![Update Field Type to Lookup](/VeevaNetworkPlaybook/img/doc3_013.png)

Step 5: Click ‘Next’ and ‘Save.’

## Page Layouts Network Enablement


When implementing Network, it is necessary to replace the Create and Edit buttons with custom made buttons by Veeva. Additionally, edit links must be redirected from the standard edit page by Salesforce so that the data can go through the Veeva DCR process. These buttons and links are provided an override where they instead go to custom DCR VisualForce pages.

If the buttons and links are not redirected to the custom DCR VisualForce pages, then users will be seeing the standard SFDC edit pages.  Any changes they make will be saved to the SFDC object immediately, and the result is the CRM version of the record being out of sync from the Master version.  The DCR functionality would not have been invoked to capture the changes and enforce the change governance.

Some customers choose not to display the Members and/or Member Of related list on their account page layouts.  Typically, this is done when the Customer does not use the Account Hierarchy feature of Veeva CRM.  The bridge integration does use that hierarchy, so these lists must be updated.  If a customer still does not want to display them, it is best practice to make these button changes anyways – if the customer changes their mind in the future, we don’t want an invalid related list configuration in the system.

_For all business and person page layouts actively used, the following changes must be made. Ensure to check all business/person account record types being used and the associate page layouts_

### Business Account Page Layouts

Step 1: Navigate to Setup > Customize > Accounts > Page Layouts
Step 2: Edit the Page Layout
Step 3: In the Standard Buttons section, remove the Edit button, and drag the Edit_DCR_vod custom button into the Custom Buttons section to replace it

![Business Account Page Layout](/VeevaNetworkPlaybook/img/doc3_014.png)
