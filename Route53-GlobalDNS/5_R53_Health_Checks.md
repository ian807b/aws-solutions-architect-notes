# ❤️ Route 53 – Health Checks

## 🧠 Key Concepts

### ✅ What Is a Health Check?

- A **Route 53 resource** that monitors the health of:

  - Publicly accessible endpoints (IP or DNS)
  - Application states via CloudWatch alarms
  - Aggregated check status (calculated checks)

- **Health checks are configured independently** of DNS records, but can be **linked to them**.

---

## 🌍 Global Health Checker Fleet

- Route 53 performs health checks using a **globally distributed fleet**.
- Ensure your endpoints **do not block** these checkers, or you'll get **false negatives**.

---

## 🔍 Types of Health Checks

### 1. **Endpoint Checks**

- Monitor a specific domain name or IP address.
- **Protocols supported**:
  - **TCP** – Ensure port is reachable within 10 seconds.
  - **HTTP/HTTPS** – Confirm:
    - TCP connection is made (4 sec)
    - Response returns 2xx/3xx HTTP code (2 sec)
  - **String Matching** – Check if a specific string is found in the response body.

### 2. **Calculated Checks**

- Combine multiple other health checks.
- Useful for assessing **overall application health**.

### 3. **CloudWatch Alarm Checks**

- Use the status of a CloudWatch Alarm as the health source.
- Enables monitoring of **in-app or system-level metrics**.

---

## ⏱️ Configuration Parameters

| Setting               | Description                                                             |
| --------------------- | ----------------------------------------------------------------------- |
| **Request Interval**  | 30s (default) or 10s (extra cost)                                       |
| **Failure Threshold** | # of failed checks before marking unhealthy                             |
| **Inversion**         | Flip healthy/unhealthy status (rare use case)                           |
| **Latency Graphs**    | Enable visual latency tracking                                          |
| **Search String**     | For string matching in HTTP(S) response (first 5,120 bytes only)        |
| **Notification**      | Integrate with **SNS topics** to notify or trigger responses on failure |

---

## 🌐 Routing Behavior with Health Checks

- When linked to a **record**, Route 53:
  - Continuously checks the target
  - Removes the record from DNS responses if **health check fails**
- A health check is considered **healthy** if:
  - ≥ **18% of checkers report it as healthy**

---

## ✅ Use Cases

- **Failover Routing**: Switch to backup if the primary fails
- **Weighted / Latency Routing**: Only route to healthy resources
- **Monitoring**: Alert via SNS if an endpoint goes down
- **Smart DNS**: Ensure only healthy servers receive traffic

---

## 🔧 Example: Health Check Creation (UI Flow)

1. Go to **Route 53 → Health Checks**
2. Choose **"Create Health Check"**
3. Pick type:
   - Endpoint (IP or domain name)
   - Calculated check
   - CloudWatch Alarm
4. Configure:
   - Protocol (TCP, HTTP, HTTPS)
   - Port and path
   - Optional: search string, request interval, failure threshold
5. (Optional) Add an SNS notification

---
