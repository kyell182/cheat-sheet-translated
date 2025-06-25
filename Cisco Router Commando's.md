# Cisco Router Command Cheat Sheet

3 basis configuratie modes waar we al reeds mee bekend zijn ( je zal ze nog veel tegenkomen )

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

## Belangerijke "show" commando's

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

### Filtering informatie via "show" commando's

sommige commando's, zoals **show running-config**, kunnen meerdere lijnen outputen.

om deze the filteren kan je het pijp ( | ) character gebruiken tesamen met de **filter parameter** en een **filter uitdrukking** `<expression>`.

| **Filter parameter**     | **Effect**                                 |
| -------------------------|--------------------------------------------|
| `section <expression>`   | toont het gedeelte van de filterexpressie  |
| `include <expression>`   | **bevat alleen de regels** van de **uitvoer** die **uitsluitend overeenkomen met de filterexpressie**. |
| `exclude <expression>`   | **sluit alle regels** van de **uitvoer** uit die **overeenkomen met de filterexpressie**. |
| `begin <expression>`     | toont **alle regels** van de **uitvoer**, **beginnend** vanaf de **regel** die **overeenkomt met de filterexpressie**. |

hier is een voorbeeld van het gebruik van filtering met een **show** commando:

```bash
show running-config | include line con
```

💡ProTip:

Standaard bedraagt de output 24 lijnen.

als je de hoeveelheid lijnen die er worden getoond op het terminal scherm wil veranderen kan je volgende commando gebruiken

```bash
R1# terminal length [number-of-lines]
```

⚠️

jammer genoeg word dit commando niet ondersteund in Cisco Paket Tracer ( getest op versie 7.2.2 ).

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

## Dynamisch routing: OSPF (Open Shortest Path First)

### OSPF configuratie

|Commando       |Beschrijving   |
|---------------|---------------|
|`R1(config)#router ospf [PID]`| maakt een OSPF process met Process ID [PID] (1-65535) |
|`R1(config-router)#router-id [a.b.c.d]`| ken de router manueel een ID toe ( in een ipv4 formaat). |
|`network <net> [wildcard] area [area-id]`| Voor alle direct verbonden netwerken, kondig elk netwerk aan volgens deze naamgevingsconventie.Het Area ID kan variëren van 0 tot 4294967295. |

💡

het "area id" kan ook uitgedrukt worden in ip-address formaat daarom het bereik van beschikbare "area id's".

💡ProTip:

Hoe kan ik gemakkelijk alle direct verbonden netwerken met de router visualiseren?

```bash
R1(config)#do show ip route con
```

het toont enkel de direct verbonden netwerken in de routing tabel (routes).

🏆

Best practices

OSPF-routers binnen een area gebruiken en hebben unieke ID’s nodig om zichzelf te identificeren.

Het is sterk aan te raden om handmatig een gewenst router-ID in te stellen met het router-id commando.

### OSPF verificatie

|commando                | Beschrijving                              |
|------------------------|-------------------------------------------|
|``R1#show ip protocols``| Je go-to commando om snel de belangrijkste OSPF-configuratie-informatie op een router te verifiëren (inclusief PID, router ID, geadverteerde netwerken, buren en administratieve afstand) is.|
|``R1#show ip route``    | Je kunt daarnaast het commando ``show ip route ospf`` gebruiken om alleen de OSPF-geleerde netwerken (entries) in de routing tabel te bekijken.|
|``R1#show ip ospf neighbor``| Een lijst van de OSPF buur-routers van deze router.|
|``R1#show ip ospf``| Handig om de PID, router ID, area-informatie en het tijdstip waarop het SPF-algoritme voor het laatst is uitgevoerd te identificeren.|
|``R1#show ip ospf interface brief``| Een samenvatting van de OSPF-geactiveerde interfaces van de router. ⚠️ Dit commando lijkt NIET te werken in Packet Tracer 7.3.|
|``R1#show ip ospf interface``| Gedetailleerde informatie voor een specifieke OSPF-geactiveerde interface.|

💡Ter info:

De standaard Administrative Distance (AD) van OSPF is 110.

