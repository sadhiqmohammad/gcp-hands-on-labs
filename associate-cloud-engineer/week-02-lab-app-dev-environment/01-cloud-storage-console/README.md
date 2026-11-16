# Lab 1: Manage Cloud Storage with the Google Cloud Console

A Cloud Storage bucket with an uploaded image, made public to everyone, plus folders created and then deleted.

| Lab ID | Direct link |
|---|---|
| GSP073 | [Open in Google Skills](https://www.skills.google/course_templates/637/labs/592541) |

> **ACE exam note:** bucket names are global and must be unique across all of Google Cloud. To make objects public, grant `allUsers` the **Storage Object Viewer** role on the bucket, which is blocked while public access prevention is enforced. Folders are only name prefixes in a flat namespace.

This is a console lab, so the console path is the main step in each task. The `gcloud storage` commands do the same thing from Cloud Shell, which is useful for the exam and for the CLI lab later on. The lab assigns its own region and project, so use those values. Tasks 1 to 3 have a **Check my progress** step.

```bash
export REGION=us-east1                             # from your lab
export BUCKET=$(gcloud config get-value project)   # the lab suggests the project ID as the bucket name
```

## Task 1. Create a bucket

**Console path:** Navigation menu > Cloud Storage > Buckets > **+ Create**. Click **Continue** after each step.

1. **Name:** your Project ID. It is always unique.
2. **Location type:** Region, and pick the lab's region for **Location**.
3. **Default class:** Standard.
4. **Access control:** Uniform, and clear **Enforce public access prevention on this bucket**.
5. Leave the rest as default and click **Create**. If a "Public access will be prevented" dialog appears, clear the box and click **Confirm**.

```bash
gcloud storage buckets create gs://$BUCKET \
  --location=$REGION \
  --default-storage-class=STANDARD \
  --uniform-bucket-level-access \
  --no-public-access-prevention
```

Bucket naming rules worth knowing: 3 to 63 characters, lowercase letters, numbers, `-`, `_` and `.` only, start and end with a letter or number, no IP-address form, and no `goog` prefix or `google` in the name. The namespace is global and public, so never put sensitive data in a bucket name.

## Task 2. Upload an object into the bucket

Right-click the kitten image in the lab instructions and save it as `kitten.png`.

**Console path:** Buckets > your bucket > **Objects** tab > **Upload** > **Upload files** > choose `kitten.png`. If the name is different, use the three-dot menu > **Rename** to make it `kitten.png`, since the progress check looks for that name.

```bash
# In Cloud Shell, upload the file first with More (⋮) > Upload, then:
gcloud storage cp kitten.png gs://$BUCKET
gcloud storage ls -l gs://$BUCKET
```

## Task 3. Share a bucket publicly

**Console path:** your bucket > **Permissions** tab > **View by principals** > **Grant access**.

1. **New principals:** `allUsers`
2. **Role:** Cloud Storage > **Storage Object Viewer**
3. **Save**, then **Allow public access** in the "Are you sure you want to make this resource public?" window.

```bash
gcloud storage buckets add-iam-policy-binding gs://$BUCKET \
  --member=allUsers \
  --role=roles/storage.objectViewer
```

Check it: on the **Objects** tab the **Public access** column reads **Access granted to public principals** (refresh if it doesn't). Click **Copy URL** and open it in a new tab:

```bash
echo "https://storage.googleapis.com/$BUCKET/kitten.png"
curl -I "https://storage.googleapis.com/$BUCKET/kitten.png"   # expect HTTP 200
```

## Task 4. Create folders

**Console path:** **Objects** tab > **Create folder** > name it `folder1` > **Create**. Click `folder1`, create `folder2` inside it, click `folder2`, then **Upload** > **Upload files** and pick `kitten.png` again.

```bash
# A "folder" is just a name prefix. Copying to the path creates it.
gcloud storage cp kitten.png gs://$BUCKET/folder1/folder2/kitten.png
gcloud storage ls -r gs://$BUCKET
```

## Task 5. Delete a folder

**Console path:** click the arrow next to **Bucket details** to go back to the bucket's top level, tick `folder1`, click **Delete**, type `DELETE` and click **Delete**. This removes `folder1`, `folder2` and the image inside them.

The lab text says "Select the bucket" here, but the confirmation deletes the folder. Tick the `folder1` row, not the bucket, or you delete the bucket and the image from Task 2.

```bash
gcloud storage rm -r gs://$BUCKET/folder1
gcloud storage ls gs://$BUCKET
```

## Troubleshooting

| Problem | Fix |
|---|---|
| "The requested bucket name is not available" | Bucket names are global. Use the Project ID, or add a suffix. |
| `allUsers` can't be added, or the public URL returns 403 | Public access prevention is still enforced. Bucket > **Configuration** tab > Public access prevention, turn it off, then grant `allUsers` again. |
| Public access column doesn't change after Task 3 | Refresh the page. IAM changes can take a minute to apply. |
| No per-object **Edit access** option | The bucket uses Uniform access control, so access is IAM on the bucket only (Task 3). That's what the lab asks for. |
| Check my progress fails on Task 2 | The object must be named exactly `kitten.png` at the bucket's top level. Rename it from the three-dot menu. |
| Check my progress fails on Task 3 | The binding must be `allUsers` with Storage Object Viewer on the bucket, not on one object. |
| An empty folder vanishes in `gcloud storage ls` | Without a placeholder object, a folder only exists while an object has that prefix. |
