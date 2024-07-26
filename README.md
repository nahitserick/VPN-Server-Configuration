# VPN-Server-Configuration-with-pfSense

## Objective
The objective of this lab was to configure a VPN server using pfSense to secure remote access to a network. The focus was on setting up IPsec and OpenVPN VPNs to allow remote clients to securely connect to network resources.

### Skills Learned
- Understanding of VPN concepts and configurations.
- Setting up and managing VPN servers using pfSense.
- Enhanced knowledge of encryption protocols (IPsec and OpenVPN).
- Ability to configure secure remote access for users.
- Practical experience with VPN server configuration and firewall rules.

### Tools Used
- pfSense
- IPsec
- OpenVPN
- Windows Server 2019

## Steps
**Part 1: Configure an IPsec VPN Server
**

1.Open pfSense Web GUI:
- From the vWorkstation, open Chrome and navigate to the pfSense Web GUI at 172.30.0.1.
- Log in with the credentials admin and pfsense.

2.Create a Certificate Authority (CA):
- Navigate to System > Cert. Manager.
- Click the Add button to create a new CA.
- Fill in the fields: Descriptive name (IPsecVPN_yourname), Method (Create an internal Certificate Authority), Key Length (2048 bits), Digest Algorithm (SHA256), Lifetime (3650 days), and other identifying information.
- Save the CA.

Ref 1: Updated Certificate Authority Table

