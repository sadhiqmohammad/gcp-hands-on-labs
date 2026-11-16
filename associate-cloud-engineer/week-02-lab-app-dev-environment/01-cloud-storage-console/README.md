# Lab 1: Manage Cloud Storage with the Google Cloud Console

A Cloud Storage bucket with an uploaded image, made public to everyone, plus folders created and then deleted.

| Lab ID | Direct link |
|---|---|
| GSP073 | [Open in Google Skills](https://www.skills.google/course_templates/637/labs/592541) |

> **ACE exam note:** bucket names are global and must be unique across all of Google Cloud. To make objects public, grant `allUsers` the **Storage Object Viewer** role, which is blocked while public access prevention is enforced. Folders are only name prefixes in a flat namespace.

This is a console lab, so the console path is the main step in each task. The `gcloud storage` commands do the same thing from Cloud Shell, which is useful for the exam and for the CLI lab later on. The lab assigns its own region and project, so use those values.

```bash
export REGION=us-east1                       # from your lab
export BUCKET=$(gcloud config get-value project)   # bucket names must be globally unique; the project ID usually is
```

## Task 1. Create a bucket

**Console path:** Navigation menu > Cloud Storage > Buckets > **+ Create**.

1. **Name:** a globally unique name, such as your project ID. No uppercase letters or spaces.
2. **Location type:** Region, set to the lab's region.
3. **Storage class:** Standard.
4. **Access control:** clear **Enforce public access prevention on this bucket**, then pick **Fine-grained** if the lab asks for it.
5. Click **Create**. If a "Public access will be prevented" dialog appears, keep the box cleared and confirm.

```bash
gcloud storage buckets create gs://$BUCKET \
  --location=$REGION \
  --default-storage-class=STANDARD \
  --no-uniform-bucket-level-access \
  --no-public-access-prevention
```

## Task 2. Upload an object into the bucket

Download the image the lab links to and save it as `kitten.png`.

**Console path:** Buckets > your bucket > **Upload** > Upload files > choose `kitten.png`.

```bash
# In Cloud Shell, upload the file first with More (⋮) > Upload, then:
gcloud storage cp kitten.png gs://$BUCKET
gcloud storage ls gs://$BUCKET
```

## Task 3. Share a bucket publicly

**Console path:** your bucket > **Permissions** tab > **Grant access**.

1. **New principals:** `allUsers`
2. **Role:** Cloud Storage > **Storage Object Viewer**
3. **Save**, then confirm **Allow public access**.

```bash
gcloud storage buckets add-iam-policy-binding gs://$BUCKET \
  --member=allUsers \
  --role=roles/storage.objectViewer
```

Check it: on the **Objects** tab the object now shows **Public to internet** with a **Copy URL** link. Open it in a private browser window:

```bash
echo "https://storage.googleapis.com/$BUCKET/kitten.png"
curl -I "https://storage.googleapis.com/$BUCKET/kitten.png"   # expect HTTP 200
```

## Task 4. Create folders

**Console path:** your bucket > **Create folder** > name it `folder1` > Create. Open `folder1`, create `folder2` inside it, open `folder2` and upload `kitten.png` again.

```bash
# A "folder" is just a name prefix. Copying to the path creates it.
gcloud storage cp kitten.png gs://$BUCKET/folder1/folder2/kitten.png
gcloud storage ls -r gs://$BUCKET
```

## Task 5. Delete a folder

**Console path:** go back to the bucket's top level (the bucket name link above the object list), tick `folder1` > **Delete** > type `DELETE` to confirm. This deletes `folder1`, `folder2` and the image inside them.

```bash
gcloud storage rm -r gs://$BUCKET/folder1
gcloud storage ls gs://$BUCKET
```

## Troubleshooting

| Problem | Fix |
|---|---|
| "The requested bucket name is not available" | Bucket names are global. Add a suffix, such as `$BUCKET-1`. |
| `allUsers` can't be added, or the public URL returns 403 | Public access prevention is still enforced. Bucket > **Configuration** tab > Public access prevention, turn it off, then grant `allUsers` again. |
| Public URL still returns 403 just after granting access | IAM changes can take a minute to apply. Wait and retry in a private window. |
| "Uniform bucket-level access is enabled" when editing object ACLs | The bucket uses uniform access. Use IAM on the bucket (Task 3) instead of per-object ACLs. |
| An empty folder vanishes in `gcloud storage ls` | Without a placeholder object, a folder only exists while an object has that prefix. |
| Check my progress fails on Task 3 | The binding must be `allUsers` with Storage Object Viewer on the bucket itself, not on one object. |
