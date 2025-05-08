**What is virtual private cloud?**
A virtual private cloud, or VPC, is a secure, individual, private cloud-computing model hosted within a public cloud – like Google Cloud. On a VPC, customers can run code, store data, host websites, and do anything else they could do in an ordinary private cloud, but this private cloud is hosted remotely by a public cloud provider. This means that VPC's combine the **scalability** and convenience of public cloud computing with the **data isolation** of private cloud computing.
![[Pasted image 20250508095405.png]]


**VPC Routing**
Google Cloud routes **define the paths that network traffic takes from a virtual machine (VM) instance to other destinations**. These destinations can be inside your Google Cloud Virtual Private Cloud (VPC) network (for example, in another VM) or outside it.

## GCP Cloud Load Balancers

- **Application (Proxy NLB):** Operates at the application layer (Layer 7), understanding content (like HTTP) to make routing decisions. The LB acts as a proxy, terminating client connections.
- **Network (Passthrough NLB):** Operates at the network layer (Layer 4), forwarding TCP/UDP traffic based on IP and port without modifying headers, preserving the client's IP to the backend.
- **Network (Proxy NLB):** Operates at the network layer (Layer 4) but acts as a proxy, terminating client TCP connections and establishing new ones to the backend. The backend sees the LB's IP.