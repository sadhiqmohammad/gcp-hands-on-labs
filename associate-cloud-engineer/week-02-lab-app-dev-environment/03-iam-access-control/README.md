# Lab 3: Manage Access Control with IAM

Two lab users: Username 1 (Owner) removes Username 2's project Viewer role, then grants it only Storage Object Viewer, and Username 2 lists a bucket from Cloud Shell.

| Lab ID | Direct link |
|---|---|
| GSP064 | [Open in Google Skills](https://www.skills.google/course_templates/637/labs/592543) |

> **ACE exam note:** know the four basic roles (Browser, Viewer, Editor, Owner) and that only Owner can manage IAM and billing. A predefined role such as Storage Object Viewer gives access to one service without the project-wide Viewer role. IAM changes can take up to 80 seconds to apply.

The lab gives you **two** sets of credentials, and each needs its own browser session. The lab says to use a second incognito window, but in Chrome every incognito window shares one session, so the second one is already signed in as Username 1. Instead:

- **Username 1:** a Chrome incognito window.
- **Username 2:** a Chrome **Guest** window (profile icon at the top right > **Guest**) or a private window in another browser, such as Safari (File > New Private Window). Copy the **Open Google Console** link from the lab panel and paste it there. To check which user a tab uses, hover over the avatar. If a tab shows the wrong user, sign out and use **Open Google Console** again.

You need any small `.txt` or `.html` file on your computer for Task 2.

The steps are console steps. The `gcloud` commands run in Cloud Shell **as Username 1** and do the same thing:

```bash
export PROJECT_ID=$(gcloud config get-value project)
export USER2=student-xx-xxxxxxxx@qwiklabs.net   # Username 2 from the Lab Connection panel
export BUCKET=$PROJECT_ID-iam                   # any globally unique name
```

Tasks 2, 3 and 4 have a **Check my progress** step.

## Task 1. Explore the IAM console and project-level roles

**Username 1 tab:** Navigation menu > IAM & Admin > IAM > **Grant access** > **Select a role** > **Basic**. Hover over each role to see its permissions, then click **Cancel**.

| Role | What it allows |
|---|---|
| `roles/browser` | Browse the project hierarchy (folders, organization, IAM policy). Can't see resources inside the project. |
| `roles/viewer` | Read-only actions that don't change state. |
| `roles/editor` | Viewer, plus actions that change existing resources. |
| `roles/owner` | Editor, plus managing roles and permissions and setting up billing. |

Username 1 can manage roles, so it has Owner.

**Username 2 tab:** IAM & Admin > IAM > **View by principals**. Username 2 has **Viewer**, and **Grant access** is disabled because it lacks `resourcemanager.projects.setIamPolicy`.

```bash
gcloud projects get-iam-policy $PROJECT_ID \
  --flatten="bindings[].members" \
  --format="table(bindings.role, bindings.members)"

gcloud iam roles describe roles/viewer
```

## Task 2. Prepare a Cloud Storage bucket for access testing

**Username 1 tab:** Cloud Storage > Buckets > **Create**.

| Property | Value |
|---|---|
| Name | A globally unique name. Note it for Task 4. |
| Location type | Multi-region |

Leave the rest as default and click **Create**. If prompted with "Public access will be prevented", click **Confirm**.

On the bucket details page click **Upload files**, pick your text or HTML file, then use the three-dot menu > **Rename** to name it `sample.txt`.

```bash
gcloud storage buckets create gs://$BUCKET --location=US
echo "IAM lab sample" > sample.txt
gcloud storage cp sample.txt gs://$BUCKET
```

**Username 2 tab:** Cloud Storage > Buckets. Username 2 can see the bucket because Viewer gives read-only access to the whole project.

## Task 3. Remove project access

**Username 1 tab:** IAM & Admin > IAM > **View by principals** > on Username 2's row click **Edit principal** (pencil, scroll right if hidden) > **Delete role** (trash can) next to Viewer > **Save**. Username 2 disappears from the list.

```bash
gcloud projects remove-iam-policy-binding $PROJECT_ID \
  --member=user:$USER2 \
  --role=roles/viewer
```

**Username 2 tab:** Cloud Storage > Buckets. You should now get a permission error. If not, wait 2 minutes and refresh.

## Task 4. Add Cloud Storage permissions

**Username 1 tab:** IAM & Admin > IAM > **Grant access**.

1. **New principals:** Username 2's email address.
2. **Role:** Cloud Storage > **Storage Object Viewer**.
3. **Save**. A 403 or "IAM policy update failed" on a new lab project usually clears if you wait a few minutes and save again.

```bash
gcloud projects add-iam-policy-binding $PROJECT_ID \
  --member=user:$USER2 \
  --role=roles/storage.objectViewer
```

**Username 2 tab:** this user can't browse the project in the console any more, but it can read Cloud Storage objects. Open Cloud Shell and list the bucket:

```bash
gcloud storage ls gs://[YOUR_BUCKET_NAME]
```

Expected:

```text
gs://[YOUR_BUCKET_NAME]/sample.txt
```

## Troubleshooting

| Problem | Fix |
|---|---|
| Second incognito window is already signed in as Username 1, so Username 2 can't sign in | Chrome incognito windows share one session. Sign in Username 2 from a Chrome Guest window or another browser's private window. |
| Permission error creating the bucket | The tab is signed in as Username 2. Sign out, then sign in again with Username 1. |
| Username 2 can still see buckets after Task 3 | IAM changes take up to 80 seconds. Wait 2 minutes and refresh. |
| 403 or "IAM policy update failed" when saving in Task 4 | New lab users take a few minutes to propagate. Wait and save again. |
| `AccessDeniedException` on `gcloud storage ls` in Task 4 | Permissions are still propagating. Wait a minute and rerun. Check that you are in Username 2's Cloud Shell and the bucket name is right. |
| Can't find the pencil icon in Task 3 | Widen the window or scroll the IAM table to the right. |
| Check my progress fails on Task 2 | The object must be named exactly `sample.txt`. |
