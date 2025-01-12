# Step-by-Step Guide to Create a VPC and an Amazon EKS Cluster

This document provides a detailed step-by-step guide to set up a Virtual Private Cloud (VPC) and use it to create an Amazon Elastic Kubernetes Service (EKS) cluster using the AWS Management Console.

---

## Step 1: Plan the VPC and Subnet CIDR Blocks

### VPC CIDR
- Choose a CIDR block for the VPC, e.g., `10.0.0.0/16`. This provides 65,536 IPs.

### Subnets
- Create at least **two subnets** in different availability zones for high availability.
- Subnet CIDRs:
  - **Subnet 1 (AZ1)**: `10.0.0.0/24` (256 IPs)
  - **Subnet 2 (AZ2)**: `10.0.1.0/24` (256 IPs)

---

## Step 2: Create the VPC
1. Log in to the AWS Management Console.
2. Navigate to **VPC Dashboard** > **Your VPCs** > **Create VPC**.
3. Fill in the details:
   - **Name tag**: `eks-vpc`
   - **IPv4 CIDR block**: `10.0.0.0/16`
   - **Tenancy**: Default
4. Click **Create VPC**.

---

## Step 3: Create Subnets
1. Go to **Subnets** > **Create subnet**.
2. Add two subnets:
   - **Subnet 1**:
     - **Name**: `eks-subnet-1`
     - **VPC**: Select `eks-vpc`.
     - **Availability Zone**: Select any (e.g., `us-east-1a`).
     - **CIDR block**: `10.0.0.0/24`.
   - **Subnet 2**:
     - **Name**: `eks-subnet-2`
     - **VPC**: Select `eks-vpc`.
     - **Availability Zone**: Another zone (e.g., `us-east-1b`).
     - **CIDR block**: `10.0.1.0/24`.
3. Click **Create subnet**.

---

## Step 4: Create an Internet Gateway
1. Go to **Internet Gateways** > **Create internet gateway**.
2. Name the gateway (e.g., `eks-igw`).
3. Attach it to the VPC:
   - Select the VPC (`eks-vpc`).
   - Click **Attach internet gateway**.

---

## Step 5: Create a Route Table
1. Go to **Route Tables** > **Create route table**.
2. Fill in the details:
   - **Name**: `eks-public-rt`
   - **VPC**: Select `eks-vpc`.
3. Click **Create**.

### Add a Route to the Internet Gateway
1. Select the route table (`eks-public-rt`).
2. Go to the **Routes** tab > **Edit routes**.
3. Add the following route:
   - **Destination**: `0.0.0.0/0`
   - **Target**: Select the internet gateway (`eks-igw`).
4. Save the route.

### Associate Subnets with the Route Table
1. Go to the **Subnet Associations** tab > **Edit subnet associations**.
2. Select `eks-subnet-1` and `eks-subnet-2`.
3. Save the associations.

---

## Step 6: Create a Security Group for EKS
1. Go to **Security Groups** > **Create security group**.
2. Fill in the details:
   - **Name**: `eks-sg`
   - **VPC**: Select `eks-vpc`.
3. Configure inbound rules:
   - Allow SSH (optional for debugging):
     - **Protocol**: TCP
     - **Port**: 22
     - **Source**: `0.0.0.0/0` (or restrict it to your IP).
   - Allow HTTPS:
     - **Protocol**: TCP
     - **Port**: 443
     - **Source**: `0.0.0.0/0`.
4. Save the security group.

---

## Step 7: Create the EKS Cluster
1. Navigate to **EKS Dashboard** > **Create cluster**.
2. Fill in the cluster details:
   - **Name**: `eks-cluster`
   - **Kubernetes version**: Select the desired version.
   - **Role**: Create or select an IAM role with EKS permissions.
3. Networking:
   - **VPC**: Select `eks-vpc`.
   - **Subnets**: Select both `eks-subnet-1` and `eks-subnet-2`.
   - **Security group**: Select `eks-sg`.
4. Click **Create**.

---

## Step 8: Create Node Groups (Optional for Managed Nodes)
1. In the **EKS Dashboard**, select the cluster.
2. Go to the **Node Groups** tab > **Add node group**.
3. Fill in the details:
   - **Name**: `eks-node-group`.
   - **IAM Role**: Create or use an existing role for EKS nodes.
   - **Subnets**: Select both subnets.
   - **Instance type**: Choose the desired instance type (e.g., `t3.medium`).
4. Click **Create**.

---

## Summary
After completing these steps, your VPC and EKS cluster will be ready for use. You can now deploy workloads on your EKS cluster and manage them using Kubernetes tools.

