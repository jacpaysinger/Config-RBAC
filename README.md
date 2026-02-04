# Configuring Role-Based Access Control (RBAC) for Incidents and Asset Tags

## Overview
This repository documents the implementation of role-based access control for the Holographic Handheld Device (HHD) application in ServiceNow. The lab focused on restricting application visibility, securing Incident records with Access Control Lists (ACLs), and enforcing field-level permissions for sensitive asset data.

The configuration ensured that only authorized support teams could access HHD incidents while allowing controlled exceptions for product ownership and asset management.

## Use Case
Infinity, the manufacturer of the HHD, requires that HHD Incident records be visible only to approved support team members and product owners as part of a non-disclosure agreement. Additionally, asset tag values needed to be protected from modification except by a designated asset manager.

This implementation simulated how a ServiceNow administrator enforces application access, table-level security, and field-level restrictions using roles, groups, and ACLs.

## Features
- Application and module access restricted by custom role
- Group-based role assignment for support teams
- Table-level ACLs for Incident read, write, and create operations
- Field-level ACL for Asset Tag updates
- Role-based exception handling for product ownership
- Validation through user impersonation

## Technologies Used
- ServiceNow Platform
- Role-Based Access Control (RBAC)
- Access Control Lists (ACLs)
- Incident Management
- CMDB
- User and Group Management

## Implementation Walkthrough

### Step 1: Restricted HHD Application and Module Access
The existing **u_holographic_handheld_hhd_user** role was verified and applied to the HHD application menu. The default **itil** role was removed to ensure only users explicitly granted the HHD role could view the application.

Each HHD module was updated so visibility was limited exclusively to users with the **u_holographic_handheld_hhd_user** role.

This configuration ensured that general IT users could not access HHD records through navigation or search.

<img width="1898" height="779" alt="Screen Shot 2026-01-30 at 2 47 20 PM" src="https://github.com/user-attachments/assets/4a4e62fb-f225-43fc-af0a-4cc5d9f2d5e7" />

### Step 2: Assigned HHD Role to Support Groups
The **u_holographic_handheld_hhd_user** role was added to the **Service Desk** and **Training Technology Support** groups.

Once assigned, background jobs automatically propagated the role to group members, allowing authorized support users to access HHD functionality without direct role assignment.

<img width="1898" height="473" alt="Screen Shot 2026-01-30 at 3 00 50 PM" src="https://github.com/user-attachments/assets/6fccbf65-82fa-4a66-be1d-1f00f9191390" />

<img width="1898" height="450" alt="Screen Shot 2026-01-30 at 3 01 49 PM" src="https://github.com/user-attachments/assets/3330b3c4-c3b5-4a17-9c1f-8ba5ea1e53e7" />

### Step 3: Validated Application Visibility
User impersonation was used to validate access controls:
- A non-group IT user was unable to view the HHD application or its modules.
- A designated product owner was able to view all HHD modules and create new records successfully.

This confirmed correct enforcement of application-level access rules.

<img width="1898" height="253" alt="Screen Shot 2026-01-30 at 3 18 18 PM" src="https://github.com/user-attachments/assets/19be5988-680f-497a-b759-0a3c200827e2" />

<img width="1898" height="280" alt="Screen Shot 2026-01-30 at 3 19 07 PM" src="https://github.com/user-attachments/assets/9ffcea5d-f03b-46a9-8cee-3eff1170fd73" />
<img width="1898" height="469" alt="Screen Shot 2026-01-30 at 3 20 10 PM" src="https://github.com/user-attachments/assets/ac4fff61-831d-4b4d-ad92-ee7816d7082e" />

### Step 4: Established Incident Table ACLs
With elevated security privileges enabled, three table-level ACLs were created on the **Incident [incident]** table for **read**, **write**, and **create** operations.

Each ACL:
- Required the **u_holographic_handheld_hhd_user** role
- Included a data condition restricting access to records where **Service Offering = Infinity (HHD)**

This ensured HHD incidents were hidden from general Incident lists while remaining accessible to approved users.

<img width="1898" height="217" alt="Screen Shot 2026-01-30 at 3 33 27 PM" src="https://github.com/user-attachments/assets/f824c9b5-f1cf-4e96-b351-0c4f0fb5c507" />

### Step 5: Created Asset Tag Management Role
A new role named **u_hhd_asset_tagger** was created to manage asset tag updates independently of incident access.

This role was designed specifically for field-level write access and was not tied to general HHD application permissions.

<img width="1898" height="656" alt="Screen Shot 2026-01-30 at 3 39 21 PM" src="https://github.com/user-attachments/assets/3a2837cf-6fa4-4d66-a8d3-234e93de1b9f" />

### Step 6: Implemented Field-Level Asset Tag ACL
The existing **write** ACL on the **Holographic Handheld HHD** table was extended to include a field-level rule for the **Asset tag** field.

The ACL required the **u_hhd_asset_tagger** role, ensuring that asset tags could not be modified by standard HHD support users.

<img width="1898" height="282" alt="Screen Shot 2026-01-30 at 3 40 47 PM" src="https://github.com/user-attachments/assets/7dffc38a-4cf6-41c0-9734-e70349566d3e" />

### Step 7: Granted Asset Tag Permissions
A new group named **HHD Asset Taggers** was created and assigned the **u_hhd_asset_tagger** role.

The product owner was added as the sole member of this group, granting exclusive permission to update asset tag values.

<img width="1898" height="462" alt="Screen Shot 2026-01-30 at 3 48 57 PM" src="https://github.com/user-attachments/assets/6d57e15f-d4ce-42ae-b95a-7f6bf9bcfbe1" />
<img width="1898" height="144" alt="Screen Shot 2026-01-30 at 3 49 13 PM" src="https://github.com/user-attachments/assets/1f1ad69e-62f8-4fb2-991b-5d8df7dec541" />

### Step 8: Validated Asset Tag Security
Impersonation testing confirmed:
- Standard HHD users could view but not edit the Asset tag field.
- The designated asset tagger could successfully update the Asset tag field.
- All other HHD functionality remained unaffected.

The elevated security role was removed after validation.

<img width="1898" height="198" alt="Screen Shot 2026-01-30 at 3 52 02 PM" src="https://github.com/user-attachments/assets/fea0a722-01dc-4949-b5ac-c248b185341a" />
<img width="1898" height="198" alt="Screen Shot 2026-01-30 at 3 53 05 PM" src="https://github.com/user-attachments/assets/a34610f5-0485-41dd-9230-8a6e75c7ab4c" />

## Lessons Learned
- Application menus and modules should never rely on default IT roles for sensitive access.
- Group-based role assignment simplifies long-term permission management.
- Table-level ACLs are essential for hiding sensitive records from global lists.
- Field-level ACLs allow precise control over critical data elements without impacting broader access.
- Impersonation testing is critical for validating real-world security behavior.

