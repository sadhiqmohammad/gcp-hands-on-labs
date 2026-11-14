# Lab 4.8: Get Started with Virtual Private Cloud Networking and Compute Engine

Delete the default network, rebuild it as an auto mode VPC network with firewall rules, create a VM in two regions, then delete the firewall rules one at a time to see what each one allows.

| Lab ID | Direct link |
|---|---|
| TBD | [Open in Google Skills](https://www.skills.google/course_templates/60/labs/689567) |

> **ACE exam note:** you can't create a VM without a VPC network. An auto mode network creates a subnet in every region. Every VPC network has two implied rules, deny all ingress and allow all egress, at the lowest priority, so anything you want to reach a VM needs an allow rule. Know which rule allows what: `allow-icmp` (ping from anywhere), `allow-custom` (all traffic inside the network), `allow-ssh` (tcp:22) and `allow-rdp` (tcp:3389).

This lab is part of module 4 of the Core Infrastructure course (Course 60). It takes 40 minutes. The lab assigns two regions and two zones (shown as `Region 1`, `Zone 1`, `Region 2` and `Zone 2`), so use what your lab shows.

Each step below has two options that do the same thing:

- **Option A: Console.** The lab's own steps in the Google Cloud console UI.
- **Option B: Cloud Shell.** The same step as `gcloud` commands. The lab itself is console-only; these commands are added here for practice and exam prep.

What the default network's four ingress rules allow, from the lab:

| Rule | Allows | From |
|---|---|---|
| `default-allow-icmp` | ICMP (ping) | Anywhere (`0.0.0.0/0`) |
| `default-allow-rdp` | RDP | Anywhere (`0.0.0.0/0`) |
| `default-allow-ssh` | SSH | Anywhere (`0.0.0.0/0`) |
| `default-allow-internal` | All TCP, UDP and ICMP | Inside the network (`10.128.0.0/9`) |

## Setup and requirements

1. Click **Start Lab**, then **Open Google Cloud console**. In Chrome, you can right-click and open it in an Incognito window.
2. Sign in with the **Username** and **Password** from the **Lab setup and access** panel. If **Choose an account** appears, click **Use Another Account**.
3. Accept the terms. Don't add recovery options or two-factor authentication, and don't sign up for free trials.

> Use only the lab's temporary credentials, never your own Google Cloud account, which may incur charges.

For Option B, click **Activate Cloud Shell** at the top of the console, then set the regions and zones the lab gives you:

```bash
export REGION=<Region 1>     # use the regions and zones your lab assigns
export ZONE=<Zone 1>
export REGION_2=<Region 2>
export ZONE_2=<Zone 2>
gcloud config set compute/zone $ZONE
```

## Task 1. Explore the default network

Every Google Cloud project has a default network with subnets, routes and firewall rules.

### View the subnets

The default network has a subnet in each region. Each subnet has a region, a private RFC 1918 CIDR block for its internal IP address range, and a gateway.

**Option A: Console**

1. In the **Navigation menu**, click **VPC network > VPC networks**.
2. Click **default**.
3. Click **Subnets**, and look at the default network's subnets.

**Option B: Cloud Shell**

```bash
gcloud compute networks subnets list --network=default
```

### View the routes

Routes tell VMs and the VPC network how to send traffic to a destination, inside the network or outside Google Cloud. There is a route for each subnet. These routes are managed for you, but you can add custom static routes, for example to send all outbound traffic to an instance set up as a NAT gateway.

**Option A: Console**

1. In the left pane, click **Routes**.
2. Under **Effective Routes**, click **Network** and select **default**.
3. Click **Region** and select the lab region assigned to you.
4. Click **View**, and note the route for each subnet.

**Option B: Cloud Shell**

```bash
gcloud compute routes list --filter="network:default"
```

### View the firewall rules

Every VPC network has a distributed virtual firewall, plus two implied rules: block all incoming connections, and allow all outgoing connections.

**Option A: Console**

1. In the left pane, click **Firewall**.
2. Note the 4 ingress rules for the default network: `default-allow-icmp`, `default-allow-rdp`, `default-allow-ssh` and `default-allow-internal`. The **Targets**, **Filters**, **Protocols/ports** and **Action** columns explain each rule.

**Option B: Cloud Shell**

```bash
gcloud compute firewall-rules list --filter="network:default"
```

### Delete the firewall rules

**Option A: Console**

1. On the **Firewall** page, select all of the default network's firewall rules.
2. Click **Delete**.
3. Click **Delete** to confirm.

**Option B: Cloud Shell**

```bash
gcloud compute firewall-rules delete default-allow-icmp default-allow-rdp \
  default-allow-ssh default-allow-internal --quiet
```

### Delete the default network

**Option A: Console**

1. In the **Navigation menu**, click **VPC network > VPC networks**.
2. Select the **default** network.
3. Click **Delete VPC network**, then **Delete** to confirm. Wait for the network to be deleted.
4. In the left pane, click **Routes**: there are no routes.
5. In the left pane, click **Firewall**: there are no firewall rules.

**Option B: Cloud Shell**

```bash
gcloud compute networks delete default --quiet

gcloud compute routes list           # no routes left
gcloud compute firewall-rules list   # no firewall rules left
```

Without a VPC network, there are no routes and no firewall rules.

### Try to create a VM instance

**Option A: Console**

1. In the **Navigation menu**, click **Compute Engine > VM instances**.
2. Click **Create instance**.
3. Accept the default values and click **Create**. An error shows on the **Networking** tab.
4. Click **Go to Issues**. Under **Network Interfaces**, note the **no more networks** and **no network available** errors.
5. Click **Cancel**.

**Option B: Cloud Shell**

```bash
gcloud compute instances create test-vm --zone=$ZONE   # fails: there is no network
```

Without a VPC network, you can't create a VM instance.

## Task 2. Create a VPC network and VM instances

### Create an auto mode VPC network with firewall rules

Replicate the default network by creating an auto mode network.

**Option A: Console**

1. In the **Navigation menu**, click **VPC network > VPC networks**.
2. Click **Create VPC network**.
3. For **Name**, type `mynetwork`.
4. For **Subnet creation mode**, click **Automatic**. Auto mode creates a subnet in each region.
5. For **Firewall rules**, select all available rules. They are the same standard rules the default network had.
   - The **deny-all-ingress** and **allow-all-egress** rules also show, but you can't select or clear them because they are implied. They have a lower priority (a higher number means a lower priority), so the allow ICMP, custom, RDP and SSH rules are checked first.
6. Click **Create**. When the network is ready, note that a subnet was created for each region.
7. Look at the IP address ranges for the subnets in `Region 1` and `Region 2`.

**Option B: Cloud Shell**

```bash
gcloud compute networks create mynetwork --subnet-mode=auto

gcloud compute firewall-rules create mynetwork-allow-icmp --network=mynetwork \
  --direction=INGRESS --allow=icmp --source-ranges=0.0.0.0/0 --priority=65534
gcloud compute firewall-rules create mynetwork-allow-custom --network=mynetwork \
  --direction=INGRESS --allow=all --source-ranges=10.128.0.0/9 --priority=65534
gcloud compute firewall-rules create mynetwork-allow-rdp --network=mynetwork \
  --direction=INGRESS --allow=tcp:3389 --source-ranges=0.0.0.0/0 --priority=65534
gcloud compute firewall-rules create mynetwork-allow-ssh --network=mynetwork \
  --direction=INGRESS --allow=tcp:22 --source-ranges=0.0.0.0/0 --priority=65534

gcloud compute networks subnets list --network=mynetwork \
  --filter="region:($REGION $REGION_2)"
```

The lab doesn't state the priority `65534` or the RDP port `3389`; they are what these commands use to match the console's standard rules.

> If you ever delete the default network, you can re-create it quickly as an auto mode network, as here. In the re-created network, the `allow-internal` rule becomes `allow-custom`.

### Create a VM instance in Region 1

The region and zone you pick decide the subnet, and the VM's internal IP address comes from that subnet's range.

**Option A: Console**

1. In the **Navigation menu**, click **Compute Engine > VM instances**.
2. Click **Create Instance**.
3. Set:

   | Property | Value |
   |---|---|
   | Name | `mynet-us-vm` |
   | Region | `Region 1` |
   | Zone | `Zone 1` |

4. For **Series**, select **E2**.
5. For **Machine type**, select **e2-micro (2 vCPU, 1 GB memory)**.
6. Click **Create**.

**Option B: Cloud Shell**

```bash
gcloud compute instances create mynet-us-vm --zone=$ZONE \
  --machine-type=e2-micro --network=mynetwork
```

### Create a VM instance in Region 2

**Option A: Console**

1. Click **Create Instance**.
2. Set the following, and leave the other settings as defaults:

   | Property | Value |
   |---|---|
   | Name | `mynet-r2-vm` |
   | Region | `Region 2` |
   | Zone | `Zone 2` |

3. For **Series**, select **E2**.
4. For **Machine type**, select **e2-micro (2 vCPU, 1 GB memory)**.
5. Click **Create**.

**Option B: Cloud Shell**

```bash
gcloud compute instances create mynet-r2-vm --zone=$ZONE_2 \
  --machine-type=e2-micro --network=mynetwork

gcloud compute instances list   # both VMs, with internal and external IPs
```

> Both VMs' external IP addresses are **ephemeral**. If an instance stops, its ephemeral external IP goes back to the Compute Engine pool, and it gets a new one when it starts again. A **static** external IP stays with your project until you release it.

Click **Check my progress** for **Create a VPC network and VM instance**.

## Task 3. Explore the connectivity for VM instances

You SSH to a VM on tcp:22, ping the other VM's internal and external IPs with ICMP, then delete the firewall rules one at a time to see what changes.

### Verify connectivity

**Option A: Console**

1. In the **Navigation menu**, click **Compute Engine > VM instances**.
2. Note the internal and external IP addresses of `mynet-r2-vm`.
3. For `mynet-us-vm`, click **SSH** to open a terminal and connect. If an **Authorize** pop-up appears, click **Authorize**.
4. In the SSH terminal, ping both IPs of `mynet-r2-vm`:

   ```bash
   ping -c 3 <mynet-r2-vm internal IP>
   ping -c 3 <mynet-r2-vm external IP>
   ```

**Option B: Cloud Shell**

```bash
INTERNAL_IP=$(gcloud compute instances describe mynet-r2-vm --zone=$ZONE_2 \
  --format='get(networkInterfaces[0].networkIP)')
EXTERNAL_IP=$(gcloud compute instances describe mynet-r2-vm --zone=$ZONE_2 \
  --format='get(networkInterfaces[0].accessConfigs[0].natIP)')

gcloud compute ssh mynet-us-vm --zone=$ZONE \
  --command="ping -c 3 $INTERNAL_IP; ping -c 3 $EXTERNAL_IP"
```

Both pings work:

- SSH works because of `allow-ssh`, which allows tcp:22 from anywhere (`0.0.0.0/0`). Compute Engine generates an SSH key for you and stores it in project or instance metadata by default, or with your user account if OS Login is set up.
- The internal ping works because of `allow-custom`.

**Lab question:** which firewall rule allows the ping to `mynet-r2-vm`'s external IP address? Answer: `mynetwork-allow-icmp`. It allows ICMP from anywhere, and the external IP is reached from outside the network, so `allow-custom` (inside the network only) doesn't cover it.

### Remove the allow-icmp rule

**Option A: Console**

1. In the **Navigation menu**, click **VPC network > Firewall**.
2. Select the `mynetwork-allow-icmp` rule.
3. Click **Delete**, then **Delete** to confirm. Wait for the rule to be deleted.
4. Return to the `mynet-us-vm` SSH terminal and ping again:

   ```bash
   ping -c 3 <mynet-r2-vm internal IP>   # still works: allow-custom
   ping -c 3 <mynet-r2-vm external IP>   # 100% packet loss: allow-icmp is gone
   ```

**Option B: Cloud Shell**

```bash
gcloud compute firewall-rules delete mynetwork-allow-icmp --quiet

gcloud compute ssh mynet-us-vm --zone=$ZONE \
  --command="ping -c 3 $INTERNAL_IP; ping -c 3 $EXTERNAL_IP"
```

### Remove the allow-custom rule

**Option A: Console**

1. In the **Navigation menu**, click **VPC network > Firewall**.
2. Select the `mynetwork-allow-custom` rule.
3. Click **Delete**, then **Delete** to confirm. Wait for the rule to be deleted.
4. Return to the `mynet-us-vm` SSH terminal, ping the internal IP, then close the terminal:

   ```bash
   ping -c 3 <mynet-r2-vm internal IP>   # 100% packet loss: allow-custom is gone
   exit
   ```

**Option B: Cloud Shell**

```bash
gcloud compute firewall-rules delete mynetwork-allow-custom --quiet

gcloud compute ssh mynet-us-vm --zone=$ZONE --command="ping -c 3 $INTERNAL_IP"
```

### Remove the allow-ssh rule

**Option A: Console**

1. In the **Navigation menu**, click **VPC network > Firewall**.
2. Select the `mynetwork-allow-ssh` rule.
3. Click **Delete**, then **Delete** to confirm. Wait for the rule to be deleted.
4. In the **Navigation menu**, click **Compute Engine > VM instances**.
5. For `mynet-us-vm`, click **SSH**. **Connection failed** shows, because `allow-ssh` is gone.

**Option B: Cloud Shell**

```bash
gcloud compute firewall-rules delete mynetwork-allow-ssh --quiet

gcloud compute ssh mynet-us-vm --zone=$ZONE   # fails: nothing allows tcp:22 now
```

What still works after each deletion (the rules are deleted in order, so each row includes the deletions above it):

| Rules deleted so far | Ping internal IP | Ping external IP | SSH |
|---|---|---|---|
| None | Works | Works | Works |
| `allow-icmp` | Works (`allow-custom`) | Fails | Works |
| `allow-icmp`, `allow-custom` | Fails | Fails | Works |
| `allow-icmp`, `allow-custom`, `allow-ssh` | Fails | Fails | Fails |

## Task 4. Review

You explored the default network's subnets, routes and firewall rules, deleted it, and saw that you can't create a VM without a VPC network. You then created an auto mode VPC network with subnets, routes, firewall rules and two VMs, and tested how each firewall rule affects connectivity.

## End your lab

Click **End Lab**. Google Skills removes the resources and cleans the account. Rate the lab if you want to, or close the dialog.

## Troubleshooting

| Symptom | Fix |
|---|---|
| Can't create a VM: **no more networks** or **no network available** | Expected in Task 1, after you delete the default network. Create `mynetwork` first. |
| Can't change **deny-all-ingress** or **allow-all-egress** | They are implied rules on every VPC network; they can't be selected or removed. |
| Firewall rule list shows `allow-custom`, not `allow-internal` | Expected for an auto mode network you create; it covers the same internal traffic. |
| Ping still works right after deleting a rule | Wait for the deletion to finish, then ping again. |
| External ping fails but internal ping works | `mynetwork-allow-icmp` is deleted; `allow-custom` still allows traffic inside the network. |
| SSH shows **Connection failed** | `mynetwork-allow-ssh` is deleted. Expected at the end of the lab. |
| `$INTERNAL_IP` or `$EXTERNAL_IP` is empty in Cloud Shell | Cloud Shell was restarted. Re-run the `export` lines from setup and the two `describe` commands. |
| Check my progress fails | Make sure `mynetwork` is auto mode and both VMs exist with the names, regions, zones and machine type from the lab. |
