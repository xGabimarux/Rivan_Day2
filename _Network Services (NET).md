
<!-- Your monitor number = 31 -->


## ⛅ Warm Up for Day 2.

Access the ff via SecureCRT:
- 10.31.1.2       CoreTAAS
- 10.31.1.4       CoreBABA
- 10.31.100.8     CUCM
- 10.31.31.1  EDGE

<br>

Verify Connectivity:

~~~cmd
@cmd
ping 10.31.1.10         PC Network Adapter
ping 10.31.1.2		    CoreTAAS
ping 10.31.1.4		    CoreBABA
ping 10.31.100.8		CUCM
ping 10.31.31.1		EDGE - INSIDE
ping 200.0.0.31		    EDGE - OUTSIDE

ping 200.0.0.k		        Klassmate's EDGE	       k = klassmate's Monitor Number
ping 10.k.100.8		        Klassmate's CUCM
ping 10.k.1.4		        Klassmate's CoreBABA
ping 10.k.1.2		        Klassmate's CoreTAAS
ping 10.k.1.10		        Klassmate's PC
~~~


<br>
<br>

---
&nbsp;


### REVIEW

~~~
!@CoreBABA
conf t
 monitor session 1 source interface fa0/3,fa0/5,fa0/7
 monitor session 1 destination interface fa0/1,fa0/9
 end
~~~


<br>
<br>

---
&nbsp;


## CIA

### Confidentiality, Integrity, Availability

*How to access port 445 because you don't have a Firewall!*
~~~
!@cmd
net use \\10.3.3.x\ipc$ /user:administrator C1sc0123
net use x: /delete
net use x: \\10.3.3.x\c$
~~~


<br>


~~~
!@Win2022
Win + R
  lusrmgr.msc
~~~


<br>


~~~
!@CoreTAAS
conf t
 username admin privilege 15 secret pass
 username _____ privilege 15 secret pass
 !
 ip domain name sec.com
 crypto key generate rsa
 2048
 ip ssh version 2
 !
 line vty 0 14
  transport input all
  login local
  exec-timeout 0 0
  end
~~~


<br>


__Parser View__
~~~
!@CoreTAAS
conf t
 aaa new-model
 aaa authentication login default local
 aaa authorization exec default local
 line vty 0 14
  transport input all
  login authentication default
 !
 parser view T1
  secret pass
  commands exec include configure terminal
  commands exec include show ip interface brief
  commands exec include show interface *
  commands configure include interface
  commands configure include interface GigabitEthernet0/1 
  commands interface include shutdown
  commands interface include no shutdown
  exit
 username tier1 view T1 secret pass
 username tier2 privilege 15 secret pass
 end
~~~


<br>
<br>

---
&nbsp;


### [NIST SP 800-53](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-53r5.pdf) - Security and Privacy Controls for Information Systems and Organizations

> Employ the principle of least privilege, allowing only authorized accesses for users (or processes acting on behalf of users) that are necessary to accomplish assigned organizational tasks.


<br>
<br>

---
&nbsp;


### ✉️ Integrity
~~~
!@cmd
certutil -hashfile SERVER_EVAL_x64FRE_en-us.iso md5
~~~


<br>
<br>

---
&nbsp;


### 🔀 Availability
*Avoid a single point of failure*

<br>

Execute a persistent ping
~~~
!@cmd
ping 10.31.1.2 -t
~~~


__Trunking and etherchannel__
~~~
!@CoreBABA, CoreTAAS
conf t
 int range fa0/10-12
  switchport trunk encapsulation dot1q
  switchport mode trunk
  channel-group 1 mode active
  channel-protocol lacp
  end
~~~


<br>
<br>

---
&nbsp;


### NIC TEAMING

Windows Server 2022:
  Name: WIN-NETPLUS-31
  
  | NetAdapter        | Connection          | IP Address        |
  | ---               | ---                 | ---               |
  | Network Adapter 1 | NAT                 | 208.8.8.8     /24 |
  | Network Adapter 2 | VMNet 2             | 192.168.102.8 /24 |
  | Network Adapter 3 | VMNet 3             | 192.168.103.8 /24 |
  | Network Adapter 4 | VMNet 3             |                   |
  | Network Adapter 5 | VMNet 3             |                   |
  | Network Adapter 6 | Bridged (Replicate) | 10.31.1.8 /24 |
  
  
<br>
<br>

---
&nbsp;


## Domain Name System

Query only the ff interface:
- 208.8.8.8


