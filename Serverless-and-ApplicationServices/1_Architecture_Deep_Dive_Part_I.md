# Event-Driven Architecture

## Understanding Architectures: The CatTube Example

Let's illustrate with an example: a popular online video sharing platform called **CatTube**.

Imagine **Bob uploads a 4K video of "Whiskers on holiday"** to CatTube.
* CatTube starts processing the video, generating multiple versions (1080p, 720p, 480p).
* Video processing is the most resource-intensive part.
* The website also handles displaying videos, managing playlists/channels, and storing/retrieving data from a database.



---

## Monolithic Architecture

**Monolithic architecture** was common.
* A **single "black box"** containing all application components.
* With CatTube:
    * **Upload component**: Bob uploads videos.
    * **Processing component**: Converts videos.
    * **Store and Manage component**: Interacts with persistent storage.

### Considerations of Monolithic Architecture:
* **Fails together**: If one component fails, it impacts the entire system. A failure in uploading can affect processing and storage.
* **Scales together**: Components are highly coupled and generally expect to be on the same server with the same codebase.
    * Requires **vertical scaling** (increasing the size of the single server).
* **Costs**: Components are always running and incurring charges, even if idle. This is often the **least cost-effective** architecture.

---

## Tiered Architecture

A **tiered architecture** evolves from the monolith by breaking it into separate "tiers."
* Each tier can be on the same or different servers.
* Components are still **completely coupled** because each tier directly connects to a single endpoint of another tier (e.g., upload tier sends data directly to the processing tier).
    * *Example*: WordPress separating the database (RDS) from the EC2 instance running the web server and application. Both still tightly communicate.

### Benefits of Tiered Architecture (vs. Monolith):
* **Independent vertical scaling**: Individual tiers can be scaled independently. If the processing tier needs more CPU, its server can be increased in size without affecting upload or store/manage tiers.

### Evolving Tiered Architecture with Load Balancers

To further improve, we can introduce **load balancers** between tiers.
* This uses **internal load balancers**.
* **Abstraction**: The upload tier no longer communicates with a specific instance of the processing tier; it goes via a load balancer.
* **Horizontal Scaling & High Availability**:
    * Additional processing tier instances can be added.
    * Communication occurs via load balancers, so upload/store/manage tiers don't need to know the processing tier's internal architecture (whether it's one instance or 100).
    * If one instance fails, the load balancer redistributes connections to working instances, making the system **highly available**.
* Each tier can now be scaled **independently**, both vertically and horizontally.

### Limitations of Tiered Architecture (Even with Load Balancers):
1.  **Tiers are still coupled**: The upload tier **expects and requires** the processing tier to exist and respond.
    * If the processing tier fails completely, the upload tier will fail.
    * A backlog or slowdown in processing can impact the upload tier and customer experience.
2.  **Cannot scale down to zero**: Even with no jobs, the processing tier must have something running to avoid failure when the upload tier attempts to add a job.
    * Communication is **synchronous**: The upload tier expects an immediate response from the processing tier.

---