# Lab 2: Manage Cloud Storage with the Google Cloud CLI

The Lab 1 tasks done from Cloud Shell with `gcloud storage`: a bucket, an uploaded image, a folder copy, listings, and an object made public and private again with ACLs.

| Lab ID | Direct link |
|---|---|
| GSP074 | [Open in Google Skills](https://www.skills.google/course_templates/637/labs/592542) |

> **ACE exam note:** know the `gcloud storage` verbs: `buckets create`, `cp`, `ls` (`-l` for size and date), `rm`, and `objects update` for ACLs. Lab 1 made a whole bucket public with IAM; this lab makes one object public with an ACL (`entity=allUsers,role=READER`), which only works on a bucket with fine-grained access control.

All tasks run in Cloud Shell. The lab assigns its own project and region, so use those values. You can watch each change in the console under Navigation menu > Cloud Storage; refresh after each command. Tasks 1, 4 and 7 have a **Check my progress** step.

## Setup

```bash
gcloud auth list
gcloud config list project

export REGION=us-east1                             # from your lab
gcloud config set compute/region $REGION

export BUCKET=$(gcloud config get-value project)   # any unique name that follows the naming rules
```

## Task 1. Create a bucket

```bash
gcloud storage buckets create gs://$BUCKET
```

This uses the default settings. To see them: Cloud Storage > your bucket > **Configuration** tab.

Bucket names follow the same rules as in [Lab 1](../01-cloud-storage-console/README.md#task-1-create-a-bucket): 3 to 63 characters, lowercase letters, numbers, `-`, `_` and `.`, start and end with a letter or number, no `goog` prefix, no `google` in the name. If the name is taken you get `ServiceException: 409 Bucket ... already exists`, so pick another.

## Task 2. Upload an object into your bucket

Download the image to Cloud Shell, upload it to the bucket, then delete the local copy:

```bash
curl https://upload.wikimedia.org/wikipedia/commons/thumb/a/a4/Ada_Lovelace_portrait.jpg/800px-Ada_Lovelace_portrait.jpg --output ada.jpg

gcloud storage cp ada.jpg gs://$BUCKET

rm ada.jpg
```

Tip: press Tab to autocomplete the bucket name.

## Task 3. Download an object from your bucket

```bash
gcloud storage cp -r gs://$BUCKET/ada.jpg .
```

Expect `Operation completed over 1 objects`.

## Task 4. Copy an object to a folder in the bucket

Copying to a path ending in `/` creates the folder `image-folder`:

```bash
gcloud storage cp gs://$BUCKET/ada.jpg gs://$BUCKET/image-folder/
```

## Task 5. List contents of a bucket or folder

```bash
gcloud storage ls gs://$BUCKET
```

Expected:

```text
gs://YOUR-BUCKET-NAME/ada.jpg
gs://YOUR-BUCKET-NAME/image-folder/
```

## Task 6. List details for an object

```bash
gcloud storage ls -l gs://$BUCKET/ada.jpg
```

The output shows the object's size in bytes, its creation time and a total.

## Task 7. Make your object publicly accessible

Grant all users read access to this one object with an ACL:

```bash
gcloud storage objects update gs://$BUCKET/ada.jpg --add-acl-grant=entity=allUsers,role=READER
```

Check it: Cloud Storage > your bucket. `ada.jpg` now shows a public link. Click **Copy URL** and open it in a new tab, or:

```bash
curl -I "https://storage.googleapis.com/$BUCKET/ada.jpg"   # expect HTTP 200
```

## Task 8. Remove public access

```bash
gcloud storage objects update gs://$BUCKET/ada.jpg --remove-acl-grant=allUsers
```

Refresh the console. The public link checkmark is gone.

## Delete objects

```bash
gcloud storage rm gs://$BUCKET/ada.jpg
gcloud storage ls -r gs://$BUCKET
```

Only the top-level `ada.jpg` is removed. The copy in `image-folder/` is still there.

## Troubleshooting

| Problem | Fix |
|---|---|
| `409 Bucket ... already exists` | Bucket names are global. Try another name, such as `$BUCKET-1`. |
| `ada.jpg` not found on upload | Run `ls` to check the `curl` download worked and you are in the same directory. |
| Task 7 fails with "uniform bucket-level access is enabled" | Object ACLs need fine-grained access. Turn it off with `gcloud storage buckets update gs://$BUCKET --no-uniform-bucket-level-access`, then run Task 7 again. |
| Task 7 fails or the URL returns 403 because of public access prevention | Check the bucket's **Configuration** tab. If public access prevention is enforced, run `gcloud storage buckets update gs://$BUCKET --no-public-access-prevention`. |
| Check my progress fails on Task 4 | The object must be at `image-folder/ada.jpg`. Run `gcloud storage ls -r gs://$BUCKET` to check the path. |
| Public link still shows after Task 8 | Refresh the console. |
