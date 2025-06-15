# Advanced EC2

## Bootstrapping EC2 using User Data

- **Bootstrapping**: Automating the setup process of an EC2 instance.
  - Executes scripts or configuration on **first launch only**.
  - Used to install software and perform initial setup tasks.

- **User Data**:
  - Passed at instance launch.
  - Executed once, as root, via metadata endpoint:  
    `http://169.254.169.254/latest/user-data`

> ⚠️ User data does **not** re-run if the instance is restarted or updated.

- **Bootstrapping Flow**:
  1. Launch EC2 with an AMI.
  2. Attach EBS via block device mapping.
  3. EC2 passes user data script to the OS.
  4. OS checks metadata endpoint and runs the script.

> ⚠️ If the user data fails, the instance still boots but may be misconfigured.

### Security & Risk
- No validation—EC2 just runs the data as-is.
- Runs as **root** — dangerous if misused.
- Not secure — accessible to anyone with OS access.
- 16 KB size limit.

### Boot Time vs. Service Time
- **Boot Time**: Time for AWS to provision the instance.
- **Post-Launch Time**: Time for setup scripts (e.g., installing software).
- **Service Time** = Boot + Post-Launch.

> 💡 You want to minimize service time for faster deployments.

---

## Reducing Post-Launch Time

### Bootstrapping
- Script-based setup.
- Automated, no human delay.

### AMI Baking
- Create a pre-configured AMI with software and settings.
- Combine both:
  - **Bake** time-consuming installs.
  - **Bootstrap** quick configs.

---

## Bootstrapping with `cfn-init`

- CloudFormation supports **state-driven** configuration.
  - It ensures the infrastructure reaches a **desired state**, not just run commands.

- **cfn-init**:
  - EC2 helper script for CloudFormation.
  - Pulls metadata from the stack and applies it (e.g., installs Apache).
  - Uses `AWS::CloudFormation::Init` section.

- **Flow**:
  1. Launch stack with EC2.
  2. User data calls `cfn-init`.
  3. `cfn-init` fetches metadata.
  4. Config is applied.
  5. `cfn-signal` sends success/failure back to CloudFormation.

> 🔁 Use `CreationPolicy` to make CloudFormation wait for `cfn-signal`.

---

## EC2 Instance Roles & Instance Profile

- **Instance Role**: IAM role assumed by an EC2 instance.
  - Grants apps temporary access to AWS services.

- **Instance Profile**:
  - A container for the role.
  - Automatically created in Console (CLI/CFN: must create manually).

- **Credentials**:
  - Provided via instance metadata.
  - Auto-rotated by EC2 using Secure Token Service (STS).

> 💡 Never hard-code credentials in EC2 apps — always use instance roles.

---

## SSM Parameter Store

- Secure way to store config data (vs. unsafe user data).
- **Types**:
  - `String`: Plain text.
  - `StringList`
  - `SecureString`: Encrypted with KMS.

- Versioning support: enables rollback.
- IAM integration for access control.

> ✅ Use SSM Parameter Store for environment variables and secrets.

---

## EC2 Logging with CloudWatch

- **CloudWatch Logs** stores and visualizes logs.
- **CloudWatch Agent**:
  - Installed in the instance.
  - Collects OS metrics + logs.
  - Sends data to CloudWatch.

- **IAM Role** required to grant CloudWatch access.

> 🛠️ Can automate setup using CloudFormation, User Data, or SSM.

---

## EC2 Placement Groups

Placement groups control how EC2 instances are physically placed within an AZ.

### Types of Placement Groups

| Type      | Goal                | Key Feature                                |
|-----------|---------------------|---------------------------------------------|
| Cluster   | Performance         | Instances close together on same rack       |
| Spread    | High availability   | Instances far apart across racks or AZs     |
| Partition | Large-scale apps    | Isolated groups across partitions/racks     |

---

### Cluster Placement Group
- High throughput + low latency.
- Same rack or host.
- Up to 10 Gbps bandwidth (requires **enhanced networking**).
- Single AZ only.
- Lower fault tolerance.

> 🧪 Use for HPC or apps needing fast node-to-node communication.

---

### Spread Placement Group
- Maximize **resilience**.
- Instances placed across **different racks**.
- Supports multiple AZs.
- Limited to **7 instances per AZ**.
- No support for Dedicated Instances/Hosts.

> 🔒 Use when you need strict instance isolation.

---

### Partition Placement Group
- Designed for **large distributed systems** with replication.
- Up to **7 partitions per AZ**.
- Each partition is isolated — failure in one doesn’t affect others.
- **Topology-aware** apps benefit most.

> 💡 Helps intelligent replication across failure boundaries.

---

## Dedicated Hosts

- A physical server **dedicated to your AWS account**.
  - Rent entire host — pay **per host**, not per instance.

### Billing
- **On-Demand**: Flexibility, short-term.
- **Reserved**: 1 or 3 years, cheaper with commitment.

### Use Cases
- Bring-your-own-license (e.g., Oracle, SQL Server).
- Software licensed by socket/core.

> 🏢 Can share host with other accounts in your AWS Organization.

---

## Enhanced Networking & EBS-Optimized

### Enhanced Networking
- Improves:
  - Throughput
  - Latency
  - Packets per second (PPS)

- Uses **SR-IOV** for direct access to physical NICs.
- Reduces CPU overhead.
- Required for **Cluster Placement Groups**.

> 🚀 Enabled by default on modern instance types and free to use.

---

### EBS-Optimized Instances
- Separate bandwidth for EBS vs. network traffic.
- Faster, more reliable block storage.
- Better IOPS and throughput.

> 📦 Most current instance types support this by default, no extra cost.

