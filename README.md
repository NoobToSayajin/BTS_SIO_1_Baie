# Configuration de baie

<a name="haut-de-page">

<!-- TABLE OF CONTENTS -->
<details>
  <summary>Index</summary>
  <ol>
    <li>
      <a href="#about-the-project">About The Project</a>
      <ul>
        <li><a href="#built-with">Built With</a></li>
      </ul>
    </li>
    <li>
      <a href="#plan-dadressage">Plan d'adressage</a>
      <ul>
        <li><a href="#schema">Schéma</a></li>
        <li><a href="#table-d-adresses">Table d'adresses</a></li>
      </ul>
    </li>
    <li><a href="#configuration-pfsense">Configuration Pfsense</a></li>
    <li>
      <a href="#configuration-router">Configuration routers</a>
      <ul>
        <li><a href="#router-r1">Router R1</a></li>
        <li><a href="#router-r2">Router R2</a></li>
        <li><a href="#router-r3">Router R3</a></li>
      </ul>
    </li>
    <li>
      <a href="#configuration-switch-multi-layer">Switch MultiLayer</a>
      <ul>
        <li><a href="#switch-nviii-vlan-par-port">Switch Niveau III <I>Vlan par port</I></a></li>
        <li><a href="#switch-nvii-vlan-par-reseau">Switch Niveau II <I>Vlan par reseau</I></a></li>
      </ul>
    </li>
    <li><a href="#configuration-windows-server">Configuration Windows Server</a></li>
    <li><a href="#configuration-debian">Configuration Debian</a></li>
    <li><a href="#configuration-pc-client">Configuration PC client</a></li>
  </ol>
</details>

## plan d'adressage

<a name="schema"></a>

<!-- ![Schéma](.\Img\SchemaBaie.png) -->
<img src=".\Img\SchemaBaie.png" alt="Schéma">

<a name="table-d-adresses"></a>
|Device        |        address| CIDR| interface| Relier à:|
|:-------------|--------------:|----:|---------:|---------:|
|PFsense       |  192.168.11.80|   24|   em0/WAN|     Nuage|
|PFsense       |     172.16.0.1|   24|   em1/LAN|        R1|
|R1            |     172.16.0.2|   24|     G 0/0|   PFsense|
|R1            |       10.0.0.1|   24|   S 0/0/0|        R3|
|R1            |       10.0.1.1|   24|   S 0/0/1|        R2|
|R2            |       10.0.1.2|   24|   S 0/0/0|        R1|
|R2            |   192.168.10.1|   24|     G 0/0|    SwML-3|
|R3            |       10.0.0.2|   24|   S 0/0/0|        R1|
|R3            |   192.168.20.1|   24|     F 0/0|    Debian|
|SwML-3 Vlan 1 |  192.168.10.10|   24|       P 1|        R2|
|Windows Server| 192.168.10.100|   24|       eth|    SwML-3|
|Debian        | 192.168.20.100|   24|     Ens33|        R3|

Switch
|Address         |  Port|    Vlan|nom Vlan|
|:---------------|-----:|-------:|-------:|
|192.168.10.10   |      |  Vlan 1|   Admin|
|                |   4-8| Vlan 10|  Hacene|
|                |  9-13| Vlan 20|  Julien|
|                | 14-18| Vlan 30|   Simon|

<p align="right">(<a href="#haut-de-page">Haut de page</a>)</p>

## Configuration PFsense

+ WAN
  + interface: em0
  + IP: 192.168.11.80 /24
  + upstream: 192.168.11.1
+ LAN
  + interface: em1
  + IP: 172.16.0.1 /24
  + DHCP: null

<p align="right">(<a href="#haut-de-page">Haut de page</a>)</p>

## Configuration Router

### Router R1

+ hostname: R3
+ Interface GigaEthernet 0/0
  + description "Link to Pfsense"
  + ip address 172.16.0.2 255.255.255.0
  + ip ant outside
  + no shutdown
+ Interface GigaEthernet 0/1
  + null
+ Interface Serial 0/0/0
  + description "Link to R3"
  + ip address 10.0.0.1 255.255.255.0
  + ip nat inside
  + no shutdown
+ Interface Serial 0/0/1
  + description "Link to R2"
  + ip address 10.0.1.1 255.255.255.0
  + ip nat inside
  + no shutdown
+ ip nat inside source list 1 interface GigaEthernet 0/0 overload
+ access-list 1 permit any
+ ip route 0.0.0.0 0.0.0.0 172.16.0.1
+ login
+ telnet

### Router R2

+ hostname: R2
+ Interface GigaEthernet 0/0
  + description "Link to Switch"
  + ip address 192.168.10.1 255.255.255.0
  + no shutdown
+ Interface GigaEthernet 0/1
  + null
+ Interface Serial 0/0/0
  + description "Link to R1"
  + ip address 10.0.1.2 255.255.255.0
  + no shutdown
+ Interface Serial 0/0/1
  + null
+ login
+ telnet

### Router R3

+ hostname: R3
+ Interface FastEthernet 0/0
  + description "Link to Debian"
  + ip address 192.168.20.1 255.255.255.0
  + no shutdown
+ Interface FastEthernet 0/1
  + null
+ Interface Serial 0/0/0
  + description "Link to R1"
  + ip address 10.0.0.2 255.255.255.0
  + no shutdown
+ Interface Serial 0/0/1
  + null
+ login
+ telnet

<p align="right">(<a href="#haut-de-page">Haut de page</a>)</p>

## Configuration Switch Multi Layer

### Switch Nv:III *vlan par port*

+ encapsulation dot1q
+ Vlan 1
  + ip: 192.168.10.10 /24
+ Vlan 10
  + nom: Hacene
  + Port range: 4-8
+ Vlan 20
  + nom: Julien
  + Port range: 9-13
+ Vlan 30
  + nom: Simon
  + Port range: 14-18

### Switch Nv:II *vlan par reseau*

<p align="right">(<a href="#haut-de-page">Haut de page</a>)</p>

## Configuration Windows Server

+ [x] Adresse IP
  + IP: 192.168.10.100/24
  + GW: 192.168.10.1
  + VLAN:
+ [x] ADDS/DNS
  + Nom de Domaine: Epnak.local
+ [x] Creation d'utilisateurs
+ [ ] Creation d'OU
+ [ ] Creation de GPO

<p align="right">(<a href="#haut-de-page">Haut de page</a>)</p>

## Configuration Debian

+ [x] Addressse IP
  + IP: 192.168.20.100/24
  + GW: 192.168.20.1
  + VLAN: null
+ Server GLPI/OCS
  + [ ] GLPI // PHP Version
  + [ ] OCS
  + [ ] Connexion LDAP

<p align="right">(<a href="#haut-de-page">Haut de page</a>)</p>

## Configuration PC Client

### PC 1

### PC 2

### PC 3