| Name | Domain Name   | IP                 |
| ---  | ---           | ---                |
| ns1  | net31.com | 10.31.1.8      |
| www  |               | 10.31.1.8      |
|      |               | 10.31.1.8      |
| ct   |               | 10.31.1.2      |
| cb   |               | 10.31.1.4      |
| cm   |               | 10.31.100.8    |
| ed   |               | 10.31.31.1 |
| cam6 |               | 10.31.50.6     |
| cam8 |               | 10.31.50.8     |


<br>
<br>

On real WinServer
> Conditional Forwarder  
  > net31.com  -  208.8.8.8  


~~~
!@cmd
ping www.net31.com

ping smtp.net31.com
~~~


&nbsp;
---
&nbsp;


### DNS Hierarchy
~~~
ROOT 
   TLD 
     SLD   
	   SUB/HOST 
~~~


<br>
<br>

---
&nbsp;


## Web Server
__Configure Web Server (net31.com)__  
1. Internet Information Services Manager
2. Create an `http` mapping for the domain `www.net31.com`


<br>

__Access__
http://www.net31.com/


<br>
<br>

---
&nbsp;


### 🔴 Phishing Websites
Configure DNS for  `bclo.com`

| Record | Mapping   |
| ---    | ---       |
|        | 208.8.8.8 |
| ns     | 208.8.8.8 |
| www    | 208.8.8.8 |


<br>
<br>


1. Internet Information Services Manager
2. Create an `http` mapping for the bdo web files to the domain `www.bclo.com`

<br>

__Access__
http://www.bclo.com/


<br>
<br>

---
&nbsp;


### 🎯 Exercise: Create another phishing website for bpi.com.ph

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>


## Secure Web (HTTPS)
Example Web: `www.cisco.com`  
Win+R: `certmgr.msc`  


&nbsp;
---
&nbsp;


### CA Hierarchy
1. ROOT CA   (Self-signed)
   - X509v3  
   - Basic Constraints [Critical]  
       - CA  
   - Key Usage [Critical]  
       - Certificate Sign  
       - CRL Sign  

<br>

2. SUB CA
   - X509v3
   - Basic Constraints [Critical]
       - CA
	   - Path Len: 0
   - Key Usage [Critical]
       - Certificate Sign
       - CRL Sign

<br>

3. LEAF CA
   - X509v3
   - Basic Constraints [Critical]
       - END-ENTITY
   - Extensions
       - Subject Alt Names
   - Key Usage [Critical]
       - Digital Signature
	   - Key Encipherment
	   - Data Encipherment
	   - Key Agreement
   - Extended Key Usage
       - TLS Web Server Authentication
       - TLS Web Client Authentication	   
	   - E-mail Protection
	   - IPSec End System
	   - IPSec Tunnel
	   - IPSec User
	   - IP Security end entity


<br>
<br>

~~~
!@cmd
ping -4 www.rivanit.com
~~~


<br>
<br>

---
&nbsp; 


### SSL Certificate

Subject : Distinguished Names  
  - Defined ITU-T (International Telecommunication Union Telecommunication Standardization Sector) X.500 Series
  - X.501 - Defines the directory model and Distinguished Names.
  - X.520 - Defines the standard attribute types used in DNs


<br>
<br>

---
&nbsp;


### Wildcard Certificate  
Configure DNS for  `sec31.com`

| Record | Mapping           |
| ---    | ---               |
| www    | 208.8.8.8         |
| web    | www.net31.com |
| site   | www.net31.com |


&nbsp;
---
&nbsp;


### 🎯 Exercise: Create a ROOT, INTERMEDIATE, LEAF Certificate for https://www.bclo.com

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>


&nbsp;
---
&nbsp;



<br>
<br>

---
&nbsp;


## Enterprise Email Server
- HMail Server
- Thunderbird

__Create Accounts For `net31.com`__
1. Support
2. Admin
3. User


Setup __DNS Secondary Zones__ & __Conditional Forwarders__
~~~
!@cmd SEC-AZURE31
route add 10.0.0.0 mask 255.0.0.0 10.31.1.4 -p
route add 200.0.0.0 mask 255.255.255.0 10.31.1.4 -p
~~~


<br>
<br>

---
&nbsp;


## Security Awareness
Phishing Simulation  

__Admin__ > __Users__  


<br>
<br>

---
&nbsp;


## Identity Access Management
Install `Active Directory Domain Services`

<br>

Identity Management Framework (AGDLP)
__Accounts__ > __Global Group__ > __Domain Local__ > __Permissions__

