# AWS CloudWatch Events and EventBridge

EventBridge provides a near real-time stream of system events describing changes in AWS products and services.

## CloudWatch Events Overview
* **Focus**: Delivers near real-time streams of **system events**.
* Events describe changes in AWS products and services (e.g., an EC2 instance being terminated, started, or stopped generates an event).
* Supported AWS products and services generate events that CloudWatch Events can see.

## EventBridge: The Successor
* **EventBridge is replacing CloudWatch Events**.
* It provides a **superset of functionality** compared to CloudWatch Events.
* **Key additions in EventBridge**: Can handle events from **third parties** and **custom applications**, not just AWS services.
* Both services share the same basic underlying architecture and APIs.
* **AWS encourages migration to EventBridge** for any new deployments.
* Things created in one are visible in the other *for now*, but this could change. Best practice: **start using EventBridge by default**.

## Core Functionality: "If X Happens, Then Do Z" or "If Something Happens at a Certain Time, Then Do Z"
* **X**: A supported service generates an event (a producer of an event).
* **Y**: A certain time or time period, specified using the **Unix Cron format** (flexible for scheduling).
* **Z**: A supported target service to deliver the event to.

## Event Buses
* Both EventBridge and CloudWatch Events operate using an **event bus**.
* Each AWS account has a **default event bus**.
* An event bus is a **stream of events** from any supported service within that AWS account.
* **CloudWatch Events**: Only **one** default event bus is available; it's implicit and not exposed in the UI.
* **EventBridge**: You can create **additional event buses** for your applications or third-party products/services, and interact with them in the same way as the default bus.

## Rules
* With CloudWatch Events and EventBridge, you create **rules** that:
    * **Pattern match events** occurring on the event buses.
    * When a match occurs, they **deliver that event to a target**.
* **Two types of rules:**
    1.  **Pattern Matching Rules**: Match specific patterns within the event's data itself as it passes through the event bus.
    2.  **Schedule-Based Rules**: Match a certain date/time or range, defined using a **Cron expression**. The rule executes when the time matches.
* When a rule executes (either by matching an event pattern or a schedule), it delivers the event to **one or more defined targets**.
* An example target is to **invoke a specific Lambda function**.

## How EventBridge (and CloudWatch Events) Works Architecturally
1.  At the heart is the **default account event bus**, a stream of events from supported AWS services.
2.  **Example**: Bob changes the state of an **EC2 instance** from `stopped` to `running`.
3.  When the instance changes state, an **event is generated** and passes through the event bus.
4.  **EventBridge monitors all events** passing through its exposed event buses.
5.  **Rules** within EventBridge (or CloudWatch Events) are linked to a specific event bus (default is the account default).
6.  When a rule (either pattern-matching or scheduled) is executed, it delivers the matched event to **one or more targets** (e.g., invoking a Lambda function).
7.  **Events are JSON structures**. The target service (e.g., Lambda) receives this JSON data, which includes details like which instance changed state, the new state, and the timestamp.

## Summary
* CloudWatch Events and EventBridge serve as a **central point for managing events** generated inside an AWS account and controlling actions based on those events.