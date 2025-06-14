# Event-Driven Architecture: Part 2 

## Evolving Architecture with Queues

The previous architecture can be improved by using **queues**.
* A queue is a system that **accepts messages**.
* Messages are **sent onto** a queue and **received/polled off** the queue.
* Often, queues have **ordering**, typically **FIFO (First In, First Out)**, though not always.

### CatTube with a Queue-Based Decoupled Architecture

Let's revisit CatTube:
1.  **Bob uploads** his video of Whiskers to the **upload component**.
2.  Once complete, instead of direct communication with the processing tier:
    * The master 4K video is stored in an **S3 bucket**.
    * A **message** is added to the queue, detailing the video's S3 location and required sizes.
3.  The upload tier's transaction is **finished**. It doesn't directly communicate with or care about the processing tier's state.
4.  The key is **asynchronous communication**: The upload tier sends the message and can continue other tasks without waiting for an immediate response from the processing tier.
5.  Other video uploads will add messages behind Bob's job in the FIFO queue.

### How the Processing Tier Works with Queues

* On the other side of the queue, an **Auto Scaling Group** is configured:
    * Minimum size: **0**
    * Desired size: **0**
    * Maximum size: **1,337**
* Currently, no instances are provisioned.
* **Auto scaling policies** provision or terminate instances based on the **queue length** (number of items in the queue).
* When messages are added to the queue, the ASG detects this and:
    * Increases desired capacity (e.g., from 0 to 2).
    * Provisions instances.
* These processing instances start **polling the queue** and receiving messages (which contain job data and S3 object locations).
* Instances retrieve the master video from S3, process the jobs, and then **delete the messages from the queue**.
* As the queue length shortens, the ASG scales back down (e.g., from 2 to 1, then from 1 to 0), terminating instances until the queue is empty and no instances are running.

### Benefits of Queue-Based Decoupling:
* **Decoupling**: One tier (upload) adds jobs and doesn't care about the health of the other (processing); the other tier reads jobs and doesn't care how they got there.
* **No direct communication**: Unlike load-balanced tiered architectures where synchronous communication still occurred, here there's no direct communication.
* **Independent, free scaling**: Components can scale independently. The processing tier (a worker fleet) can scale from **zero** to a near-infinite number of instances based **only on the queue length**.
* **Powerful architecture**: Common for applications like CatTube where uploads require processing by a scalable worker fleet.

---

## From Monolith to Microservices

If you continue breaking down a monolithic application into smaller and smaller pieces, you arrive at a **microservice architecture**.
* A microservice architecture is a collection of **microservices**.
* **Microservices** do individual things very well (e.g., upload microservice, processing microservice, store and manage microservice).
* A full application like CatTube might have hundreds or thousands of microservices.

### Producers, Consumers, and Events in Microservices
* **Producers**: Microservices that **produce data or messages** (e.g., upload service).
* **Consumers**: Microservices that **consume data or messages** (e.g., processing service).
* Some microservices can be **both** producers and consumers (e.g., data, store and manage microservice).
* The "things" that services produce and consume architecturally are **events**.
* Queues can be used to communicate events, but larger microservice architectures can become complex with too many queues if every service needs a direct queue for every partner service.
* A microservice is a tiny, self-sufficient application with its own logic, data store, and I/O components.

---

## Event-Driven Architectures

* It's a collection of:
    * **Event Producers**: Components (application, infrastructure like EC2, monitoring systems) that **generate/produce events** in reaction to something.
        * Examples: Customer clicks "submit," an error occurs (e.g., during order packing, video upload).
    * **Event Consumers**: Pieces of software **ready and waiting** for events to occur.
        * If they see an event they care about, they take an action (e.g., display something, dispatch a human, retry an upload).
* Components/services can be **both producers and consumers** (e.g., generate a "failed upload" event, then consume an event to retry).

### Key Characteristics of Event-Driven Architectures:
* Neither producers nor consumers are constantly consuming resources or waiting around for things.
* **Producers**: Events are generated **only when something happens** (button click, successful/failed upload).
* **Consumers**: Events are **delivered to them**. They take an action when an event is received, then **stop** (don't constantly consume resources).

### The Role of the Event Router
* To avoid complex architectures with a queue between every component, best practice event-driven architectures use an **event router**.
* An **event router** is a highly available, central exchange point for events.
* It has an **event bus** – a constant flow of information.
* Producers add events to this event bus.
* The router delivers these events to relevant event consumers.

### Resource Consumption in Event-Driven Architectures:
* Contrast with traditional systems like a WordPress instance on EC2, which consumes resources consistently regardless of load.
* In an event-driven system with many small services waiting for events:
    * The system **springs into action** when events are received.
    * It allocates resources and scales components up as required.
    * It deals with the events.
    * It then **returns to low or no resource usage** (its default state).
* Event-driven architectures **only consume resources as and when required**.
* Generally, there's nothing constantly running or polling for things.
* Producers generate events when specific actions occur (clicks, errors, criteria met, uploads complete).
* Events are delivered to consumers via an event router, which decides which consumers receive which events.
* Consumers take action, and then the system goes into a **dormant state**, not consuming resources.

---