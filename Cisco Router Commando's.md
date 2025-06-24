# Cisco Router Command Cheat Sheet

3 basis configuratie modes waar we al reeds mee bekend zijn( je zal ze nog veel tegenkomen )

## Configuration Modes

| prompt          | Mode                       | Commando om mode te activeren | uitleg                                       |
| --------------- | -------------------------- | ----------------------------- | -------------------------------------------- |
| `R1>`         | User EXEC (view only mode) | `enable`                    | type enable om user credentials te activeren |
| `R1#`         | Privileged EXEC ( admin )  | `configure terminal`        | om naar de administrator modus te gaan       |
| `R1(config)#` | Global Configuration       | niet van toepassing           | globale configuratie modus na R1#            |

algemene afkortingen van bovenstaande commando's (gescheiden door komma's)

```bash
en , ena
conf t, config term
```

op een cisco router heb je via globale configuratie toegang tot nog 2 andere modes.

| Commando               | Mode             | Beschrijving                  |
| ---------------------- | ---------------- | ----------------------------- |
| `R1(config-if)#`     | Interface Config | configuratie van de interface |
| `R1(config-router)#` | Router Config    | configuratie van de router    |

💡

Wanneer je een IPv6 routing protocol zoals bv : OSPFv3, dan zal de router configuratie-mode prompt de volgende zijn:

 `R1(config-rtr)#`

 <div style="page-break-after: always;"></div>

## Important "SHOW" Commando's

