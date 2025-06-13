# EC2 Auto Scaling Groups (ASG)

EC2 Auto Scaling Groups (ASGs) automatically adjust the number of EC2 instances in a group to meet performance demands and can also implement self-healing to replace failed instances. ASGs are typically used with **Elastic Load Balancers (ELB)** and **Launch Templates**.

---

## Core Function & Configuration

An ASG's primary job is to ensure that the number of running EC2 instances matches a defined capacity. It achieves this by launching or terminating instances as needed.

* **Instance Definition**: An ASG uses a `Launch Template` (recommended) or a legacy `Launch Configuration` to know *what* kind of EC2 instance to provision. All instances launched by the ASG will share this same base configuration.
* **Self-Healing**: The ASG monitors instance health. If it finds an unhealthy instance, it terminates it and launches a new one to replace it.

### Key Capacity Settings
An ASG is configured with three crucial values:
* **Minimum Size (`min`)**: The smallest number of instances the group will maintain. The ASG will never go below this number.
* **Desired Capacity (`desired`)**: The number of instances the ASG aims to have running at any given time. **Scaling policies work by adjusting this value.**
* **Maximum Size (`max`)**: The largest number of instances the group can scale out to. The ASG will never exceed this number.

> **Example**: A configuration of `min: 1`, `desired: 2`, `max: 4` means the ASG will start by running 2 instances, will never have fewer than 1, and will never have more than 4. If an instance fails, the ASG will launch a replacement to get back to the desired capacity of 2.

### Architectural Placement (The "Where")
* An ASG is linked to a specific VPC.
* You configure the ASG with one or more subnets within that VPC.
* The ASG launches instances into these specified subnets.
* It automatically attempts to **balance the number of instances evenly across the Availability Zones** associated with the configured subnets to increase availability.

---

## Scaling Policies: Adjusting Capacity

Scaling policies are the rules that automate the process of changing the `Desired Capacity` of the ASG.

### 1. Manual Scaling
You manually change the `min`, `max`, or `desired` values in the console or via the CLI. The ASG then performs the necessary actions (launching or terminating instances) to match the new `desired` capacity.

### 2. Scheduled Scaling
This policy adjusts the `desired` capacity based on a predictable schedule.
* **Use Case**: Ideal for applications with known traffic patterns, such as scaling out during business hours and scaling in at night, or preparing for a scheduled marketing event.
* For the exam, if a question mentions predictable or known usage patterns, **Scheduled Scaling** is often the correct answer.

### 3. Dynamic Scaling
These policies react to changes in performance metrics, most commonly by using Amazon CloudWatch alarms.

#### Dynamic Scaling Types:
* **Simple Scaling**:
    * Triggered when a metric crosses a threshold (e.g., "If average CPU > 70%, add 1 instance").
    * A cooldown period is enforced after a scaling activity to prevent it from launching or terminating more instances before the effects of the last change are clear.

* **Step Scaling**:
    * An improvement on Simple Scaling. It allows you to define multiple "steps" for a response.
    * The size of the scaling response can vary based on the size of the alarm breach.
    * **Example**: "If CPU is 70-80%, add 1 instance. If CPU is > 80%, add 3 instances."
    * This allows for a more aggressive response to sudden spikes and is almost always preferable to Simple Scaling.

* **Target Tracking Scaling**:
    * The simplest to configure. You define a target value for a specific metric, and the ASG automatically adjusts the instance count to keep the metric at (or near) that target.
    * **Example**: "Maintain average CPU Utilization at 40%." The ASG will handle adding or removing instances to stay at that level.
    * **Supported Metrics Include**:
        * Average CPU Utilization
        * Average Network In / Out
        * Application Load Balancer Request Count Per Target

---

## Health Checks & Self-Healing

ASGs automatically monitor the health of the instances they manage.

* **Default Health Check**: Uses **EC2 Status Checks** (`system` and `instance`). If an instance fails these checks, the ASG considers it unhealthy and replaces it.
* **ELB Health Checks**: When an ASG is attached to a load balancer, it can be configured to use the ELB's health checks instead. These are often more effective as they are **application-aware** (e.g., checking if an HTTP endpoint returns a `200 OK` status), whereas EC2 checks are not.
    > **Caution**: Be careful with ELB health check configuration. A poorly configured check (e.g., one that depends on an external database) could cause all instances to be marked as unhealthy if the database fails, leading to a mass replacement event when the instances themselves were fine.
* **Simple High Availability Trick**: Configure an ASG with `min: 1`, `desired: 1`, `max: 1` across subnets in multiple AZs. If the single instance or its AZ fails, the ASG will automatically launch a replacement in a healthy AZ. This is a cheap and effective HA strategy.

---

## Integration with Elastic Load Balancers

The true power of ASGs is realized when they are integrated with an ELB.

1.  An ELB receives incoming traffic and directs it to a **Target Group**.
2.  The ASG is configured to register its instances with that Target Group.
3.  When the ASG **scales out** (launches a new instance), it is **automatically added** to the target group and starts receiving traffic.
4.  When the ASG **scales in** (terminates an instance), it is **automatically removed** from the target group.

This creates a truly **elastic architecture**, where compute capacity scales seamlessly with user demand, and users are completely abstracted from the individual instances.

---

## Other Important Concepts

* **Cooldown Period**: A setting (in seconds) that prevents an ASG from launching or terminating additional instances immediately after a scaling activity. This helps stabilize the environment and avoid excessive costs from rapid scaling "flapping".
* **Suspending Processes**: You can temporarily suspend specific ASG processes for maintenance or troubleshooting. Examples include `Launch`, `Terminate`, `HealthCheck`, `AZRebalance`, and `AlarmNotification`. You can also put a specific instance into a `Standby` state to perform maintenance on it without the ASG terminating it.

## Key Exam Takeaways
* **Cost**: Auto Scaling Groups are **free**. You only pay for the underlying resources they create (e.g., EC2 instances, EBS volumes).
* **Cost-Effectiveness**:
    * Use **cooldown periods** to prevent rapid, unnecessary scaling actions.
    * Using **more, smaller instances** provides better granularity. Scaling in smaller steps is more cost-effective than scaling with large, expensive instances.
* **Elasticity**: The combination of an **ASG + Application Load Balancer** is the classic pattern for elasticity on AWS.
* **Separation of Concerns**:
    * An **ASG** controls the **when** (scaling policies) and **where** (VPC subnets/AZs).
    * A **Launch Template** controls the **what** (AMI, instance type, user data, etc.).