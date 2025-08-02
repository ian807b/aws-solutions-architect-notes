# AWS Solutions Architect Foundational Network Knowledge

## IP

- An IP address is a 32-bit number, giving us about 4.3 billion addresses.
- It is composed of a network ID and a host ID.
- IP addresses that share the same network ID belong to the same network.
- For example, `111.111.111.111` is an IP address in decimal.
- The same IP address in binary is `01101111.01101111.01101111.01101111`.
- Each 8-bit group, or octet, has a decimal range from 0 to 255 $(2^8-1)$.
- IP addresses are divided into different classes: A, B, and C. This is done to chop IP addresses for efficient allocation since they are running out.

## IPv4 Classes and Subnet Masks

- **Class A**
  - The left-most 8 bits are the network ID, and the rest are used for hosts.
  - The left-most digit starts with 0.
  - The IP range is `1.0.0.0` to `126.0.0.0`.
  - `0.0.0.0` and `127.0.0.0` are reserved IPs.
  - The subnet mask is `255.0.0.0`.
  - The number of available hosts is $2^{24}-2=16,777,214$.
- **Class B**
  - The left-most 16 bits are the network ID, and the rest are used for hosts.
  - It starts with 10.
  - The IP range is `128.0.0.0` to `191.255.0.0`.
  - The subnet mask is `255.255.0.0`.
  - The number of available hosts is 65,534.
- **Class C**

  - The left-most 24 bits are the network ID, and the rest are used for hosts.
  - It starts with 110.
  - The IP range is `192.0.0.0` to `223.255.255.0`.
  - The subnet mask is `255.255.255.0`.
  - The number of available hosts is 254.

- For each class, 0 and 255 are not used for hosts; they are used as the network identity and for broadcasting. For example, in `192.168.1.0`, `0` identifies the entire network and `255` is for broadcasting.

## Subnet

- A subnet is a smaller network inside a network.
- Subnetting is the process of dividing a larger network into smaller ones (subnets).
- A **Subnet Mask** is a 32-bit number that separates an IP address into the network ID and the host ID.
- The subnet masks for Class A, B, and C are `255.0.0.0`, `255.255.0.0`, and `255.255.255.0`, respectively.
- A **Prefix** is a concise way to represent the subnet mask. For example, `/24` means the subnet mask is `255.255.255.0`.

## More on Subnetting & CIDR Expression

- A company needing 50 IP addresses might receive `192.168.10.0/24`, which allows 254 available hosts.
- The network can be subdivided further.
- Consider `192.168.10.70/26`. This IP address implies it is a Class C network.
- The default subnet mask is `255.255.255.0`.
- The network ID is `192.168.10`, and the host ID is `.70`.
- The Class C range can be subdivided into four pieces:
  - Network 1: `192.168.10.0` to `192.168.10.63`.
  - Network 2: `192.168.10.64` to `192.168.10.127`.
  - Network 3: `192.168.10.128` to `192.168.10.191`.
  - Network 4: `192.168.10.192` to `192.168.10.255`.
- The company's IPs belong to Network 2.
- Another way to interpret `192.168.10.70/26` is by noting that the subnet mask has 26 ones. This is `1111 1111.1111 1111.1111 1111.1100 0000` in binary, which is `255.255.255.192`.
