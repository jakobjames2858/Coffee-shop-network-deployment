## Cisco IOS Configuration Breakdown (`Coffeeshop-SW1`)

The local coffee shop switch was configured using enterprise-grade deployment standards. Below is a detailed breakdown of the commands implemented, their technical functions, and the custom security enhancements added to improve the baseline topology.

---

### 1. Device Management & Hardening

*   **`enable secret`**
    *   **Technical Function:** Encrypts and protects the privileged EXEC mode access using a secure hashing algorithm.
    *   **Purpose:** Prevents unauthorized local users from entering configuration mode and altering device settings.
*   **`service password-encryption`**
    *   **Technical Function:** Scrambles plain-text passwords (such as console or line passwords) into a Type-7 encrypted format within the configuration file.
    *   **Purpose:** Protects administrative credentials from being read in clear text during configuration reviews or accidental text leaks.
*   **`banner motd`**
    *   **Technical Function:** Establishes a custom Message of the Day (MOTD) banner that displays instantly upon terminal connection.
    *   **Purpose:** Provides a clear legal warning stating that unauthorized access is strictly prohibited.
*   **`line console 0` & `password`**
    *   **Technical Function:** Enters the physical console port configuration interface and maps a local authentication password to it.
    *   **Purpose:** Secures the switch from unauthorized administrative access via a direct, physical console cable connection.
*   **`logging synchronous`**
    *   **Technical Function:** Synchronizes unsolicited IOS terminal alerts with active user keyboard command-line inputs.
    *   **Purpose:** Prevents system logs and diagnostic messages from interrupting or splitting up text while an administrator is actively typing commands.

---

### 2. Layer 2 Segmentation & Port Security

#### VLAN 10: Management Office
*   **Purpose:** Isolates back-office administrative computers and corporate office devices.
*   **Interfaces:** `FastEthernet 0/1` through `0/5`.
*   **Configuration:** Ports set to static access mode (`switchport mode access`) and explicitly bound to VLAN 10 (`switchport access vlan 10`).
*   **STP Optimization:** `spanning-tree portfast` enabled to bypass standard Listening/Learning delays, granting immediate network connectivity to office hardware.
*   **Hardening (Not done in the video):** 
    *   `spanning-tree bpduguard enable` was explicitly deployed to prevent network loops and topology manipulation if an unauthorized switch or router is connected to an office port.
    *   Unused assigned interfaces (`Fa0/3` - `Fa0/5`) were put into an administrative `shutdown` state as an industry best practice to prevent rogue physical access.

#### VLAN 20: Point of Sale (POS) Systems
*   **Purpose:** Dedicates an isolated network segment strictly for cash registers and credit card transaction devices. 
*   **Interfaces:** `FastEthernet 0/6` through `0/10`.
*   **Configuration:** Interfaces locked down to static access mode and assigned directly to VLAN 20.
*   **STP Optimization:** PortFast enabled to ensure financial endpoints connect and acquire DHCP addressing instantly upon boot.
*   **Hardening (Not done in the video):** 
    *   **BPDU Guard** was deployed to safeguard financial endpoints from loop-inducing hardware.
    *   Unused ports within the block (`Fa0/8` - `Fa0/10`) were administratively **shut down** to eliminate physical tampering vulnerabilities in customer-facing areas.

#### VLAN 30: Guest Wi-Fi
*   **Purpose:** Allocates an isolated segment dedicated exclusively to public customer wireless traffic.
*   **Interfaces:** `FastEthernet 0/11` (Connecting to the physical Wireless Access Point).
*   **Configuration:** Configured as a static access port on VLAN 30 with PortFast active, allowing the wireless access point to join the forwarding state instantly.

####  VLAN 99: Network Management SVI
*   **Purpose:** Creates a dedicated, non-default virtual management segment to securely administer local infrastructure hardware.
*   **Configuration:** Configured via `interface Vlan99` with a static IP address (`192.168.99.2 /24`) and default gateway to enable remote administrative visibility and control plane traffic across subnets.

#### Unused Interfaces Hardening
*   **Hardening (Not done in the video):** All remaining unused interfaces (`Fa0/12` through `Fa0/24` and `Gi0/2`) were manually targeted with a `shutdown` command to completely eliminate vulnerabilities.

---

### 3. Core Uplink Trunk & Remote Access (SSH)

*   **High-Speed Trunk Port (`GigabitEthernet 0/1`)**
    *   **Technical Function:** Configured as a formal 802.1Q trunk link (`switchport mode trunk`).
    *   **Purpose:** Acts as a high-speed multi-lane highway, allowing isolated traffic streams from VLANs 10, 20, 30, and 99 to travel securely to the core router across a single physical link using frame tags (`dot1q`).
*   **Secure Remote Shell (SSHv2) Implementation**
    *   **Technical Function:** Configured secure virtual lines (`line vty 0 15`) to restrict access exclusively to encrypted SSHv2 traffic while completely disabling cleartext Telnet.
    *   **Purpose:** Secures remote management across the network by establishing an internal domain name (`Coffeeshop.local`), generating RSA cryptographic key pairs, and creating a dedicated local administrator profile (`username admin privilege 15`).
 

### Verification & Output Validation

To verify that the Layer 2 configuration was successfully applied and that all local interfaces are operating as expected, the following Cisco IOS verification commands were executed:

<details>
<summary><b>Click to View Verification Screenshots</b></summary>

#### 1. VLAN Verification (`show vlan brief`)
This output confirms that all functional VLANs (10, 20, 30) were properly created in the database and that the respective access interfaces were successfully mapped to their isolated domains.

![Cisco IOS Show VLAN Brief Output](./Image/show-vlan-brief.png)

#### 2. Interface Status Verification (`show ip interface brief`)
This output verifies the Layer 3 Switched Virtual Interface (SVI) for the Management VLAN 99 is assigned the correct IP address (`192.168.99.2`) and that all unused physical ports are successfully in an `administratively down` state.

![Cisco IOS Show IP Interface Brief Output](./Image/show-ip-interface-brief.png)

</details>

## Verification & Security Validation

To verify that the infrastructure operates exactly as intended, the coffee shop network was subjected to simulated perimeter security testing. **Before** the final administrative port shutdown commands were applied to the unused interfaces).


<details>
<summary><b> Click to View Live BPDU Guard Security Testing</b></summary>

#### Live Security Testing: BPDU Guard Enforcement
To validate our perimeter security hardening, a rogue switch was maliciously attached to a secure customer-facing access port.

*   **Topology Impact:** The switch instantly detected incoming foreign Bridge Protocol Data Units (BPDUs). To prevent a network loop or unauthorized Spanning Tree takeover, **BPDU Guard** immediately auto-tripped and forced the interface into a secure `err-disabled` shutdown state.
    
    ![BPDU Guard Topology Simulation](./Image/bpduguard-topology.png)

*   **Cisco IOS Console Error Logs:** Running verification commands displays the precise administrative shutdown sequence generated by the operating system, confirming the port was locked down defensively:
    
    ![BPDU Guard Console Output](./Image/bpduguard-error-output.png)

  This simulation why BPDU Guard is a critical operational safeguard. If a future network administrator troubleshooting an endpoint re-enables a physical switchport without realizing `spanning-tree portfast` is globally active on it, any accidental loop or rogue hardware extension would immediately crash the local network. 


</details>

