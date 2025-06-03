# 🧾 Simple Routing

## 📌 Summary

**Simple Routing** is the default and most basic routing policy in Route 53.  
It is used when you want to associate a **single DNS name with one or more IP addresses** or endpoints **without any health checks or routing logic**.


## 🧠 Key Concepts

### ✅ What It Does

- You create a record in a **hosted zone** (e.g., `www.animals4life.org`)
- The record is of a standard type (e.g., A, AAAA, CNAME)
- You can assign **one or more values** (e.g., multiple IP addresses)
- Route 53 will return **all values randomly ordered** in a single DNS response
- The **client chooses one** and connects

