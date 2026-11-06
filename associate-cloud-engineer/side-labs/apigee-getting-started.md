# Apigee: put an API proxy in front of your service

A hands-on path from zero to a working, API-key-protected Apigee endpoint in front of a Cloud Run service, plus the cheaper API Gateway route for lab practice.

> **ACE exam note:** Apigee is barely tested on Associate Cloud Engineer. Cloud Run, IAM, service accounts, and enabling APIs are. Treat this as a portfolio/side lab, not core exam prep. API Gateway (section 8) is the cheaper way to practice the same idea.

---

## 0. Pick a provisioning option first (cost matters)

| Option | Cost | Good for |
|---|---|---|
| **Evaluation org** | Free for 60 days, then it expires and is deleted | Learning. **Use this.** |
| **Pay-as-you-go** | Billed per environment-hour plus API calls and add-ons. Even a Base environment runs to hundreds of USD a month | Real workloads |
| **Subscription** | Annual contract | Enterprise |

Cautions:
- One Apigee org per GCP project. Use a **dedicated lab project** so cleanup is easy.
- Eval orgs need a billing account linked, but are not charged for Apigee itself. The supporting resources (load balancer, external IP) can still cost a little.
- Check current prices on the [Apigee pricing page](https://cloud.google.com/apigee/pricing) before you pick pay-as-you-go.

## 1. Project setup and APIs

```bash
export PROJECT_ID=my-apigee-lab
export REGION=us-central1

gcloud projects create $PROJECT_ID
gcloud billing projects link $PROJECT_ID --billing-account=XXXXXX-XXXXXX-XXXXXX
gcloud config set project $PROJECT_ID

gcloud services enable \
  apigee.googleapis.com \
  servicenetworking.googleapis.com \
  compute.googleapis.com \
  cloudkms.googleapis.com \
  run.googleapis.com \
  cloudbuild.googleapis.com
```

## 2. A backend to proxy (Cloud Run)

If you don't have one yet, deploy Google's sample:

```bash
gcloud run deploy hello-backend \
  --image=us-docker.pkg.dev/cloudrun/container/hello \
  --region=$REGION \
  --allow-unauthenticated

BACKEND_URL=$(gcloud run services describe hello-backend --region=$REGION --format='value(status.url)')
echo $BACKEND_URL
```

`--allow-unauthenticated` keeps the lab simple. For a private backend, see section 7.

## 3. Provision the Apigee eval org

**Console (easiest):** search for **Apigee** in the console, choose **Free evaluation**, and follow the wizard:
1. Enable the APIs it lists.
2. Networking: pick your VPC (`default` is fine) and let it reserve a peering IP range. (Newer wizards also offer a no-VPC-peering option; either works.)
3. Runtime region: `us-central1`.
4. Access routing: **Enable internet access** and accept the generated hostname. It looks like `34-x-x-x.nip.io`.

Provisioning takes roughly 30 to 60 minutes. When it's done you'll have:
- an environment named `eval`
- an environment group `eval-group` with your hostname attached

**gcloud alternative** (does the same thing as the wizard):

```bash
gcloud alpha apigee organizations provision \
  --runtime-location=$REGION \
  --analytics-region=$REGION \
  --authorized-network=default \
  --project=$PROJECT_ID
```

Find your hostname afterwards in **Apigee > Management > Environments > Environment groups**.

```bash
export APIGEE_HOST=34-x-x-x.nip.io   # yours
```

## 4. Create the API proxy

Console: **Apigee > Proxy development > API proxies > + Create**
1. Template: **Reverse proxy**.
2. Name: `hello-v1`. Base path: `/hello/v1`.
3. Target (existing API): paste `$BACKEND_URL`.
4. Skip security for now (you'll add it in section 6).
5. Create.

What you just built: a **ProxyEndpoint** (what clients call: `https://$APIGEE_HOST/hello/v1`) and a **TargetEndpoint** (where Apigee forwards: Cloud Run).

## 5. Deploy and test

In the proxy editor click **Deploy**, choose environment `eval`, and accept the default service account (or create one). Wait for the status to show deployed.

```bash
curl -i https://$APIGEE_HOST/hello/v1
```

You should get the Cloud Run "It's running!" HTML back. The **Debug** tab in the proxy editor lets you start a session and watch each request flow through the policies, which is the fastest way to learn what's happening.

## 6. Protect it with an API key

**a) Add the policy.** In the proxy's **Develop** tab, under *Proxy endpoints > default > PreFlow (Request)*, add a **Verify API Key** policy:

```xml
<VerifyAPIKey name="VA-verify-key">
  <APIKey ref="request.queryparam.apikey"/>
</VerifyAPIKey>
```

Optionally add **Assign Message** after it to strip the key so it isn't sent to the backend:

```xml
<AssignMessage name="AM-remove-key">
  <Remove><QueryParams><QueryParam name="apikey"/></QueryParams></Remove>
</AssignMessage>
```

Save and redeploy. Calling without a key now returns `401`.

**b) Create the consumer side** (this is Apigee's "productize your API" model):
1. **Distribution > API products > Create**: name `hello-product`, add operation for proxy `hello-v1`, path `/**`, environment `eval`, access *Public*, approval *Automatic*.
2. **Distribution > Developers > Create**: any test developer email.
3. **Distribution > Apps > Create**: pick the developer, add `hello-product`. Copy the **Key** it generates.

**c) Test:**

