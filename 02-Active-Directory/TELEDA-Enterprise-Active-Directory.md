# TELEDA Enterprise Active Directory

## Departmental Access Control and AGDLP Implementation

## 1. Project Overview

I designed and implemented a departmental access control structure for a fictional company called TELEDA using my VirtualBox Active Directory lab.

The objective was to simulate a realistic business environment where employees belong to different departments and receive access to company resources according to their job responsibilities and roles.

I used Active Directory Organizational Units (OUs), Global security groups, Domain Local security groups, NTFS permissions, and Windows network sharing to implement and test departmental access control.

## 2. Lab Environment

| Component | Configuration |
|---|---|
| Domain | ad.teleda.online |
| NetBIOS Name | AD |
| Domain Controller | Server1 |
| Server Operating System | Windows Server 2025 |
| Client Computer | TELEDA |
| Client Operating System | Windows 11 Pro |
| Domain Controller IP | 192.168.10.10 |
| Client IP | 192.168.10.20 |
| Shared Folder | C:\TELEDA-Shared |

## 3. Organizational Unit (OU) Structure

I created a departmental OU structure in Active Directory to organize employees, computers, and security groups.

ad.teleda.online
└── TELEDA
    ├── IT
    │   ├── Users
    │   ├── Computers
    │   └── Groups
    ├── SOC
    │   ├── Users
    │   ├── Computers
    │   └── Groups
    ├── HR
    │   ├── Users
    │   ├── Computers
    │   └── Groups
    ├── Finance
    │   ├── Users
    │   ├── Computers
    │   └── Groups
    └── Resource-Groups


## 4. Employee Account Creation

I created eight fictional employee accounts across four departments.

I populated their Active Directory profiles with job titles, department details, and reporting managers.

| Employee | Username | Job Title | Department |
|---|---|---|---|
| Chinedu Okafor | cokafor | IT Support Officer | IT |
| Tunde Adeyemi | tadeyemi | Systems Administrator | IT |
| Amina Yusuf | ayusuf | SOC Analyst L1 | SOC |
| Ibrahim Bello | ibello | SOC Lead | SOC |
| Ngozi Eze | neze | HR Officer | HR |
| Funke Adebayo | fadebayo | HR Manager | HR |
| Emeka Nwosu | enwosu | Finance Officer | Finance |
| Mariam Ibrahim | mibrahim | Finance Manager | Finance |

## 5. Global Security Groups

I created Global security groups to organize employees according to their departments and job responsibilities.

### Departmental Groups

| Group Name | Members |
|---|---|
| GG_IT_Staff | Chinedu Okafor, Tunde Adeyemi |
| GG_SOC_Staff | Amina Yusuf, Ibrahim Bello |
| GG_HR_Staff | Ngozi Eze, Funke Adebayo |
| GG_Finance_Staff | Emeka Nwosu, Mariam Ibrahim |

### Role-Based Groups

| Group Name | Member |
|---|---|
| GG_IT_Support | Chinedu Okafor |
| GG_Systems_Admins | Tunde Adeyemi |
| GG_SOC_Analysts | Amina Yusuf |
| GG_SOC_Leads | Ibrahim Bello |
| GG_HR_Officers | Ngozi Eze |
| GG_HR_Managers | Funke Adebayo |
| GG_Finance_Officers | Emeka Nwosu |
| GG_Finance_Managers | Mariam Ibrahim |

All groups were created as Global security groups.

## 6. Domain Local Resource Groups

I created Domain Local security groups to manage access to departmental shared folders.

| Domain Local Group | Global Group Member | Intended Permission |
|---|---|---|
| DL_IT_Folder_Read | GG_IT_Support | Read |
| DL_SOC_Folder_Read | GG_SOC_Analysts | Read |
| DL_HR_Folder_Read | GG_HR_Officers | Read |
| DL_Finance_Folder_Modify | GG_Finance_Officers | Modify |

I used these Domain Local groups to assign permissions to departmental folders instead of assigning permissions directly to individual employee accounts.

## 7. AGDLP Implementation

I used the AGDLP model to organize access permissions in Active Directory.

AGDLP stands for:

- **A – Accounts:** Individual employee accounts.
- **G – Global Groups:** Groups containing employees with similar roles.
- **DL – Domain Local Groups:** Groups used to assign permissions to resources.
- **P – Permissions:** Access rights assigned to the resource.

### Example: SOC Folder Access

The access-control path for Amina Yusuf was:

ayusuf → GG_SOC_Analysts → DL_SOC_Folder_Read → SOC Folder Read Access

This structure allowed me to assign permissions to groups rather than individual employees.

When an employee is added to the appropriate Global group, they receive the resource permissions assigned through the Domain Local group.


## 8. Shared Folder Implementation

On Server1, I created a shared folder structure to represent departmental resources.

The folder structure was:

C:\TELEDA-Shared
├── IT
├── SOC
├── HR
└── Finance


