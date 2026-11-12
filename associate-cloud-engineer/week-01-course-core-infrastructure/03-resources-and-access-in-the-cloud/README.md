# Module 3: Resources and Access in the Cloud

How Google Cloud is structured, and how that structure controls who can do what.

## Section 3.1 Google Cloud resource hierarchy

Video: [Google Cloud resource hierarchy](https://youtu.be/ecVv1L_TEo8) (5m 28s)

Key concepts:

- **Four levels, from the bottom up:**

  | Level | What it is |
  |---|---|
  | 1. Resources | Virtual machines, Cloud Storage buckets, tables in BigQuery, or anything else in Google Cloud |
  | 2. Projects | Hold resources |
  | 3. Folders | Hold projects, or other folders (subfolders) |
  | 4. Organization node | The top level. Encompasses all the projects, folders and resources in your organization |

- **Why the hierarchy matters:** it directly relates to how policies are managed and applied.
  - Policies can be defined at the project, folder and organization node levels. Some services also allow policies on individual resources.
  - Policies are **inherited downward**. A policy applied to a folder also applies to all the projects in that folder.
- **Projects:** the basis for enabling and using Google Cloud services, such as managing APIs, enabling billing, adding and removing collaborators, and enabling other Google services.
  - Each project is a separate entity under the organization node, and each resource belongs to **exactly one** project.
  - Projects can have different owners and users because they are billed and managed separately.
- **Three identifying attributes of a project:**

  | Attribute | Assigned by | Unique? | Can it change? | Used for |
  |---|---|---|---|---|
  | Project ID | Google | Globally unique | No, immutable after creation | Telling Google Cloud the exact project to work with |
  | Project name | You | No | Yes, at any time | A user-created label |
  | Project number | Google | Unique | Not covered | Mainly used internally by Google Cloud to track resources |

- **Resource Manager:** an API that programmatically helps you manage projects. It can list all projects associated with an account, create, update and delete projects, and recover previously deleted projects. It is accessed through the RPC API and the REST API.
- **Folders:** let you assign policies to resources at the level of granularity you choose.
  - A folder can contain projects, other folders, or both. Resources in a folder inherit the folder's policies and permissions.
  - Use them to group projects in a hierarchy, for example one folder per department.
  - They let teams delegate administrative rights so they can work independently.
  - Example: two projects run by the same team can share policies placed on a common folder. Putting duplicate copies on each project is tedious and error-prone, because any change must be made in two places instead of one.
- **Organization node:** the topmost resource in the hierarchy. You must have one to use folders. Everything else attached to the account (folders, projects, other resources) sits under it, and folders and projects are its "children".
- **Special organization roles:**
  - **Organization policy administrator:** only people with this privilege can change policies.
  - **Project creator:** controls who can create projects, and therefore who can spend money.
- **Creating an organization node:**
  - With a **Google Workspace** domain, Google Cloud projects automatically belong to your organization node.
  - Otherwise, use **Cloud Identity**, Google's identity, access, application and endpoint management platform, to generate one.
  - Once created, anyone in the domain can still create projects and billing accounts, as before.

## Section 3.2 Identity and Access Management (IAM)

Video: [Identity and Access Management (IAM)](https://youtu.be/Di1T4RyO9yg) (4m 22s)

Key concepts:

- **IAM:** lets administrators apply policies that define **who** can do **what** and on **which resources**. It helps restrict who has access to what when an organization node holds lots of folders, projects and resources.
- **Principal (the "who"):** a Google account, a Google group, a service account, or a Cloud Identity domain. Each principal has its own identifier, usually an email address.
- **Role (the "can do what"):** a collection of permissions. Granting a role to a principal grants all the permissions the role contains.
  - Example: managing VM instances in a project needs permissions to create, delete, start, stop and change VMs, so they are grouped into one role that is easier to understand and manage.
- **Inheritance:** a role granted on an element of the resource hierarchy applies to that element and **all the elements below it**.
- **Deny rules:** prevent certain principals from using certain permissions, regardless of the roles they are granted.
  - IAM always checks relevant **deny policies before allow policies**.
  - Deny policies are inherited through the resource hierarchy, like allow policies.
- **Three kinds of roles:**

  | Role type | Scope | Examples |
  |---|---|---|
  | Basic | Broad. On a project, they affect all resources in that project | Owner, editor, viewer, billing administrator |
  | Predefined | Sets of roles offered by specific Google Cloud services, which also define where the roles can be applied | Compute Engine `instanceAdmin`, applied to a project, a folder or an entire organization |
  | Custom | The exact permissions you define | An `instanceOperator` role that can stop and start Compute Engine VMs but not reconfigure them |

- **Basic roles in detail:**

  | Basic role | Can access resources | Can change resources | Extra |
  |---|---|---|---|
  | Viewer | Yes | No | |
  | Editor | Yes | Yes | |
  | Owner | Yes | Yes | Manages the associated roles and permissions, and sets up billing |
  | Billing administrator | Not covered | No | Controls billing for a project without being able to change its resources |

  - **Caution:** if several people work on a project with sensitive data, basic roles are probably too broad.
- **Least privilege:** a model many companies use, where each person gets the minimal amount of privilege needed to do their job. Custom roles support it.
- **Two details before creating custom roles:**
  1. You must manage the permissions that define the custom role. Because of this, some organizations prefer predefined roles.
  2. Custom roles can only be applied at the **project or organization level**, not the folder level.

## Section 3.3 Service accounts

Video: [Service accounts](https://youtu.be/xoo5NfLqePY) (1m 38s)

Key concepts:

- **Service account:** lets you assign specific permissions to a virtual machine, so it can interact with other cloud services without human intervention. No person has to grant access manually each time the program runs.
  - Example: an application in a VM needs to store data in Cloud Storage, and only that VM should have access, not anyone on the internet. Create a service account to authenticate that VM to Cloud Storage.
- **Identity:** service accounts are named with an email address, but they use **cryptographic keys** instead of passwords to access resources.
- **Roles still apply:** if a service account has Compute Engine's Instance Admin role, an application running in a VM with that service account can create, modify and delete other VMs.
- **A service account is also a resource:** besides being an identity, it can have IAM policies of its own attached, just like any other Google Cloud resource.
  - Example: Alice needs to manage which Google accounts can act as service accounts, so she gets the editor role on the service account. Bob only needs to view the list of service accounts, so he gets the viewer role.

## Section 3.4 Cloud Identity

Video: [Cloud Identity](https://youtu.be/EZccX9nFaiI) (1m 17s)

Key concepts:

- **The common starting point:** new customers often log in to the Google Cloud console with a Gmail account and use Google Groups to collaborate with teammates in similar roles.
  - It is easy to start with, but the team's identities are **not centrally managed**.
  - If someone leaves the organization, there is no easy way to immediately remove their access to the team's cloud resources.
- **Cloud Identity:** lets organizations define policies and manage their users and groups using the **Google Admin Console**.
  - Admins can log in and manage Google Cloud resources with the same usernames and passwords they already use in existing **Active Directory or LDAP** systems.
  - When someone leaves, an administrator can use the Google Admin Console to disable their account and remove them from groups.
- **Editions:**

  | Edition | What it adds |
  |---|---|
  | Free | Core Cloud Identity features |
  | Premium | Capabilities to manage mobile devices |

- **Google Workspace customers:** this functionality is already available in the Google Admin Console.

## Review questions

- Name the four levels of the resource hierarchy, from the bottom up.
- At which levels can policies be defined, and which way are they inherited?
- How many projects can a resource belong to?
- Compare the project ID, project name and project number. Which one is immutable?
- What can the Resource Manager API do with projects?
- Why put shared policies on a folder instead of on each project?
- Which two special roles at the organization node does the course name, and what does each control?
- How is an organization node created with and without Google Workspace?
- What are the four kinds of IAM principal, and how is each one usually identified?
- Which does IAM check first, deny policies or allow policies?
- Name the three kinds of IAM role and give an example of each.
- What can a project owner do that a project editor cannot?
- Why might basic roles be a poor fit for a project with sensitive data?
- At which levels can custom roles be applied, and which level is excluded?
- What problem do service accounts solve for a program running in a VM?
- How is a service account named, and what does it use instead of a password?
- Why can a service account have IAM policies of its own attached to it?
- What goes wrong when a team uses Gmail accounts and Google Groups and someone leaves?
- Where do admins manage users and groups with Cloud Identity, and which existing directory systems can they reuse credentials from?
- What does the premium edition of Cloud Identity add over the free edition?
