Google Cloud’s resource hierarchy contains four levels, and starting from the bottom up they are: resources, projects, folders, and an organization node.

Organizational Node
   |
Folder---------------
   |                        |
Project            Project
   |                         |
Resources     Resources


**IAM (Identity and Access Management)**
With IAM, administrators can apply policies that define who can do what and on which resources.

**Types of Roles**
*1) Basic Roles **(Broadest)** : Owner, Editor, Viewer and Billing Admin*
*2) Predefined Roles: Predefined roles give granular access to specific Google Cloud resources. These roles are created and maintained by Google. Google automatically updates their permissions as necessary, such as when Google Cloud adds new features or services.*
*3) Custom Roles **(Fine grained)**: provide granular access according to a user-specified list of permissions.*