I configured the network share: \\192.168.10.10\TELEDA-Shared

I removed the broad permissions and configured Authenticated Users with Read and Change permissions at the share level, without granting Full Control.

I then used NTFS permissions on the individual departmental folders to control which employees could access each department's resources.

**Share permissions control access through the network share, while NTFS permissions control access to the folders and files themselves. Windows evaluates both when someone accesses a resource over the network.**

## 9. NTFS Permission Configuration

I configured NTFS permissions on each departmental folder using the corresponding Domain Local security groups.

| Department Folder | Security Group | NTFS Permission |
|---|---|---|
| IT | DL_IT_Folder_Read | Read |
| SOC | DL_SOC_Folder_Read | Read |
| HR | DL_HR_Folder_Read | Read |
| Finance | DL_Finance_Folder_Modify | Modify |

I reviewed inherited permissions and removed broad AD\Users access from restricted departmental folders where necessary.

I retained essential administrative and SYSTEM permissions.

The Finance Officer received Modify permission, which allowed document management without granting permission to change folder security settings or take ownership.

## 10. Practical Access-Control Testing

I tested departmental access using four employee accounts, one from each department. The tests confirmed that each employee could access their own departmental folder while access to other departmental folders was denied.

| Employee | Department Folder Access | Other Department Folders | File Modification |
|---|---|---|---|
| Chinedu Okafor | Allowed | Denied | Denied |
| Amina Yusuf | Allowed | Denied | Denied |
| Ngozi Eze | Allowed | Denied | Denied |
| Emeka Nwosu | Allowed | Denied | Allowed |

### SOC Department Test

I signed in using Amina Yusuf's account (AD\ayusuf) and tested access to the shared folders at \\192.168.10.10\TELEDA-Shared.

- Successfully opened the shared folder.
- Accessed the SOC folder.
- Could not access the IT, HR, or Finance folders.
- Attempted to create a text file inside the SOC folder, but Windows denied the action.

### Finance Department Test

I signed in using Emeka Nwosu's account (AD\enwosu) and tested access to the Finance folder.

- Successfully accessed the Finance folder.
- Created, modified, saved, and deleted a test file.
- Attempted to change the Finance folder permissions, but Windows denied the action.

Chinedu Okafor and Ngozi Eze also passed their departmental access tests.

## 11. Security Principles Demonstrated

This project helped me apply several important security principles:

- Least Privilege: Employees received only the permissions required for their job responsibilities.
- Departmental Isolation: Employees were restricted from accessing other departments' folders.
- Role-Based Access Control: Access was managed through security groups based on employee roles.
- Separation of Duties: Employees could manage documents without automatically receiving administrative control over folder permissions.
- Access Verification: I tested permissions using employee accounts to confirm that the access restrictions worked as intended.

## 12. IT Support and SOC Analyst Skills Demonstrated

| IT Support Skills | SOC Analyst Skills |
|---|---|
| Active Directory administration | Access-control investigation |
| User and group management | Least-privilege enforcement |
| NTFS permission configuration | Permission auditing |
| Windows network sharing | Unauthorized access prevention |
| Account troubleshooting | Privilege exposure investigation |
| File access troubleshooting | Security boundary validation |

## 13. Conclusion

I successfully designed, implemented, and tested a departmental access control environment using Active Directory in my lab.

Through this project, I gained practical experience with Organizational Units, employee account management, Global and Domain Local security groups, AGDLP, NTFS permissions, and Windows network sharing.

The access control tests confirmed that employees could access their own departmental resources while being restricted from other departments.

The tests also demonstrated that read-only employees could not modify protected files, while the Finance Officer could manage documents without administrative control over folder permissions.

This project strengthened my understanding of enterprise identity management, Windows access control, and the principle of least privilege.

## Project Evidence (Screenshots)

The following evidence supports the implementation documented in this project:

1. [Active Directory departmental OU structure](../06-Evidence/TELEDA-AD-01-Departmental-OU-Structure.png).

2. [Departmental and role-based Global security groups](../06-Evidence/TELEDA-AD-02-SOC-Global-Security-Groups.png).

3. [Domain Local resource groups and group membership](../06-Evidence/TELEDA-AD-03-Domain-Local-Group-Membership.png).

4. [Shared folder structure on Server1](../06-Evidence/TELEDA-AD-04-Departmental-Shared-Folders.png).

5. NTFS permissions configured on departmental folders:
   - [SOC folder NTFS permissions](../06-Evidence/TELEDA-AD-05-SOC-NTFS-Permissions.png).
   - [Finance folder NTFS permissions](../06-Evidence/TELEDA-AD-07A-Finance-NTFS-Permissions.png).

6. [Practical access-control test showing restricted access](../06-Evidence/TELEDA-AD-06-SOC-Restricted-Access-Test.png).

7. [Finance permission-management access denied](../06-Evidence/TELEDA-AD-08-Finance-Permission-Management-Denied.png).
