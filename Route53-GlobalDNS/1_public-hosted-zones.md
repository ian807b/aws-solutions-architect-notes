# 🧾 Route53 Public Hosted Zones

## 🌐 Overview

-   **Hosted Zone**: A DNS database for a domain (e.g. `animals4life.org`) hosted in Route 53.
    
-   **Two types**:
    
    -   **Public Hosted Zone**: Accessible from the public internet and VPCs.
        
    -   **Private Hosted Zone**: Accessible only within associated VPCs.
        

----------

## 📌 Key Concepts

### ✅ What is a Hosted Zone?

-   A **hosted zone** is a DNS database that is **stored and managed in Route 53**.
    - A collection of DNS records for a specific domain.

-   You can create a hosted zone in one of two ways:
    - **Automatically**, when registering a domain via Route 53.
    - **Manually**, if your domain is registered with a third-party (e.g., GoDaddy) but you want Route 53 to manage DNS for it.

-   Each hosted zone incurs:
    - A **monthly hosting fee**
    - A **small per-query fee**
        

### ✅ Global Resilience

-   Route 53 name servers are **distributed globally**.
    
-   Each server has **the same dataset** — resilient to regional outages.
    

----------

## 🔧 DNS Record Types

-   Common types:
    
    -   `A` record (IPv4)
        
    -   `AAAA` record (IPv6)
        
    -   `MX` (Mail Exchange)
        
    -   `NS` (Name Server)
        - Specifies the **authoritative name servers** for a domain.  
        - These servers are responsible for answering DNS queries for that domain.  
        - Used to delegate a domain to a specific DNS hosting service (e.g., Route 53).
        
    -   `TXT` (Text)
        

----------

## 🛠 Public Hosted Zone Details

-   A **zone file** the complete set of DNS records for a domain, written in a standardized text format.
    - It's the entire collection of DNS records.
    - For example,
        ```text
            example.com         A       192.0.2.10     
            www.example.com     CNAME   example.com  
            example.com         MX      10 mail.example.com
        ```
    
-   Accessible via:
    
    -   The **public internet**
        
    -   **AWS VPCs** (via Route 53 resolver if DNS resolution is enabled)
        

----------

## 🧩 Delegation & Authority

-   When you register a domain, NS records are added to the **TLD (Top Level Domain)** zone (e.g., `.org`) to **delegate authority** to Route 53’s name servers.
    
-   These name servers become **authoritative** for the domain.
    

----------

## 🔗 External Domain Integration

-   You can register a domain via third-party registrars (e.g., GoDaddy, Hover).
    
-   Then:
    
    1.  Create a public hosted zone in Route 53.
        
    2.  Retrieve the 4 name servers from Route 53.
        
    3.  Set these NS records in the registrar’s interface.
        
-   Route 53 then becomes authoritative for that domain.
    

----------

## 📶 Access Architecture

### From Inside a VPC

-   EC2 instances can access public hosted zones via the **Route 53 resolver**.
    
-   Resolver uses VPC’s DNS settings (e.g., `.2` IP address).
    

### From Public Internet

-   Standard DNS resolution applies:
    
    1.  Resolver queries **root servers**
        
    2.  Root points to **TLD servers** (e.g., `.org`)
        
    3.  TLD returns **NS records** for `animalsforlife.org`
        
    4.  These point to **Route 53 name servers**
        

> This is known as **walking the DNS tree**.
