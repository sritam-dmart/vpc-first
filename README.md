# Creating a VPC Network in GCP Console

## Step 1: Sign in to the Google Cloud Console
1. Navigate to [Google Cloud Console](https://console.cloud.google.com/).
2. Log in with your credentials if you aren't already logged in.

## Step 2: Navigate to the VPC Networks Section
1. Once logged in, click on the **Navigation Menu** (three horizontal lines) in the top-left corner.
2. From the drop-down, go to **VPC network** under the **Networking** section.
3. Click on **VPC networks**.

## Step 3: Create a New VPC Network
1. At the top of the **VPC networks** page, click the **Create VPC network** button.
   
## Step 4: Configure VPC Network Details
1. **Name**: Set the name to `mynetwork`.
2. **Subnet creation mode**: Click on **Automatic**.
   - Auto mode creates subnets in each region automatically, similar to the default network.

## Step 5: Configure Firewall Rules
1. In the **Firewall rules** section, check all available firewall rule options, such as:
   - **Allow ICMP**
   - **Allow internal traffic**
   - **Allow RDP (3389)**
   - **Allow SSH (22)**
   
   These rules replicate the standard firewall rules that come with the default network.
   
2. You will also see two implied rules:
   - **deny-all-ingress**: Blocks all incoming traffic.
   - **allow-all-egress**: Allows all outgoing traffic.
   
   These cannot be unchecked as they are system-generated but have lower priority compared to the custom allow rules.

## Step 6: Create the VPC Network
1. Review the configuration and click **Create**.
2. Wait for `mynetwork` to be created.

## Step 7: Verify the VPC Network
1. Once the network is created, you will be redirected to the **VPC networks** page.
2. You will notice that subnets have been automatically created for each region.
3. Click on the **mynetwork** name and record the **IP address range** for subnets in different regions. These details will be used when creating resources like VM instances.

## Step 8: Recreating the Default Network (Optional)
- If the default network is ever deleted, you can recreate it by following the same process of creating an auto mode VPC network, as described above.

## Setting Up VPC Using gcloud Commands

### Step 9: Set Environment Variables
You can use the following script to create a VPC network and associated firewall rules using the `gcloud` command-line tool:

```bash
gcloud compute networks create vpc-02 --project=velocity-406304 --description=example\ vpc\ for\ learning.\ created\ by\ sritam --subnet-mode=auto --mtu=1460 --bgp-routing-mode=regional --bgp-best-path-selection-mode=legacy

gcloud compute firewall-rules create vpc-02-allow-custom --project=velocity-406304 --network=projects/velocity-406304/global/networks/vpc-02 --description=Allows\ connection\ from\ any\ source\ to\ any\ instance\ on\ the\ network\ using\ custom\ protocols. --direction=INGRESS --priority=65534 --source-ranges=10.128.0.0/9 --action=ALLOW --rules=all

gcloud compute firewall-rules create vpc-02-allow-icmp --project=velocity-406304 --network=projects/velocity-406304/global/networks/vpc-02 --description=Allows\ ICMP\ connections\ from\ any\ source\ to\ any\ instance\ on\ the\ network. --direction=INGRESS --priority=65534 --source-ranges=0.0.0.0/0 --action=ALLOW --rules=icmp

gcloud compute firewall-rules create vpc-02-allow-rdp --project=velocity-406304 --network=projects/velocity-406304/global/networks/vpc-02 --description=Allows\ RDP\ connections\ from\ any\ source\ to\ any\ instance\ on\ the\ network\ using\ port\ 3389. --direction=INGRESS --priority=65534 --source-ranges=0.0.0.0/0 --action=ALLOW --rules=tcp:3389

gcloud compute firewall-rules create vpc-02-allow-ssh --project=velocity-406304 --network=projects/velocity-406304/global/networks/vpc-02 --description=Allows\ TCP\ connections\ from\ any\ source\ to\ any\ instance\ on\ the\ network\ using\ port\ 22. --direction=INGRESS --priority=65534 --source-ranges=0.0.0.0/0 --action=ALLOW --rules=tcp:22

```bash
