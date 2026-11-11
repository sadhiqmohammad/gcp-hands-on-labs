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

## Review questions

- Name the four levels of the resource hierarchy, from the bottom up.
- At which levels can policies be defined, and which way are they inherited?
- How many projects can a resource belong to?
- Compare the project ID, project name and project number. Which one is immutable?
- What can the Resource Manager API do with projects?
- Why put shared policies on a folder instead of on each project?
- Which two special roles at the organization node does the course name, and what does each control?
- How is an organization node created with and without Google Workspace?
