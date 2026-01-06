# Site-to-Site VPN using Cisco VTI (IPSec)

This guide outlines the configuration of a router-based VPN with IPSec using Cisco’s **Virtual Tunnel Interface (VTI)**.

![Network Topology Diagram]

## 1. Initial Connectivity
The first step is to ensure there is IP connectivity between the two routers. 
* **R1 Interface IP:** `10.0.0.2`
* **R3 Interface IP:** `10.0.0.6`

![Connectivity Verification - Ping Results]

---

## 2. Phase 1: IKE / ISAKMP Policy
Once we have connectivity, we need to configure Phase 1 and 2 of the Internet Key Exchange (IKE) connection. 

On Phase 1, the **ISAKMP policy** must be configured on both routers:

![R1 ISAKMP Configuration]
![R3 ISAKMP Configuration]

> **Note:** All features must match for the Phase 1 tunnel to form, with the exception of the **lifetime** value, which will be negotiated to the lowest value in case of a mismatch.

### Pre-Shared Key (PSK)
Since the chosen authentication method is a pre-shared key, it must be configured on both routers:

![R1 PSK Configuration]
![R3 PSK Configuration]

With this, **Phase 1** of the process is complete.

---

## 3. Phase 2: Transform-Set and Profile
Now, a **transform-set** has to be configured for the second phase of the tunnel. This configuration must match on both routers. We will use **tunnel mode** (as opposed to transport mode).

![R1 Transform-Set Configuration]
![R3 Transform-Set Configuration]

### IPsec Profile
Once the transform set is done, a profile must be created to apply the transform set to, which in turn will be applied to the VTI tunnel:

![R1 IPSec Profile]
![R3 IPSec Profile]

With this, **Phase 2** of the ISAKMP process is completed.

---

## 4. VTI Tunnel Configuration
Now, the **VTI tunnel** has to be created and the profile applied to it.

![R1 Tunnel Interface Configuration]
![R3 Tunnel Interface Configuration]

### Verifying the IPsec Session
We can now observe that there is an active IPSEC session:

![Show Crypto IPsec SA - Part 1]
![Show Crypto IPsec SA - Part 2]

---

## 5. Routing over the Tunnel (OSPF)
With this tunnel configured, routes can be exchanged between the routers using a protocol like **OSPF**. Both routers will form an adjacency as if they were directly connected and exchange their loopback interfaces.

![R1 Routing Table / OSPF Neighbors]
![R3 Routing Table / OSPF Neighbors]

As seen above, the **next hop** is not the physical interface, but the **Tunnel interface**. 

### Final Verification
The loopback interfaces can now ping each other successfully:

![Final Ping Test Success]