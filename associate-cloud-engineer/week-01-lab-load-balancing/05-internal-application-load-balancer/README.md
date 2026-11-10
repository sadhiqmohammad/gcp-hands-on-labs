# Lab 5: Internal Application Load Balancer

A backend managed instance group that is reachable only inside the VPC through an internal load balancer, plus a public frontend VM that calls it.

| Lab ID | Direct link |
|---|---|
| GSP041 | [Open in Google Skills](https://www.skills.google/course_templates/648/labs/613024) |

> **ACE exam note:** internal load balancers are regional and get a private IP in your VPC (`--load-balancing-scheme=INTERNAL` or `INTERNAL_MANAGED`). Clients must be in the same VPC or a connected network. The health-check firewall ranges `130.211.0.0/22` and `35.191.0.0/16` still apply.

> **Check your lab text:** the lab page is behind sign-in, so these steps follow the standard GSP041 flow (a "prime number" backend service behind an internal LB with a fixed IP). Use the resource names, IP, region and zone your lab gives you.

## Task 1. Set region and zone

```bash
export REGION=us-central1      # from your lab
export ZONE=us-central1-a      # from your lab
gcloud config set compute/region $REGION
gcloud config set compute/zone $ZONE
```

## Task 2. Backend instance template

The backend VMs run a small Python web service that answers whether a number is prime (`/<n>` returns `True` or `False`).

Create `backend.sh` in Cloud Shell (for example with `nano backend.sh`) and paste:

```bash
sudo chmod -R 777 /usr/local/sbin/
sudo cat << EOF > /usr/local/sbin/serveprimes.py
import http.server

def is_prime(a): return a!=1 and all(a % i for i in range(2,int(a**0.5)+1))

class myHandler(http.server.BaseHTTPRequestHandler):
  def do_GET(s):
    s.send_response(200)
    s.send_header("Content-type", "text/plain")
    s.end_headers()
    s.wfile.write(bytes(str(is_prime(int(s.path[1:]))).encode('utf-8')))

http.server.HTTPServer(("",80),myHandler).serve_forever()
EOF
nohup python3 /usr/local/sbin/serveprimes.py >/dev/null 2>&1 &
```

Then create the template:

```bash
gcloud compute instance-templates create primecalc \
  --metadata-from-file startup-script=backend.sh \
  --no-address \
  --tags backend \
  --machine-type=e2-medium
```

`--no-address` means the backends get no external IP. They are only reachable inside the VPC.

## Task 3. Firewall and managed instance group

```bash
# In-VPC traffic to the backends on port 80
gcloud compute firewall-rules create http \
  --network default \
  --allow=tcp:80 \
  --source-ranges 10.128.0.0/9 \
  --target-tags backend

# Google health checkers
gcloud compute firewall-rules create allow-health-check \
  --network default \
  --allow=tcp:80 \
  --source-ranges 130.211.0.0/22,35.191.0.0/16 \
  --target-tags backend

# MIG of 3 backends with autoscaling
gcloud compute instance-groups managed create backend \
  --size 3 \
  --template primecalc \
  --zone $ZONE

gcloud compute instance-groups managed set-autoscaling backend \
  --target-cpu-utilization 0.8 \
  --min-num-replicas 3 \
  --max-num-replicas 10 \
  --zone $ZONE
```

## Task 4. Create the internal load balancer

```bash
gcloud compute health-checks create http ilb-health --request-path /2

gcloud compute backend-services create prime-service \
  --load-balancing-scheme internal \
  --region=$REGION \
  --protocol tcp \
  --health-checks ilb-health

gcloud compute backend-services add-backend prime-service \
  --instance-group backend \
  --instance-group-zone=$ZONE \
  --region=$REGION

gcloud compute forwarding-rules create prime-lb \
  --load-balancing-scheme internal \
  --ports 80 \
  --network default \
  --region=$REGION \
  --address 10.128.10.10 \
  --backend-service prime-service
```

The health check requests `/2`, which returns `True`, so healthy backends answer with HTTP 200.

## Task 5. Test from inside the VPC

```bash
gcloud compute instances create testinstance \
  --machine-type=e2-standard-2 \
  --zone $ZONE

gcloud compute ssh testinstance --zone $ZONE
```

On `testinstance`:

```bash
curl 10.128.10.10/2    # True
curl 10.128.10.10/4    # False
curl 10.128.10.10/5    # True
exit
```

Back in Cloud Shell:

```bash
gcloud compute instances delete testinstance --zone=$ZONE --quiet
```

## Task 6. Public frontend that calls the internal LB

Create `frontend.sh` in Cloud Shell and paste:

```bash
sudo chmod -R 777 /usr/local/sbin/
sudo cat << EOF > /usr/local/sbin/getprimes.py
import urllib.request
from multiprocessing.dummy import Pool as ThreadPool
import http.server
PREFIX="http://10.128.10.10/"
def get_url(number):
    return urllib.request.urlopen(PREFIX+str(number)).read().decode('utf-8')
class myHandler(http.server.BaseHTTPRequestHandler):
  def do_GET(s):
    s.send_response(200)
    s.send_header("Content-type", "text/html")
    s.end_headers()
    i = int(s.path[1:]) if (len(s.path)>1) else 1
    s.wfile.write("<html><body><table>".encode('utf-8'))
    pool = ThreadPool(10)
    results = pool.map(get_url,range(i,i+100))
    for x in range(0,100):
      if not (x % 10): s.wfile.write("<tr>".encode('utf-8'))
      if results[x]=="True":
        s.wfile.write("<td bgcolor='#00ff00'>".encode('utf-8'))
      else:
        s.wfile.write("<td bgcolor='#ff0000'>".encode('utf-8'))
      s.wfile.write(str(x+i).encode('utf-8')+"</td> ".encode('utf-8'))
      if not ((x+1) % 10): s.wfile.write("</tr>".encode('utf-8'))
    s.wfile.write("</table></body></html>".encode('utf-8'))
http.server.HTTPServer(("",80),myHandler).serve_forever()
EOF
nohup python3 /usr/local/sbin/getprimes.py >/dev/null 2>&1 &
```

Then create the frontend VM and open it to the internet:

```bash
gcloud compute instances create frontend \
  --zone=$ZONE \
  --metadata-from-file startup-script=frontend.sh \
  --tags frontend \
  --machine-type=e2-standard-2

gcloud compute firewall-rules create http2 \
  --network default \
  --allow=tcp:80 \
  --source-ranges 0.0.0.0/0 \
  --target-tags frontend

gcloud compute instances list --filter=name:frontend \
  --format='value(EXTERNAL_IP)'
```

Open `http://<frontend external IP>` in a browser. You get a 10x10 grid with primes in green. Add a number to the path (for example `/10000`) to start the grid there.

## Troubleshooting

| Symptom | Fix |
|---|---|
| `curl 10.128.10.10` hangs | Backends unhealthy. Check `allow-health-check` and the `backend` tag, then `gcloud compute backend-services get-health prime-service --region=$REGION`. |
| Forwarding rule rejects the IP | The address must be free and inside the subnet range for your region. Use the IP your lab specifies. |
| Frontend grid is empty or errors | The frontend can't reach 10.128.10.10. Confirm the forwarding rule exists and the backends are healthy. |
| Can't reach the internal IP from Cloud Shell | Expected. Cloud Shell is outside your VPC, so test from a VM. |