```bash
curl -i "https://$APIGEE_HOST/hello/v1"                    # 401
curl -i "https://$APIGEE_HOST/hello/v1?apikey=YOUR_KEY"    # 200
```

The Product > App > Key chain maps nicely onto how you think about Salesforce: product = what's sold, app = the subscriber, key = their entitlement.

Good next policies to try: **Quota** or **Spike Arrest** (rate limits), **Response Cache**, **OAuthV2**.

## 7. Private Cloud Run backend (optional)

Remove public access from Cloud Run and let Apigee call it with an ID token:

```bash
gcloud run services remove-iam-policy-binding hello-backend --region=$REGION \
  --member=allUsers --role=roles/run.invoker

gcloud iam service-accounts create apigee-runtime
gcloud run services add-iam-policy-binding hello-backend --region=$REGION \
  --member=serviceAccount:apigee-runtime@$PROJECT_ID.iam.gserviceaccount.com \
  --role=roles/run.invoker
```

In the TargetEndpoint, add:

```xml
<HTTPTargetConnection>
  <Authentication>
    <GoogleIDToken><Audience>BACKEND_URL</Audience></GoogleIDToken>
  </Authentication>
  <URL>BACKEND_URL</URL>
</HTTPTargetConnection>
```

Redeploy, choosing the `apigee-runtime` service account. This IAM pattern (service account + `run.invoker`) is very much ACE material.

## 8. Cheaper alternative for lab practice: API Gateway

API Gateway is a lighter managed gateway (OpenAPI spec, API keys, JWT auth). It has a free tier of millions of calls per month and no hourly environment fee, so it's the safer choice for practice. It lacks Apigee's policies, developer portal, monetization and analytics.

```bash
gcloud services enable apigateway.googleapis.com servicemanagement.googleapis.com servicecontrol.googleapis.com

cat > openapi.yaml <<EOF
swagger: '2.0'
info: {title: hello-api, version: 1.0.0}
schemes: [https]
produces: [application/json]
x-google-backend:
  address: ${BACKEND_URL}
securityDefinitions:
  api_key: {type: apiKey, name: key, in: query}
paths:
  /hello:
    get:
      operationId: hello
      security: [{api_key: []}]
      responses: {'200': {description: OK}}
EOF

gcloud api-gateway apis create hello-api
gcloud api-gateway api-configs create hello-config --api=hello-api --openapi-spec=openapi.yaml
gcloud api-gateway gateways create hello-gw --api=hello-api --api-config=hello-config --location=$REGION

GW_HOST=$(gcloud api-gateway gateways describe hello-gw --location=$REGION --format='value(defaultHostname)')
```

Then enable the API's managed service (`gcloud services enable <managedService from 'gcloud api-gateway apis describe hello-api'>`), create a key under **APIs & Services > Credentials**, and test:

```bash
curl "https://$GW_HOST/hello?key=YOUR_KEY"
```

## 9. Cleanup

- **Eval org:** expires after 60 days on its own. To remove sooner: `gcloud alpha apigee organizations delete --organization=$PROJECT_ID` (or just delete the project).
- **Everything:** `gcloud projects delete $PROJECT_ID` is the cleanest exit and also releases the load balancer IP.
- **API Gateway:** `gcloud api-gateway gateways delete hello-gw --location=$REGION`, then the config and API.

## References
- [Apigee: provision an eval org](https://cloud.google.com/apigee/docs/api-platform/get-started/eval-orgs)
- [Apigee: build your first proxy](https://cloud.google.com/apigee/docs/api-platform/get-started/create-proxy)
- [Verify API Key policy](https://cloud.google.com/apigee/docs/api-platform/reference/policies/verify-api-key-policy)
- [API Gateway quickstart](https://cloud.google.com/api-gateway/docs/quickstart)
