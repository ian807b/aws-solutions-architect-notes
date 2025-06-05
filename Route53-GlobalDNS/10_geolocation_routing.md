# 🌍 Route 53 Geolocation Routing

## 📌 Overview

**Geolocation Routing** is a DNS routing policy in AWS Route 53 that routes traffic **based on the geographic location of the user making the request**. Unlike latency-based routing, it doesn't consider network latency but relies entirely on user location.

---

## 🌐 Key Concepts

* **User Location Determined by IP**:

  * Route 53 checks the location of the DNS resolver (typically near the user).
* **Record Tagging Options**:

  * **Subdivision**: U.S. states (e.g., `US-CA` for California)
  * **Country**: Using ISO 3166 country codes (e.g., `US`, `DE`)
  * **Continent**: Using ISO codes (e.g., `NA` for North America, `EU` for Europe)
  * **Default**: Catch-all record if no match is found

---

## 🔁 Routing Logic

When a DNS query is made, Route 53 follows this sequence:

1. **Check Subdivision (U.S. State)**

   * Return the matching record if found
2. **Check Country**

   * Return if a country-specific record exists
3. **Check Continent**

   * Return if a continent-level record matches
4. **Use Default Record (if defined)**
5. **Return No Answer** if no match and no default exists

> ⚠️ Geolocation **does not return the closest** record — only relevant ones based on tagging.

---

## 🧪 Example Scenario

You want to serve different content based on where your users are:

| Record Name       | Routing Policy | Location Tag | IP Address      |
| ----------------- | -------------- | ------------ | --------------- |
| `www.example.com` | Geolocation    | `US`         | `192.0.2.10`    |
| `www.example.com` | Geolocation    | `EU`         | `198.51.100.20` |
| `www.example.com` | Geolocation    | `Default`    | `203.0.113.30`  |

* A user in the U.S. will get `192.0.2.10`
* A user in France will get `198.51.100.20`
* A user in Japan will get `203.0.113.30`

---

## 🎯 Use Cases

* Restricting content to specific regions (e.g., GDPR compliance)
* Language-based content delivery
* Geo-targeted promotions and services
* Regional load distribution (without relying on latency)

---

## ❗ Important Notes

* The smallest unit is a **U.S. state** (subdivision)
* If a **state** tag exists and matches, it's returned — otherwise falls back to **country**, then **continent**, then **default**
* No proximity-based logic — purely **tag relevance**
* Use **default** to ensure global coverage

---

## ✅ Summary Table

| Feature                  | Geolocation Routing               |
| ------------------------ | --------------------------------- |
| Basis for decision       | User's IP location                |
| Supports health checks   | ✅ Yes                             |
| Fallback option          | ✅ Default Record                  |
| Closest record returned? | ❌ No                              |
| Granularity              | Subdivision → Country → Continent |

---

Route 53's **Geolocation Routing** is perfect when **compliance, customization, or regional restrictions** matter more than speed. Use it thoughtfully in global deployments.

---