Let op: deze commando’s worden uitgevoerd in de bevoorrechte EXEC-modus (R1# prompt).

Je kunt ze uitvoeren vanuit de globale configuratiemodus (R1(config)# prompt) door het woord *"do"* vóór het commando te plaatsen.

voorbeeld:

```bash
R1 (config)#do show ip interface brief
```

| Commando              | Beschrijving                                                                           |
| ----------------------| ---------------------------------------------------------------------------------------|
| `show running-config` | toon de config opgeslagen in RAM (en de huidig draaiende configuratie van het toestel) |
| `show startup-config` | toon de config opgeslagen in NVRAM.                                                    |
| `show history`        | geeft een overzicht van de laatst gebruikte commando’s in je huidige sessie.           |

---

### Filtering informatie via "SHOW" commando's

sommige commando's, zoals **show running-config**, kunnen meerdere lijnen outputen.

om deze the filteren kan je het pijp ( | ) character gebruiken tesamen met de **filter parameter** en een **filter uitdrukking** `<expression>`.

| **Filter parameter**     | **Effect**                                 |
| -------------------------|--------------------------------------------|
| `section <expression>`   | toont het gedeelte van de filterexpressie  |
| `include <expression>`   | **bevat alleen de regels** van de **uitvoer** die **uitsluitend overeenkomen met de filterexpressie**. |
| `exclude <expression>`   | **sluit alle regels** van de **uitvoer** uit die **overeenkomen met de filterexpressie**. |
| `begin <expression>`     | toont **alle regels** van de **uitvoer**, **beginnend** vanaf de **regel** die **overeenkomt met de filterexpressie**. |

hier is een voorbeeld van het gebruik van filtering met een **SHOW** commando:

```bash
show running-config | include line con
```

💡ProTip:

Standaard bedraagt de output 24 lijnen.

als je de hoeveelheid lijnen die er worden getoond op het terminal scherm wil veranderen kan je volgende commando gebruiken

```bash
R1# terminal lenght [number-of-lines]
```

⚠️

jammer genoeg word dit commando niet ondersteund in Cisco Paket Tracer ( getest op versie 7.2.2 ).

<div style="page-break-after: always;"></div>

## Static Routing

Met een administratieve afstand (AD) van 1.

 Dit zijn, direct na de direct verbonden routes, de meest betrouwbare routes die een router in zijn routingtabel kan hebben.

Statische routes worden allemaal geconfigureerd in de globale configuratiemodus, met het commando **"ip route"**,

Bijvoorbeeld

```bash
R1 (config)#ip route [network-address] [subnet-mask]
```

er zijn 4 types van statische routers.

elk met hun eigen specifiek doel.

Types:

* Standard Static Route

```bash
R1(config)#ip route [network-address] [subnet-mask] ([next-hop-ip] [exit-int])
```

* Default Static Route (0.0.0.0/0)

```bash
R1(config)#ip route 0.0.0.0 0.0.0.0 ([next-hop-ip] [exit-int])
```

* Floating Static Route (AD > 1)

```bash
R1(config)#ip route [network-address] [subnet-mask] ([next-hop-ip] [exit-int])[AD]
```

* Summary Static Route

```bash
R1(config)#ip route [network-address] [subnet-mask*] ([next-hop-ip] [exit-int])
```

* Met het juiste subnetmasker waarmee de anders afzonderlijke routes worden samengevat tot één enkele statische route.

<div style="page-break-after: always;"></div>

## Dynamisch routing: OSPF (Open Shortest Path First)

### OSPF Configuration

|Commando       |Beschrijving   |
|---------------|---------------|
|`R1(config)#router ospf [PID]`| maakt een OSPF process met Process ID [PID] (1-65535) |
|`R1(config-router)#router-id [a.b.c.d]`| ken de router manueel een ID toe ( in een ipv4 formaat). |
|`network <net> [wildcard] area [area-id]`| Voor alle direct verbonden netwerken, kondig elk netwerk aan volgens deze naamgevingsconventie.Het Area ID kan variëren van 0 tot 4294967295. |

💡

het "area id" kan ook uitgedrukt worden in ip-address formaat daarom het bereik van beschikbare "area id's".

💡

ProTip:

Hoe kan ik gemakkelijk alle direct verbonden netwerken met de router visualiseren?

```bash
R1(config)#do show ip route con
```

het toont enkel de direct verbonden netwerken in de routing tabel (routes).

🏆

**best practices**

OSPF-routers binnen een area gebruiken en hebben unieke ID’s nodig om zichzelf te identificeren.
Het is sterk aan te raden om handmatig een gewenst router-ID in te stellen met het router-id commando.

### OSPF Verification

|commando                | Beschrijving                              |
|------------------------|-------------------------------------------|
|``R1#show ip protocols``| Je go-to commando om snel de belangrijkste OSPF-configuratie-informatie op een router te verifiëren (inclusief PID, router ID, geadverteerde netwerken, buren en administratieve afstand) is.|
|``R1#show ip route``    | Je kunt daarnaast het commando ``show ip route ospf`` gebruiken om alleen de OSPF-geleerde netwerken (entries) in de routing tabel te bekijken.|
|``R1#show ip ospf neighbor``| Een lijst van de OSPF buur-routers van deze router.|
|``R1#show ip ospf``| Handig om de PID, router ID, area-informatie en het tijdstip waarop het SPF-algoritme voor het laatst is uitgevoerd te identificeren.|
|``R1#show ip ospf interface brief``| Een samenvatting van de OSPF-geactiveerde interfaces van de router. ⚠️ Dit commando lijkt NIET te werken in Packet Tracer 7.3.|
|``R1#show ip ospf interface``| Gedetailleerde informatie voor een specifieke OSPF-geactiveerde interface.|

💡
Ter info: De standaard Administrative Distance (AD) van OSPF is 110.
[Zie de tabel met Administrative Distance](#administrative-distance)

## Configureren van ROAS (Router-On-A-Stick) inter-VLAN routing

Om router-on-a-stick inter-VLAN routing mogelijk te maken, moeten we meerdere subinterfaces configureren (evenveel als het aantal VLANs waarover we verkeer willen routeren).  
Hierdoor is slechts één fysieke interface, en dus één trunk-link, nodig.

In de globale configuratiemodus, dus bij de R1(config)# prompt:

|commando| Beschrijving|
|--------|-------------|
|``R1(config)#interface g0/0.[vlan-id]``| Maak een **subinterface** aan op interface GigabitEthernet 0/0 voor het betreffende VLAN-ID.|
|``R1(config-subif)#encapsulation dot1q [vlan-id]``| Configureer de subinterface om te werken op het opgegeven VLAN met IEEE 802.1Q encapsulatie.|
|``R1(config-subif)#encapsulation dot1q [vlan-id] native``| Moet geconfigureerd worden op de subinterface die bij de **native** VLAN hoort.|
|``R1(config-subif)#ip address [ip-address] [subnet-mask]``| ❗Vergeet niet om de subinterface een IP-adres toe te wijzen.|
|``R1(config-subif)#interface g0/0``| Ga naar de fysieke interface GigabitEthernet 0/0 om deze te activeren.|
|``R1(config-if)#no shutdown``| Zet de fysieke interface aan (enable).|

## 🚷 ACL (Access Control List)

💡 ProTip: Standaard of uitgebreid?  

* **Standaard ACL's** worden meestal (zo niet altijd) zo dicht mogelijk bij de **bestemming** geplaatst.  
* **Uitgebreide ACL's** worden zo dicht mogelijk bij de **bron** geplaatst.  

Moeite om dit te onthouden? Denk aan:  

* **Standaard = bestemming**
* **Uitgebreid = bron**

💡 herinner:
**Hoeveel ACL's kan ik op de router hebben?**  

* Je kunt één ACL per interface, per protocol (IPv4, IPv6), per richting (inkomend , uitgaand)

### Procedure om standaard genummerd ACLs te configureren

|Commando |Beschrijving |
|---------|-------------|
|``R1(config)#access-list [number] [permit/deny] [address] [wildcard mask]``| Maak een entry aan in een standaard IPv4 ACL met het opgegeven nummer.|
|``R1(config)#interface [int-id]``| Selecteer de interface waarop de ACL toegepast zal worden.|
|``R1(config-if)#ip access-group [number] [in/out]``| Activeer de ACL op de geselecteerde interface en geef aan of deze inkomend (in) of uitgaand (out) toegepast moet worden.|
|``R1(config)#no access-list [number]``|  ⚠️ Dit verwijdert de volledige ACL, dus **elke ACE (Access Control Entry)** die erin staat wordt gewist.|

🏆 ⚠️ Best practice / BELANGRIJK:

 Het is **ZEER aan te raden** om **altijd een back-up** te maken van **ELKE ACL** die je configureert.

### Procedure om standaard Benoemde ACLs te configureren

|Commando |Beschrijving |
|---------|-------------|
|``R1(config)#ip accesslist [standard] [name]``| Maakt een standaard benoemde ACL aan. Je komt hiermee in de standaard (std) named ACL (nacl) configuratiemodus.|
|``R1(config-std-nacl)#``|  Je bevindt je nu in de standaard (std) named ACL (nacl) configuratiemodus. Hier kun je regels toevoegen aan de benoemde ACL.|

## DHCPv4

### Configureer een CISCO router als een DHCPv4 server

```bash
ip dhcp excluded-address 192.168.1.1 192.168.1.10
ip dhcp pool LAN
 network 192.168.1.0 255.255.255.0
 default-router 192.168.1.1
 dns-server 8.8.8.8
 domain-name example.local
```

**Relay:**

```bash
interface g0/1
 ip helper-address <DHCP server IP>
```

## NAT (Network Address Translation)

**Static NAT:**

```bash
ip nat inside source static 192.168.1.10 203.0.113.10
interface g0/0
 ip nat inside
interface g0/1
 ip nat outside
```

**Dynamic NAT:**

```bash
ip nat pool MYPOOL 203.0.113.100 203.0.113.110 netmask 255.255.255.0
access-list 1 permit 192.168.1.0 0.0.0.255
ip nat inside source list 1 pool MYPOOL
```

## HSRP (Hot Standby Router Protocol)

```bash
interface g0/0
 standby version 2
 standby 1 ip 192.168.1.254
 standby 1 priority 110
 standby 1 preempt
 standby 1 timers 3 10
```

## Administrative Distance

| Type       | Code | AD  |
| ---------- | ---- | --- |
| Connected  | C    | 0   |
| Static     | S    | 1   |
| EIGRP Int. | D    | 90  |
| OSPF       | O    | 110 |
| RIP        | R    | 120 |

## IPv4 Address Classes

| Class | First Octet | Range                       |
| ----- | ----------- | --------------------------- |
| A     | 1 - 127     | 0.0.0.0 - 127.255.255.255   |
| B     | 128 - 191   | 128.0.0.0 - 191.255.255.255 |
| C     | 192 - 223   | 192.0.0.0 - 223.255.255.255 |
| D     | 224 - 239   | Multicast                   |
| E     | 240 - 255   | Experimental                |

## Private IPv4 Ranges

| Class | CIDR           | Range                         |
| ----- | -------------- | ----------------------------- |
| A     | 10.0.0.0/8     | 10.0.0.0 - 10.255.255.255     |
| B     | 172.16.0.0/12  | 172.16.0.0 - 172.31.255.255   |
| C     | 192.168.0.0/16 | 192.168.0.0 - 192.168.255.255 |

---

*Gebaseerd op Cisco CCNA Routing & Switching Essentials materiaal*