[Zie de tabel met Administrative Distance](#veelvoorkomende-administrative-distance-ad-waarden)

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

|Commando |Beschrijving |
|---------|-------------|
|``R1(config)#ip dhcp excludded-address [start-address (end-address)]``| Sluit één enkel of een reeks IPv4-adressen uit; deze adressen zullen niet beschikbaar zijn voor DHCP-toewijzing aan hosts.|
|``R1(config)#ip dhcp pool [pool name]``| Maak en benoem de pool met beschikbare adressen voor toewijzing. Je komt hiermee in de **DHCP-configuratiemodus**.|
|``R1(dhcp-config)#network [networkid] [mask]``| Specificeer het netwerkadres met het subnetmasker **of** de prefix-lengte.|
|``R1(dhcp-config)#default-router``| Specificeer het IPv4-adres van de default gateway (router) die de hosts zullen gebruiken.|

Dat is alles wat nodig is voor een eenvoudige DHCP-serverconfiguratie.  
Extra configuratie-opties zijn mogelijk met de volgende commando's:

|Commando |Beschrijving |
|---------|-------------|
|``R1(dhcp-config)#lease [days] ([hours] [minutes])``| Specificeer de tijdsduur van de lease voor elk adres.|
|``R1(dhcp-config)#dns-server [dns-address-1] ([dnsaddress-2 ... dns-address-8])``| Specificeer tot 8 DNS-serveradressen.|
|``R1(dhcp-config)#domain-name [name]``|  Specificeer de domeinnaam.|

### IP helper adres

Als je DHCP-server zich op een ander netwerksegment bevindt, moet je externe DHCPv4-verzoeken doorsturen (relayen).
Voer hiervoor het volgende commando in op elke interface van de router die het DHCP-serversegment moet bereiken:

|Commando |Beschrijving |
|---------|-------------|
|``R1(config-if)#ip helper address [DHCP-server-address]``|  Geeft aan dat de router DHCP-berichten moet doorsturen (in plaats van te negeren) naar het adres van de DHCP-server.|

## NAT (Network Address Translation)

**Static NAT:**

|Commando |Beschrijving |
|---------|-------------|
|``R1(config)#ip nat inside source static [local-add] [global-add]``| Configureer statische NAT en geef het lokale adres op dat gemapt zal worden naar een globaal adres.|

### Inside en Outside interfaces instellen voor NAT

💡 Onthoud:

 Geef na het configureren van NAT **altijd** expliciet aan **welke interfaces** **'inside'** en welke **'outside'** zijn.

Voor ons is dat misschien duidelijk met een topologiediagram, maar voor de router niet!

|Commando |Beschrijving |
|---------|-------------|
|``R1(config)#interface [int-id]``| Ga naar de configuratiemodus van de interface die je als 'inside' wilt instellen voor NAT.|
|``R1(config-if)#ip nat inside``| Markeer de interface als NAT inside (meestal de LAN-kant).|
|``R1(config)#interface [int-id]``| Ga naar de configuratiemodus van de interface die je als 'outside' wilt instellen voor NAT.|
|``R1(config-if)#ip nat outside``| Markeer de interface als NAT outside (meestal de WAN/internet-kant).|

### Configureer dynamische NAT

|Commando |Beschrijving |
|---------|-------------|
|``R1(config)#ip nat pool [pool-name] [startaddress] [end-address] {netmask [mask] prefix-length [prefix-length]}``| Maak de pool van routeerbare adressen aan die hosts zullen gebruiken om het private netwerk te verlaten.|
|``access-list [number] {permit / deny} [source] [wildcard mask]``| Maak een access-list aan om te bepalen welke interne adressen NAT mogen gebruiken.|
|``ip nat inside source list [acl-number] pool [pool name]``| Koppel de NAT-pool aan de access-list zodat alleen toegestane interne adressen uit de lijst gebruikmaken van de NAT-pool.|

💡 **Vergeet tot slot NIET**:

 om de inside- en outside-interfaces te specificeren voor NAT! Zonder deze stap werkt NAT niet correct.

## HSRP (Hot Standby Router Protocol)

|Commando |Beschrijving |
|---------|-------------|
|``R1(config-if)#standby version 2``| (optioneel) Specificeer dat HSRP versie 2 gebruikt zal worden. Anders draait je HSRP-instantie standaard in versie 1.|
|``R1(config-if)#standby [group] ip [virtual-ip]``| Specificeer de HSRP-groep en het IP-adres van de virtuele gateway.|
|``R1(config-if)#standby [group] priority [0-255]``| (optioneel) Specificeer per router een prioriteit voor de verkiezing van de actieve router.|
|``R1(config-if)#standby [group] preempt``| (optioneel) Hiermee sta je preemption toe: een router met een hogere prioriteit neemt de actieve rol over als de huidige actieve router een lagere prioriteit heeft.|
|``R1(config-if)#standby [group] timers [hellotimer] [hold-timer]``| Specificeer de hello- en hold (dead) timers. HSRP zorgt er automatisch voor dat de hold-timer groter is dan de hello-timer om fout gedrag te voorkomen.|

⚠️ **Gebruik ALTIJD dezelfde HSRP-versie op al je groepen/topologieën!**

Voor Cisco-routers geldt: als je verschillende HSRP-versies (v1 en v2) gebruikt, ziet de router dit als twee verschillende HSRP-groepen.  

Hierdoor krijg je een duplicate address error als je HSRPv1 en HSRPv2 met hetzelfde virtuele routeradres configureert.

💡 **HSRPv1 vs HSRPv2: Wat is het verschil?**

* **Aantal groepen:**
  * HSRPv1 ondersteunt maximaal 256 groepen (0-255) per interface.  
  * HSRPv2 ondersteunt tot 4096 groepen (0-4095) per interface.

* **IPv6-ondersteuning:**
  * HSRPv1 ondersteunt alleen IPv4.  
  * HSRPv2 ondersteunt zowel IPv4 als IPv6.

* **Multicast-adres:**  
  * HSRPv1 gebruikt 224.0.0.2.  
  * HSRPv2 gebruikt 224.0.0.102 (minder kans op conflicten).

* **Virtueel MAC-adres:**
  * HSRPv1: 0000.0C07.ACxx  
  * HSRPv2: 0000.0C9F.Fxxx

❗(xx/xxx = groepnummer in hex)

* **Detectie van duplicate adressen:**  
  * HSRPv2 kan duplicate virtuele IP-adressen detecteren en waarschuwen.

* **Andere verbeteringen:**  
  * HSRPv2 biedt betere schaalbaarheid, beveiliging en foutdetectie.

Gebruik HSRPv2 als je nieuwe netwerken ontwerpt of IPv6 wilt ondersteunen!

## Appendix

### Veelvoorkomende administrative distance (AD) waarden

Standaard waarden:

| Type       | Code | AD  |
| ---------- | ---- | --- |
| Connected  | C    | 0   |
| Static     | S    | 1   |
| EIGRP Int. | D    | 90  |
| OSPF       | O    | 110 |
| RIP        | R    | 120 |
  
[Terug naar OSPF verificatie](#ospf-verificatie)  
[Terug naar EIGRP IPv4 verificatie](#eigrp-ipv4-verificatie)
[Terug naar EIGRP IPv6 verificatie](#eigrp-ipv6-verificatie)  
[Terug naar RIP](#ipv4-address-classes)

### IPv4 Address Classes

Er zijn vijf klassen waarin IPv4-adressen zijn onderverdeeld: A, B, C, D en E.

💡 Klassen D en E zijn gereserveerde groepen.  

* **Klasse D-adressen** zijn bedoeld voor multicastgroepen.
* **Klasse E-adressen** zijn gereserveerd voor onderzoeksdoeleinden.

| Class | First Octet | Range                       |
| ----- | ----------- | --------------------------- |
| A     | 1 - 127     | 0.0.0.0 - 127.255.255.255   |
| B     | 128 - 191   | 128.0.0.0 - 191.255.255.255 |
| C     | 192 - 223   | 192.0.0.0 - 223.255.255.255 |
| D     | 224 - 239   | Multicast                   |
| E     | 240 - 255   | Experimental                |

### Private IPv4 Ranges

De volgende private IPv4-adresruimtes zijn gedefinieerd in RFC 1918 ("Address Allocation for Private Internets").

Deze adressen worden gebruikt binnen private netwerken en zijn dus **NIET routeerbaar** naar het publieke internet.

Om verkeer van een privaat naar een publiek netwerk door te sturen, gebruik je **Network Address Translation (NAT)**, gedefinieerd in RFC 3022.

Nat configuratie van CISCO routers word [hier](#nat-network-address-translation) behandelt.

| Class | bereik (CIDR notatie)           | Range                         |
| ----- | -------------- | ----------------------------- |
| A     | 10.0.0.0/8     | 10.0.0.0 - 10.255.255.255     |
| B     | 172.16.0.0/12  | 172.16.0.0 - 172.31.255.255   |
| C     | 192.168.0.0/16 | 192.168.0.0 - 192.168.255.255 |

**CIDR-notatie** (Classless Inter-Domain Routing)
is een compacte manier om een IP-adres en het subnetmasker samen te schrijven.  
Het formaat is: `IP-adres/prefix-lengte`  
Bijvoorbeeld: `192.168.1.0/24` betekent dat de eerste 24 bits het netwerk aanduiden (subnetmasker 255.255.255.0).

## Dynamisch Routing : RIP (Routing Information Protocol)

RIP gebruikt het aantal hops (sprongen) als metric om de beste route te bepalen, met een maximum van 15 hops.

Het protocol is eenvoudig, maar minder schaalbaar en efficiënt dan modernere protocollen zoals OSPF of EIGRP.

### RIPv1 configuratie

|Commando |Beschrijving |
|---------|-------------|
|``R1(config)#router rip``| Ga naar de router configuratiemodus en activeer RIP als routing protocol.|
|``R1(config-router)#network [network-id]``| Geef het netwerksegment op dat door RIP geadverteerd moet worden. Let op: dit gebeurt **zonder het subnetmasker.**|
|``R1(config-router)#passiveinterface [int-id]``| (optioneel) Voorkom dat een interface RIP-updates verzendt.|

💡

Om veiligheidsredenen moeten interfaces die GEEN RIP-updates hoeven te verzenden (bijvoorbeeld interfaces richting netwerksegmenten met alleen hosts en geen andere routers) als **passive** worden ingesteld.

Dit is ook best practice, want RIP-updates op zulke segmenten zijn enkel verspilde bandbreedte.

💡

Zoals je ziet, wordt er GEEN subnetmasker geconfigureerd.

Bij het doorsturen van updates gebruikt de router ofwel het masker dat op de lokale interface is ingesteld, of het standaardmasker op basis van de adresklasse (A, B of C).

💡

Meer info over adresklassen vind je [hier](#ipv4-address-classes).

⚠️

Hierdoor moeten je netwerken bij een RIPv1-configuratie **aaneengesloten** zijn (contiguous):

VLSM (Variable Length Subnet Masking) of supernetting wordt NIET ondersteund door RIP versie 1.

💡 Onthoud:

De standaard Administrative Distance (AD) van RIP is 120.

Een overzicht van veelvoorkomende AD-waarden vind je [hier](#veelvoorkomende-administrative-distance-ad-waarden).

### bijkomende RIPv1 Configuratie

|Commando |Beschrijving |
|---------|-------------|
|``R1(config-router)#defaultinformation originate``| Als er een [default static route](#static-routing) is geconfigureerd op de router, adverteer deze default route naar andere routers die RIP-updates ontvangen.|

### RIPv2 configuratie

|Commando |Beschrijving |
|---------|-------------|
|``R1(config)#router rip``| Ga naar de router configuratiemodus en activeer RIP als routing protocol.|
|``R1(config-router)#version 2``| Zet het RIP-proces om van versie 1 naar versie 2.|
|``R1(config-router)#no autosummary``| Schakel automatische netwerksamenvatting (auto summarization) uit.|

💡 Onthoud:

standaard (zonder eerst versie 2 te configureren) kan RIP zowel RIPv1- als RIPv2-routingupdates ontvangen.

💡

Je kunt dit zien met het commando `R1#show ip protocols`.

## Dynamisch Routing IPv4 : EIGRP (Enhanced Interior Gateway Routing Protocol)

EIGRP is een geavanceerd, door Cisco ontwikkeld routingprotocol dat gebruikmaakt van de Diffusing Update Algorithm (DUAL) voor snelle en efficiënte routebepaling.

Het ondersteunt VLSM, CIDR, unequal-cost load balancing en is geschikt voor zowel IPv4 als IPv6.

### EIGRP configuratie voor IPv4

|Commando |Beschrijving |
|---------|-------------|
|``R1(config)#router eigrp [AS-nummer]``| Start het EIGRP-proces met het opgegeven Autonomous System (AS) nummer (1 - 65535).|
|``R1(config-router)#eigrp router-id [a.b.c.d]``| (optioneel) Stel handmatig een router ID in, in IPv4-adresformaat.|
|``R1(config-router)#network [network-address]``| Voeg het classful netwerkadres toe voor elk direct verbonden netwerk dat je met EIGRP wilt adverteren.|
|``R1(config-router)#network [network-address] ([wildcard-mask])``| Voeg het netwerkadres toe met het wildcard-masker. Dit is aanbevolen bij het gebruik van classless addressing (VLSM/CIDR).|

💡

Als er geen EIGRP router ID is geconfigureerd, gebruikt de router het hoogste IPv4-adres van een actieve loopback-interface.

Als er GEEN actieve loopback-interfaces zijn, wordt het hoogste IPv4-adres van een actieve fysieke interface als router ID gebruikt.

💡 ProTip:

Hoe kun je eenvoudig alle direct verbonden netwerken van een router visualiseren?

Gebruik het commando `R1(config)#do show ip route con`.  

Dit toont enkel de direct verbonden netwerken (routes) in de routing tabel.

💡 Onthoud:

standaard voert EIGRP GEEN automatische netwerksamenvatting (autosummarization) uit.

### EIGRP IPv4 verificatie

|Commando |Beschrijving |
|---------|-------------|
|``R1#show ip protocols``| Verifieert de huidige geconfigureerde waarden voor EIGRP (en eventuele andere ingeschakelde routingprotocollen op het toestel).|
|``R1#show ip eigrp neighbors``| Toont de neighbor table. Gebruik dit om te verifiëren dat de router zijn EIGRP-buren herkent.|
|``R1#show ip route eigrp``|  Toont alleen de EIGRP-geleerde routes in de routing tabel.|
|``R1#show ip eigrp interface``| Verifieert welke interfaces EIGRP geactiveerd hebben, het aantal peers per interface en de transmit queues.|

💡 Onthoud:

 De standaard Administrative Distance (AD) van interne EIGRP-routes is 90.
  
Een overzicht van veelvoorkomende AD-waarden vind je [hier](#veelvoorkomende-administrative-distance-ad-waarden).

* De AD voor een EIGRP summary route is 5.  
* De AD voor een EIGRP externe route is 170.

### EIGRP Fine Tuning

|Commando |Beschrijving |
|---------|-------------|
|``R1(config-router)#variance [variance]``| Pas de variance aan om **unequal cost load balancing** mogelijk te maken. De waarde kan liggen tussen 1 en 128.|
|``R1(config-router)#auto-summary``|  Schakel **automatische netwerksamenvatting** (auto summarization) in.|
|``R1(config-router)#redistribute static``|  Propageren van een **default static route** via het routing protocol. Werkt voor zowel IPv4 als IPv6.💡 Vergeet niet de routing tabellen te controleren om te verzekeren dat de configuratie correct en gewenst is.|
|``R1(config-if)#bandwidth [BW in kilobits]``| Pas de EIGRP-metric aan door de (virtuele) bandbreedte van de interface te wijzigen. ⚠️ Dit gebeurt in de **interface configuratie modus**.|
|``R1(config-if)#ip hello-interval eigrp [AS-number] [time in seconds]``|  Pas het hello-interval aan voor EIGRP op een IPv4-interface. ⚠️ Dit gebeurt in de **interface configuratie modus**.|
|``R1(config-if)#ipv6 hellointerval eigrp [AS-number] [time in seconds]``|  Pas het hello-interval aan voor EIGRP op een IPv6-interface. ⚠️ Dit gebeurt in de **interface configuratie modus**.|
|``R1(config-if)#ip hold-time eigrp [AS-number] [time in seconds]``|  Pas de hold-timer aan voor EIGRP op een IPv4-interface. ⚠️ Dit gebeurt in de **interface configuratie modus**.|
|``R1(config-if)#ipv6 hold-time eigrp [AS-number] [time in seconds]``| Pas de hold-timer aan voor EIGRP op een IPv6-interface. ⚠️ Dit gebeurt in de **interface configuratie modus**.|

💡 Onthoud:

automatische netwerksamenvatting (auto summarization) is standaard uitgeschakeld bij EIGRP.

⚠️ Let op:

Wanneer je EIGRP hello-intervals en hold-timers aanpast, zorg er ALTIJD voor dat je hello-interval **korter is dan** je hold-timer.

Anders kan je link instabiel worden AKA "oscilleren"  (door verkeerde timerconfiguratie gaat de verbinding constant op en neer).

## Dynamisch Routing IPv6 : EIGRP (Enhanced Interior Gateway Routing Protocol)

### EIGRP configuratie voor IPv6

|Commando |Beschrijving |
|---------|-------------|
|``R1(config)#ipv6 unicastrouting``| Dit **globale configuratiecommando** zorgt ervoor dat de router IPv6-pakketten kan doorsturen (forwarden).|
|``R1(config)#ipv6 router eigrp [AS-number]``| Ga naar de router configuratiemodus voor EIGRP voor IPv6 en specificeer het AS-nummer.|
|``R1(config-rtr)#eigrp router-id [a.b.c.d]``| 💡 Het EIGRP-proces voor IPv6 zal pas actief worden nadat je de router ID hebt ingevoerd met het commando `eigrp router-id [a.b.c.d]`.|
|``R1(config-rtr)#no shutdown``| 💡 Dit is best practice, aangezien EIGRP voor IPv6 een **shutdown-feature** heeft. Vergeet dus niet `no shutdown` te gebruiken om het EIGRP-proces te activeren.|
|``R1(config-rtr)#interface [int-id]``| Ga naar **elke interface** waar je EIGRP voor IPv6 wilt inschakelen.|
|``R1(config-if)#ipv6 eigrp [AS-number]``| Gebruik dit commando met hetzelfde AS-nummer als bij de EIGRP voor IPv6-configuratie op **elke gewenste interface**.|

💡 Opmerking:

de configuratieprompt bij het configureren van EIGRP voor IPv6 is anders.

Waar EIGRP voor IPv4 wordt geconfigureerd onder de configuratiemodus met prompt `R1(config-router)#`, wordt EIGRP voor IPv6 geconfigureerd onder de modus/prompt `R1(config-rtr)#`.

### EIGRP IPv6 verificatie

Dezelfde verificatie- of troubleshooting-commando's die je voor IPv4 gebruikt, kun je ook toepassen op IPv6-implementaties.  
Vervang simpelweg `ip` door `ipv6` in je commando's.

Voorbeeld:

* `show ip route` → `show ipv6 route`
* `show ip protocols` → `show ipv6 protocols`
* `show ip eigrp neighbors` → `show ipv6 eigrp neighbors`

|Commando |Beschrijving |
|---------|-------------|
|``R1#show ipv6 protocols``| Verifieert de huidige geconfigureerde waarden voor EIGRP (en eventuele andere ingeschakelde routingprotocollen op het toestel).|
|``R1#show ipv6 eigrp neighbors``|  Toont de neighbor table. Gebruik dit om te verifiëren dat de router zijn EIGRP-buren (neighbors) herkent.|
|``R1#show ipv6 route eigrp``| Toont alleen de EIGRP-geleerde routes in de IPv6 routing tabel.|
|``R1#show ipv6 eigrp interface``|  Verifieert welke interfaces EIGRP voor IPv6 geactiveerd hebben, het aantal peers per interface en de transmit queues.|

---

*Gebaseerd op Cisco CCNA Routing & Switching Essentials materiaal & vertaald van r7perezyera