| OU  | Local Domain | Global Group | Users |
| --- | ---          | ---          | ---   |
| NOC | LGNOC        | GGNOC        | ac    |
| SOC | LGSOC        | GGSOC        | kc    |
| FIN | LGFIN        | GGFIN        | zc    |

<br>

~~~
On GPO > Default Domain Policy
         > Computer Config 
		   > Policies 
		     > Window Settings 
		       > Security Settings 
			     > Local Policies 
				   > User Rights
				     > Allow Log On Locally
					   > Add : Administrators, and LGGRoups
~~~

~~~
!@cmd
gpupdate /force
~~~


<br>
<br>

---
&nbsp;


## NAS - Network Attatched Storage (File Access)

Provide File level access to the network.


<br>
<br>

---
&nbsp;


## SAN - Storage Area Network      (Storage Access)
- Add 8GB of Storage

Provide Block Level Access

<br>
<br>

---
&nbsp;


## AAA - RADIUS (Wired & Wireless)
> [!IMPORTANT]
> Configure WinServer 2022 for a RADIUS Server

<br>

Requirements:
- ACTIVE DIRECTORY
- NETWORK POLICY SERVER (RADIUS)

<br>

~~~
!@Cisco
conf t
 username admin privilege 15 secret pass
 aaa new-model
 radius server WINRAD
  address ipv4 10.31.1.8 auth-port 1812 acct-port 1813
  key keykeymo
  exit
 aaa group server radius RADGROUP
  server name WINRAD
  exit
 aaa authentication login default group RADGROUP local
 aaa authorization exec default group RADGROUP local
 line vty 0 14
  login authentication default
  end
~~~


<br>
<br>

---
&nbsp;


## Enterprise Certificate Authority

~~~
!@UTM-PH
conf t
 hostname UTM-PH
 enable secret pass
 service password-encryption
 no logging cons
 ip domain lookup
 ip domain lookup source-interface G2
 ip name-server 192.168.102.8
 line vty 0 14
  transport input all
  password pass
  login local
  exec-timeout 0 0
 int g1
  ip add 208.8.8.11 255.255.255.0
  no shut
 int g2
  ip add 192.168.102.11 255.255.255.0
  no shut
 int g3
  ip add 10.11.11.113 255.255.255.224
  no shut
 !
 username admin privilege 15 secret pass
 ip http server
 ip http secure-server
 ip http authentication local
 ip route 0.0.0.0 0.0.0.0 208.8.8.2
 end
wr
!
~~~


<br>


Create a Service Account:
- Active Directory and Users and Computers

| New User    |              |
| ---         | ---          |
| User Name   | ca           |
| Full Name   | CERTAUTH     |
| Password    | C1sc0123     |
| Pass Policy | Never Expire |
| Member of   | IIS_IUSRS    |


&nbsp;
---
&nbsp;


### Install Active Directory Certificate Services

Afterwards, install ADCS Add-Ons:
- Certificate Enrollment Policy Web Service  
- Certificate Enrollment Web Service         
- Certificate Authority Web Enrollment
- Network Device Enrollment Service


&nbsp;
---
&nbsp;


### Create DNS Mapping for both Device
- Reverse Lookup Zone : 208.8.8.0
- A Record : utmph.net31.com : 208.8.8.11


~~~
!@UTM-PH
conf t
 crypto key generate rsa modulus 2048 label CERTKEY
 !
 crypto pki trustpoint NETPLUS
  enrollment url http://192.168.102.8/certsrv/mscep/mscep.dll
  serial-number
  fqdn utmph.net31.com
  ip-address 208.8.8.11
  subject-name CN=UTM-PH,OU=NOC,O=RIVANCORP,L=MAKATI,ST=NCR,C=PH
  subject-alt-name utmph.net31.com
  revocation-check none
  source interface GigabitEthernet2
  rsakeypair CERTKEY
  end
~~~


&nbsp;
---
&nbsp;


### Access CA Web Enrollment

Set Routes for trustpoints:
~~~
!@cmd
route add 208.8.8.11 mask 255.255.255.255 192.168.102.11
~~~

<br>
<br>


http://192.168.102.8/certsrv/mscep/mscep.dll  


<br>
<br>

Grab the Hash & Challenge Password    
- Hash: ___________    
- Pass: ___________ 


&nbsp;
---
&nbsp;


### Enroll Network Devices

~~~
!@UTM-PH
conf t
 crypto pki enroll NETPLUS
~~~
