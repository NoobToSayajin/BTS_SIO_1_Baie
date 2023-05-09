# Configuration de baie

<a name="haut-de-page">

<details>
  <summary>Index</summary>
  <ol>
    <li>
      <a href="#Roadmap">Roadmap</a>
      <ul>
        <li><a href="#TODO">TODO</a></li>
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
      </ul>
    </li>
    <li><a href="#configuration-windows-server">Configuration Windows Server</a></li>
    <li><a href="#configuration-debian">Configuration Debian</a></li>
    <li><a href="#creation-dunite-dorganisation">création d'unité d'organisation</a></li>
    <li><a href="#configuration-gpo">Configuration GPO</a></li>
  </ol>
</details>

## Roadmap

### TODO

>   **Debian**
> - Configuration Debian
> - Installation GLPI/OCS
> - Configuration GLPI/OCS
> - Configuration LDAP

> **Windows Server**
> - Configuration OU
> - Configuration GPO

> **R2**
> - ACL

## plan d'adressage

<a name="schema"></a>

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
|R3            |    192.168.0.1|   24|     F 0/0|    Debian|
|SwML-3 Vlan 1 |  192.168.10.10|   24|       P 1|        R2|
|Windows Server| 192.168.10.100|   24|       eth|    SwML-3|
|Debian        | 192.168.20.100|   24|     Ens33|        R3|

Switch

|Address         |  Port|    Vlan|nom Vlan|
|:---------------|-----:|-------:|-------:|
|192.168.1.0/24  |      |  Vlan 1|   Admin|
|192.168.10.0/24 |   4-8| Vlan 10|  Hacene|
|192.168.20.0/24 |  9-13| Vlan 20|  Julien|
|192.168.30.0/24 | 14-18| Vlan 30|   Simon|

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

<a href=".\Config\Routers\R1.config">R1 commande</a>

+ [x] hostname et bannière
+ [x] Configuration DNS
+ Configuration des Interfaces:
  + [x] GigaEthernet 0/0
  + [x] Serial 0/0/0
  + [x] Serial 0/0/1
+ [x] Configuration NAT et ACL
+ [x] Configuration ip route vers PFsense
+ [x] configuration mot de passe et acces telnet
+ [ ] configuration SSH

### Router R2

<a href=".\Config\Routers\R2.config">R2 commande</a>

+ [x] hostname et bannière
+ [x] Configuration DNS
+ Configuration des Interfaces:
  + [x] GigaEthernet 0/0
  + [x] Serial 0/0/0
  + [x] Serial 0/0/1
+ [x] configuration mot de passe et acces telnet
+ [ ] configuration SSH

### Router R3

<a href=".\Config\Routers\R3.config">R3 commande</a>

+ [x] hostname et bannière
+ [x] Configuration DNS
+ Configuration des Interfaces:
  + [x] GigaEthernet 0/0
  + [x] Serial 0/0/0
  + [x] Serial 0/0/1
+ [x] configuration mot de passe et acces telnet
+ [ ] configuration SSH

<p align="right">(<a href="#haut-de-page">Haut de page</a>)</p>

## Configuration Switch Multi Layer

### Switch Nv:III *vlan par port*

<a href=".\Config\Switch\SwML.config">Switch commande</a>

+ [ ] Vlan 1: Administration
+ [ ] Vlan 10
  + nom: Hacene
  + Port range: 4-8
+ [ ] Vlan 20
  + nom: Julien
  + Port range: 9-13
+ [ ] Vlan 30
  + nom: Simon
  + Port range: 14-18

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

+ [ ] Addressse IP
  + IP: 192.168.20.100/24
  + GW: 192.168.20.1
+ Server GLPI/OCS
  + [ ] GLPI // PHP Version
  + [ ] OCS
  + [ ] Connexion LDAP

<p align="right">(<a href="#haut-de-page">Haut de page</a>)</p>

## Creation d'unite d'organisation

<p align="right">(<a href="#haut-de-page">Haut de page</a>)</p>

## Configuration GPO


<p align="right">(<a href="#haut-de-page">Haut de page</a>)</p>
