# Stateful vs Stateless Firewalls

## How TCP/IP Connections Work

### TCP/IP Layer Summary
- **IP (Layer 3)**: Moves packets across networks; includes **source and destination IPs**
- **TCP (Layer 4)**: Adds reliability and **port numbers**
  - Example: HTTP → TCP 80, HTTPS → TCP 443

### TCP Connection Structure
Every TCP connection involves two parts:
1. **Request**: Client → Server  
   - Source: Ephemeral port on client (e.g., 1024–65535)  
   - Destination: Well-known port on server (e.g., 443)
2. **Response**: Server → Client  
   - Source: Well-known port (e.g., 443)  
   - Destination: Client's ephemeral port

> The connection direction is **perspective-dependent**:
> - On client: request = outbound, response = inbound  
> - On server: request = inbound, response = outbound

---

## Example: Bob and the Catagram Server

### Request Phase:
- From: `Bob (119.18.36.73):ephemeral`  
- To: `Catagram (1.3.3.7):443`

### Response Phase:
- From: `Catagram (1.3.3.7):443`  
- To: `Bob (119.18.36.73):ephemeral`

> Is this traffic **inbound or outbound**? **Depends on perspective!**

---

![Stateless](/24_Diagrams/StatefulvsStateless.png)

---

## Stateless Firewalls

### Definition:
- **No memory of previous packets**
- Treats **request and response as separate flows**
- **Requires two rules per connection** (one per direction)

### Example:
1. **Bob → Catagram (request)**  
   - From Bob's ephemeral → To Catagram 443  
   - **Inbound rule** required (from Bob to Catagram)
2. **Catagram → Bob (response)**  
   - From 443 → To Bob's ephemeral port  
   - **Outbound rule** required

### Reverse Example (Software Updates):
- **Catagram → Update Server** (outbound request)  
- **Update Server → Catagram** (inbound response)

> You need:
> - **Outbound rule** for request
> - **Inbound rule** for response

### Stateless Firewall Challenges:
- Must manage **two rules per logical connection**
- Cannot predict ephemeral port → must **allow wide port ranges**
- **High management overhead**

---

## Stateful Firewalls

### Definition:
- **Tracks connection state**
- **Automatically allows** return traffic (response) once request is allowed
- Only need **one rule per connection**

### Benefits:
- **Lower complexity**
- **No need to open ephemeral ports**
- Reduces misconfigurations

### Example:
1. **Bob → Catagram**  
   - Allow inbound request → response allowed automatically
2. **Catagram → Update Server**  
   - Allow outbound request → inbound response auto-allowed

> Best practice: Allow only the **request direction**  
> Response is **implicitly allowed** if matched to a tracked request

---

