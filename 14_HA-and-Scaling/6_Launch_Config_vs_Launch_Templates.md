# EC2: Launch Configurations vs. Launch Templates

Two features of EC2: **Launch Configurations** and **Launch Templates**. While they serve a similar purpose, Launch Templates are the newer, more capable version.

---

## What They Do

At a high level, both Launch Configurations and Launch Templates allow you to pre-define the configuration of EC2 instances. They are documents that specify all the settings you would normally configure when launching an instance manually.

### Configurable Parameters

* **AMI** to use
* **Instance Type** and size
* **Storage** configuration
* **Key Pair** for SSH access
* **Networking** configuration (e.g., VPC, subnets)
* **Security Groups**
* **User Data** scripts
* **IAM Role** to attach to the instance

---

## Key Characteristics and Differences

### Immutability
Both `Launch Configurations` and `Launch Templates` are **not editable** after creation. Launch Templates, however, support versioning, allowing you to create new versions of a template to update the configuration.

### Launch Configurations (Legacy)
* **No Versioning**: If you need to change the configuration, you must create an entirely new launch configuration.
* **Limited Use Case**: Their only purpose is to define EC2 instances for **Auto Scaling Groups**.

### Launch Templates (Recommended by AWS)
* **Supports Versioning**: You can create multiple versions of a single template.
* **Superset of Features**: Includes all features of launch configurations plus additional, newer capabilities.
* **Additional Features Include**:
    * T2/T3 Unlimited CPU options
    * Placement Groups
    * Capacity Reservations
    * Elastic Graphics
* **Broader Use Cases**:
    * Can be used with **Auto Scaling Groups**.
    * Can be used to launch EC2 instances directly from the AWS Console or CLI.

AWS recommends using **Launch Templates** as they provide more features and flexibility.