![image](https://github.com/user-attachments/assets/24b6acb6-e445-41e7-a518-baf9aa6010e3)


3.Create a Server Certificate:
- Go to the Certificates tab and click Add/Sign.
- Fill in the fields: Method (Create an internal Certificate), Descriptive name (IKEv2VPN_pfSense), Certificate Authority (IPsecVPN_yourname), Key Length (2048 bits), Digest Algorithm (SHA256), Lifetime (3650 days), Common Name (202.20.1.1), and Certificate Type (Server Certificate).
- Save the server certificate.

Ref 2: Updated Certificates Table

![image](https://github.com/user-attachments/assets/3a0e1041-9191-4c88-bc0d-4272d31174a3)


4.Enable IPsec Mobile Client Support:
- Navigate to VPN > IPsec.
- Go to the Mobile Clients tab and enable IKE Extensions.
- Set User Authentication to Local Database.
- Enable Virtual Address Pool and set it to 172.31.1.0/24.
- Enable Network List.
- Save and apply the changes.

Ref 3: Updated IPsec Tunnels Table

![image](https://github.com/user-attachments/assets/b0e4bd37-53d6-4bbe-9279-d83f1ba2b313)


5.Create Phase 1 for IPsec:
- Click Create Phase 1.
- Fill in the fields: Key Exchange version (IKEv2), Interface (WAN), Description (MobileIPsec), Authentication Method (EAP-MSChapv2), My identifier (Distinguished name, 202.20.1.1), Peer identifier (Any), My Certificate (IKEv2VPN_pfSense), Phase 1 Proposal (keep defaults).
- Save and apply changes.

6.Create Phase 2 for IPsec:
- Click Show Phase 2 entries and then Add P2.
- Fill in the fields: Local Network (Network, 0.0.0.0/0), Description (MobileIPsec2), Protocol (ESP), Encryption Algorithms (256 bits), Hash Algorithms (SHA1 and SHA256), PFS key group (off).
- Save and apply changes.

Ref 4: Updated IPsec Rules Table

![image](https://github.com/user-attachments/assets/28947a34-d260-46f6-943e-7a45f8259649)


7.Create a Pre-Shared Key:
- Go to the Pre-Shared Keys tab and click Add.
- Fill in the fields: Identifier (remoteworker01), Secret type (EAP), Pre-Shared Key (password1).
- Save and apply changes.

Ref 5: Updated Pre-Shared Keys Table

![image](https://github.com/user-attachments/assets/c4b7a76e-a385-4eff-becf-d3f63d506bbc)


**Part 2: Configure a Firewall Rule for VPN Traffic
**

1.Add a Rule for IPsec Traffic:
- Navigate to Firewall > Rules > IPsec.
- Click the Add button.
- Set Action to Pass, Interface to IPsec, Protocol to Any, and Description to IPsec Open.
- Save and apply changes.

Ref 6: Updated IPsec Rules Table

![image](https://github.com/user-attachments/assets/8799991f-d7b2-42a5-b1ae-da728b6baa0d)

**Part 3: Configure an OpenVPN VPN Server
**

1.Open OpenVPN Wizard:
- Navigate to VPN > OpenVPN > Wizards.
- Select Local User Access and click Next.

2.Create a Certificate Authority for OpenVPN:
- Click Add new CA.
- Fill in the fields: Descriptive name (OpenVPN_CA_yourname), Key length (2048 bits), Lifetime (3650 days), and other identifying information.
- Save the CA.

Ref 7: CA Configuration Form

![image](https://github.com/user-attachments/assets/2988dc2b-077e-4e9b-aeba-2e496c690972)


Ref 8: Tunnel Settings Section

![image](https://github.com/user-attachments/assets/32ee2fd4-faf6-4691-bab0-f7db5bd92616)


Ref 9: Client Settings Section

![image](https://github.com/user-attachments/assets/1364ba0e-be8b-464f-9e1d-c8f7f33044e2)


3.Create a Server Certificate for OpenVPN:
- Click Add new Certificate.
- Fill in the fields: Descriptive name (OpenVPN_SC_yourname), Key length (2048 bits), Lifetime (3650 days), and other identifying information.
- Save the server certificate.

4.Configure OpenVPN Server:
- Fill in the fields: Description (OpenVPN Server), Tunnel Network (172.31.1.0/24), Local Network (172.30.0.0/24), Concurrent Connections (2), Inter-Client Communication (enabled).
- Save and apply changes.

Ref 10: Completed OpenVPN Configuration

![image](https://github.com/user-attachments/assets/694379da-7b2e-48ce-adbe-104b511b6806)


5.Configure Firewall Rules for VPN Traffic:
- Accept the default firewall rules to allow traffic through the OpenVPN server.
- Verify the new rules in Firewall > Rules > WAN and Firewall > Rules > OpenVPN.

Ref 11: OpenVPN rule on the WAN Rules Table

![image](https://github.com/user-attachments/assets/48add6ba-20fd-404a-92a1-86e273a1f607)


Ref 12: OpenVPN rule on the OpenVPN Rules Table

![image](https://github.com/user-attachments/assets/841ccec8-9dc2-41dc-9425-d3acd4cc359f)

**Part 4: Enable IP Roaming for Remote VPN Clients
**

1. Enable Mobility and multihoming protocol (MOBIKE) support for the IPsec tunnel in pfSense
- Connect to the pfSense WebGUI
- Modify the IPsec tunnel configuration enabling MOBIKE support

Ref 13: Enabled MOBIKE option in the IPsec tunnel configuration

![image](https://github.com/user-attachments/assets/27a7db4c-dd19-4c48-80e8-9e8a1d8153c3)

2.Create Explicit Firewall Rules for an IPsec VPN
- Disable IPsec automatic rule creation
- modify the firewall rules to permit IPsec VPN connections
- Connect to the pfSense WebGUI
- Disable IPsec automatic rule creation
- Add required rules to the WAN interface

Ref 14: Disabled automatic IPsec rule creation option

![image](https://github.com/user-attachments/assets/de1f73a5-fd1b-4ba1-9470-f26c91e4feaf)

Ref 15:Firewall rules that permit IPsec traffic

![image](https://github.com/user-attachments/assets/6dc9b9b3-fefb-4aee-8d5b-40c9e788a3e1)

### Key Takeaways
This lab provided valuable hands-on experience in configuring VPN servers using pfSense. By setting up both IPsec and OpenVPN servers, I gained practical skills in securing remote access to network resources. The lab emphasized the importance of using strong encryption protocols and properly configuring firewall rules to ensure secure communications. These skills are crucial for protecting sensitive data and maintaining network security in a real-world environment. The detailed steps in configuring and verifying both IPsec and OpenVPN connections highlight the complexities and critical nature of VPN setups in securing organizational networks.
