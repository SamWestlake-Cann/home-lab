# Ticket 002 — User Unable to Access Folder

| Field | Detail |
|---|---|
| **Title** | User unable to access folder — permission denied |
| **Reported by** | test_user |
| **Device** | SAM-LAB-CLIENT01 |
| **Date** | 01/06/2026 |
| **Priority** | Medium |
| **Category** | Access & Permissions |
| **Status** | Resolved |

## Description
User contacted the help desk reporting they were unable to access a 
folder required to complete their work. Error message displayed: 
"You don't currently have permission to access this folder."

## Investigation
User was asked when they last successfully accessed the folder and 
whether anything had changed on their machine recently. User 
credentials were verified to confirm the account was active and in 
good standing. NTFS permissions on the folder were reviewed using 
icacls — confirmed the user account had not been granted access to 
the requested folder.

## Resolution
Appropriate NTFS permissions were applied to the folder for the user 
account via icacls. User confirmed they were able to access the folder 
and complete their work successfully.

## Evidence
- Screenshot: 11_test_user_admin_only_denied.png
- Screenshot: 10_all_folder_permissions_verified.png
