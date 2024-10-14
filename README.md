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
# Set environment variables
PROJECT_ID="velocity-406304"
VPC_NAME="vpc-02"
VPC_DESC="example vpc for learning. created by sritam"
SUBNET_MODE="auto"
MTU="1460"
BGP_ROUTING_MODE="regional"
BGP_BEST_PATH_SELECTION_MODE="legacy"
FIREWALL_PRIORITY="65534"
SOURCE_RANGES="0.0.0.0/0"
CUSTOM_SOURCE_RANGES="10.128.0.0/9"

# Create VPC Network
gcloud compute networks create $VPC_NAME \
  --project=$PROJECT_ID \
  --description="$VPC_DESC" \
  --subnet-mode=$SUBNET_MODE \
  --mtu=$MTU \
  --bgp-routing-mode=$BGP_ROUTING_MODE \
  --bgp-best-path-selection-mode=$BGP_BEST_PATH_SELECTION_MODE

# Create Firewall Rule for Custom Protocols
gcloud compute firewall-rules create "${VPC_NAME}-allow-custom" \
  --project=$PROJECT_ID \
  --network=projects/$PROJECT_ID/global/networks/$VPC_NAME \
  --description="Allows connection from any source to any instance on the network using custom protocols." \
  --direction=INGRESS \
  --priority=$FIREWALL_PRIORITY \
  --source-ranges=$CUSTOM_SOURCE_RANGES \
  --action=ALLOW \
  --rules=all

# Create Firewall Rule for ICMP
gcloud compute firewall-rules create "${VPC_NAME}-allow-icmp" \
  --project=$PROJECT_ID \
  --network=projects/$PROJECT_ID/global/networks/$VPC_NAME \
  --description="Allows ICMP connections from any source to any instance on the network." \
  --direction=INGRESS \
  --priority=$FIREWALL_PRIORITY \
  --source-ranges=$SOURCE_RANGES \
  --action=ALLOW \
  --rules=icmp

# Create Firewall Rule for RDP
gcloud compute firewall-rules create "${VPC_NAME}-allow-rdp" \
  --project=$PROJECT_ID \
  --network=projects/$PROJECT_ID/global/networks/$VPC_NAME \
  --description="Allows RDP connections from any source to any instance on the network using port 3389." \
  --direction=INGRESS \
  --priority=$FIREWALL_PRIORITY \
  --source-ranges=$SOURCE_RANGES \
  --action=ALLOW \
  --rules=tcp:3389

# Create Firewall Rule for SSH
gcloud compute firewall-rules create "${VPC_NAME}-allow-ssh" \
  --project=$PROJECT_ID \
  --network=projects/$PROJECT_ID/global/networks/$VPC_NAME \
  --description="Allows TCP connections from any source to any instance on the network using port 22." \
  --direction=INGRESS \
  --priority=$FIREWALL_PRIORITY \
  --source-ranges=$SOURCE_RANGES \
  --action=ALLOW \
  --rules=tcp:22
