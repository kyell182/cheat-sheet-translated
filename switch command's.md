Nuttige verzameling commando's voor Cisco Switches.

Gebaseerd op Cisco Networking Academy CCNA versie 6 en versie 7 cursusmateriaal, en aanbevolen voor CCNA examenvoorbereiding.

## Inhoudsopgave

- [Inhoudsopgave](#inhoudsopgave)
- [Voor we beginnen: Configuratiemodi](#voor-we-beginnen-configuratiemodi)
- [Belangrijke "show" commando's](#belangrijke-show-commandos)
  - [Informatie filteren uit show commando's](#informatie-filteren-uit-show-commandos)
- [Meer dan één interface tegelijk beheren](#meer-dan-één-interface-tegelijk-beheren)
- [VLANs](#vlans)
  - [VLANs configureren](#vlans-configureren)
  - [Een VLAN verwijderen](#een-vlan-verwijderen)
  - [Interface(s) uit een VLAN verwijderen](#interfaces-uit-een-vlan-verwijderen)
  - [IEEE 802.1q trunk links configureren](#ieee-8021q-trunk-links-configureren)
- [Dynamic Trunking Protocol (DTP)](#dynamic-trunking-protocol-dtp)
- [VLANs oplossen (Troubleshooting)](#vlans-oplossen-troubleshooting)
- [Trunks oplossen (Troubleshooting)](#trunks-oplossen-troubleshooting)
- [Voice VLANs](#voice-vlans)
- [SSH configureren](#ssh-configureren)
  - [SSH configuratie wijzigen](#ssh-configuratie-wijzigen)
- [Port Security](#port-security)
  - [🔒 Dynamische Port Security configureren](#-dynamische-port-security-configureren)
  - [🔒 Sticky Port Security configureren](#-sticky-port-security-configureren)
  - [🔒 ✅ Port Security \& veilige MAC-adressen verifiëren](#--port-security--veilige-mac-adressen-verifiëren)
  - [Een err-disabled interface weer activeren](#een-err-disabled-interface-weer-activeren)
- [VLAN trunking protocol (VTP)](#vlan-trunking-protocol-vtp)
  - [VTP verificatie](#vtp-verificatie)
- [Spanning Tree Protocol (STP)](#spanning-tree-protocol-stp)
  - [Bridge ID configuratie](#bridge-id-configuratie)
  - [Bridge ID Verificatie](#bridge-id-verificatie)
  - [PortFast en BPDU guard (Bridge Protocol Data Unit)](#portfast-en-bpdu-guard-bridge-protocol-data-unit)
  - [PortFast en BPDU guard verificatie](#portfast-en-bpdu-guard-verificatie)
  - [Rapid PVST+ configureren](#rapid-pvst-configureren)
  - [Algemene STP verificatie commando's](#algemene-stp-verificatie-commandos)
- [EtherChannel](#etherchannel)
  - [PortChannel interface aanvullende configuratie](#portchannel-interface-aanvullende-configuratie)
  - [Beschikbare EtherChannel modi](#beschikbare-etherchannel-modi)

## Voor we beginnen: Configuratiemodi

Drie basis configuratiemodi waar we al mee bekend zijn (je zult ze hieronder veel tegenkomen).

| Prompt | Apparaat configuratiemodus | Commando om modus te wijzigen (huidig -> volgend) |
| ----- | ----- | ----- |
| `S1>` | EXEC modus | type enable om naar volgende modus te gaan` |
| `S1#` | Geprivilegieerde EXEC modus | type configure terminal om naar volgende modus te gaan |
| `S1(config)#` | Globale configuratiemodus | N.V.T. |

Veelvoorkomende afkortingen voor bovenstaande commando's (gescheiden door komma's):

```bash
en, ena
conf t, config term
```

## Belangrijke "show" commando's

Let op:

deze commando's worden uitgevoerd in de bevoorrechte EXEC-modus (S1# prompt).

Je kunt ze vanuit de globale configuratiemodus (S1(config)# prompt) uitvoeren door het trefwoord **'do'** vóór het commando te plaatsen.

Voorbeeld:

``S1(config)#do show ip interface brief``

| Commando | Beschrijving |
| ----- | ----- |
| `S1#show running-config` | Toont de huidige configuratie die in RAM is opgeslagen. |
| `S1#show history` | Geeft een overzicht van de laatst gebruikte commando's in je huidige sessie. |
| `S1#show interface [int-id]` | Handig om fouten te detecteren of te verifiëren of pakketten worden verzonden en ontvangen. |
| `S1#show mac address-table` | Toont de MAC-adresstabel. |
| `S1#show port-security` | Toont de Port Security configuratie voor alle interfaces. |
| `S1#show port-security interface [int-id]` | Toont de Port Security configuratie van een interface. |
| `S1#show vlan` | Toont VLAN-informatie. |
| `S1#show vlan brief` | Toont alleen VLAN's, statussen, namen en toegewezen poorten. |
| `S1#show interface vlan [id]` | Toont gedetailleerde informatie voor een specifieke VLAN-interface. |
| `S1#show interfaces trunk` | Toont informatie over trunk-interfaces. |

### Informatie filteren uit show commando's

Sommige commando's, zoals

`show running-config`

genereren meerdere regels uitvoer.

Om de uitvoer te filteren, kun je het pipe ( | ) teken gebruiken samen met een **filterparameter** en een **filteruitdrukking**.

| Filterparameters | Effect |
| ----- | ----- |
| `section [filtering-expression]` | Toont het gedeelte van de filteruitdrukking. |
| `include [filtering-expression]` | Neemt alleen de regels van de uitvoer op die exact overeenkomen met de filteruitdrukking. |
| `exclude [filtering-expression]` | Sluit alle regels van de uitvoer uit die overeenkomen met de filteruitdrukking. |
| `begin [filtering-expression]` | Toont alle regels van de uitvoer, beginnend vanaf de regel die overeenkomt met de filteruitdrukking. |

Gebruik:

Hier is een voorbeeld van het gebruik van filtering met een show commando:

``R1#show running-config | include line con``

💡 **ProTip:**

Standaard bestaat het uitvoerscherm uit 24 regels.

 Als je het aantal uitvoerregels dat op het terminalscherp wordt weergegeven wilt wijzigen, kun je het commando gebruiken:

``R1# terminal length [aantal-regels]``

⚠️ Helaas wordt dit commando NIET ondersteund in Cisco Packet Tracer (getest op versie 7.2.2).

## Meer dan één interface tegelijk beheren

Als we een reeks commando's op meer dan één poort willen uitvoeren, maakt het selecteren van een interfacereeks de taak een stuk eenvoudiger.

Gebruik:

 `S1(config)#interface range [typeModule/eersteNummer]-[laatsteNummer]`

| typeModules | Enkele mogelijke afkortingen |
| ----- | ----- |
| `FastEthernet` | `f, fa,` |
| `GigabitEthernet` | `g, gi, gig,` |

Hier is een voorbeeld:

``S1(config)#interface range f0/1-12``

Merk op dat je meerdere reeksen in één commando kunt selecteren.

Hier is een voorbeeld:

`S1(config)#interface range f0/1-12, 15-24, g0/1-2`

Mogelijk moet je het vaak gebruiken in scenario's waar de volgende commandoblokken worden gebruikt.

## VLANs

### VLANs configureren

| Commando | Beschrijving |
| ----- | ----- |
| `S1(config)#vlan [vlan-ID]` | Maak VLAN aan en wijs het VLAN-nummer toe. |
| `S1(config-vlan)#name [naam]` | Wijs een naam toe aan het VLAN. |

Nu is het tijd om poorten toe te wijzen aan het nieuw aangemaakte VLAN.

| Commando | Beschrijving |
| ----- | ----- |
| `S1(config)#interface [int-id]` | Denk eraan, **"interface-reeks"** kan handig zijn. |
| `S1(config-if)#switchport mode access` | Stel de interfacemodus in op access. |
| `S1(config-if)#switchport access vlan [vlan-id]` | Wijs/wijzig poort-VLAN. |

### Een VLAN verwijderen

| Commando | Beschrijving |
| ----- | ----- |
| `S1(config)#no vlan [vlan-id]` | ⚠️ Verwijdert het gespecificeerde VLAN. |
| `S1(config)#delete flash:vlan.dat` | ⚠️ Wis de hele VLAN-database. |

### Interface(s) uit een VLAN verwijderen

| Commando | Beschrijving |
| ----- | ----- |
|`S1(config)#interface [int-id]` | Ga naar de interfaceconfiguratie van de opgegeven poort. Denk eraan, je kunt ook een interfacereeks selecteren met **"interface range"**. |
|`S1(config-if)#no switchport access vlan [vlan-id]` | Verwijder het VLAN uit de poort. |

**Ken het verschil!**

>💡Wanneer een VLAN wordt verwijderd, wordt elke switchpoort die aan dat VLAN is toegewezen, inactief.
>💡Aan de andere kant, wanneer
>
>`no switchport access vlan [vlan-id]`
>
>wordt uitgevoerd op een switchpoort, wordt de poort teruggestuurd naar VLAN 1.

### IEEE 802.1q trunk links configureren

| Commando | Beschrijving |
| ----- | ----- |
| `S1(config)#interface [int-id]` | Toegang tot de interface. |
| `S1(config-if)#switchport mode trunk` | Stel de interface in op trunk-modus. |
| `S1(config-if)#switchport trunk native vlan [vlan-id]` | Specificeer de native VLAN van de link. |
| `S1(config-if)#switchport trunk allowed vlan [vlan-list]` | **Alle** toegestane VLAN-ID's. |
| `S1(config-if)#switchport trunk allowed vlan remove [vlan-id]` | 🚷**VERBIEDT ALLEEN** het VLAN met de opgegeven ID op de trunk-interface. |

💡 **Tip:**

Je wilt misschien ook de routercommando's bekijken die nodig zijn voor inter-VLAN-routing via Router-On-A-Stick.

## Dynamic Trunking Protocol (DTP)

Dit Cisco-eigen protocol draagt bij aan de configuratie van trunking-interfaces tussen Cisco-switches.

💡Onthoud:

De standaardconfiguratie voor interfaces op Cisco Catalyst 2960 en 3650 switches is

`dynamic auto`

| Commando | Beschrijving |
| ----- | ----- |
| `S1(config-if)#switchport mode trunk` | Configureert een interface om specifiek in **trunk-modus** te zijn. Onderhandelt ook om de naburige link om te zetten naar een trunk. |
| `S1(config-if)#switchport mode access` | Configureert een interface om specifiek in **access-modus** te zijn, een NIET-trunk interface, zelfs als de naburige interface in trunk-modus is. |
| `S1(config-if)#switchport mode dynamic auto` | Interface **wordt alleen trunk** als de naburige interface in de modus `trunk` of `dynamic desirable` staat. |
| `S1(config-if)#switchport mode dynamic desirable` | Interface **wordt trunk** als de naburige interface in de modus `trunk`, `dynamic auto` of `dynamic desirable` staat. |
| `S1(config-if)#switchport nonegotiate` | ⛔ Stopt DTP-onderhandeling, waarin interfaces zich kunnen begeven, zoals je hierboven zag, d.w.z. een interface zal zijn modus NIET wijzigen, zelfs als de naburige interface deze via onderhandeling zou kunnen wijzigen. |

## VLANs oplossen (Troubleshooting)

| Commando | Beschrijving |
| ----- | ----- |
| `S1#show vlan` | Controleer of een poort tot het verwachte VLAN behoort. |
| `S1#show mac address-table` | Controleer welke adressen zijn geleerd op een bepaalde poort van de switch, en aan welk VLAN die poort is toegewezen. |
| `S1#show interfaces [int-id] switchport` | Handig om te verifiëren of een inactief VLAN aan een poort is toegewezen. |

## Trunks oplossen (Troubleshooting)

| Commando | Beschrijving |
| ----- | ----- |
| `S1#show interfaces trunk` | Controleer of de native VLAN-ID aan beide uiteinden van de link overeenkomt - controleer of een trunk-link is tot stand gebracht tussen switches. |

## Voice VLANs

VLAN's die spraakverkeer ondersteunen, hebben meestal Quality of Service (QoS). Spraakverkeer moet een betrouwbaar label hebben.

Let op dat de implementatie van QoS buiten het bereik van de CCNA2 (versie 6) cursus valt.

| Commando | Beschrijving |
| ----- | ----- |
| `S1(config)#interface [int-id]` | Toegang tot de interface waarop de voice VLAN zal worden toegewezen. |
| `S1(config-if)#switchport mode access` | Configureert een interface om specifiek in **access-modus** te zijn, dus geen trunk, zelfs als de naburige interface in trunk-modus is. |
| `S1(config-if)#switchport access vlan [vlan-id]` | Wijs de interface toe aan het opgegeven VLAN. |
| `S1(config-if)#mls qos trust cos` | Stel de vertrouwde status van een interface in en geef aan welke pakketvelden worden gebruikt om verkeer te classificeren. |
| `S1(config-if)#switchport voice vlan [vlan-id]` | Wijs een voice VLAN toe aan die poort. |

## SSH configureren

| Commando | Beschrijving |
| ----- | ----- |
| `S1#show ip ssh` | Gebruik dit om te verifiëren dat de switch SSH ondersteunt. |
| `S1(config)#ip domain-name [domein-naam]` | Configureer de domeinnaam van de switch. |
| `S1(config)#crypto key generate rsa` | Genereer een RSA-sleutelpaar. |
| `S1(config)#username [admin] secret [ccna]` | Maak een gebruikersnaam en wachtwoord aan. |
| `S1(config)#line vty 0 15` | Toegang tot de VTY-lijnen (virtuele terminal lijnen). |
| `S1(config-line)#transport input ssh` | Configureer de VTY-lijnen om SSH-verbindingen te accepteren. |
| `S1(config-line)#login local` | Configureer de VTY-lijnen om lokale authenticatie te gebruiken. |
| `S1(config-line)#exit` | Verlaat de configuratiemodus van de VTY-lijnen. |
| `S1(config)#ip ssh version 2` | Schakel SSH versie 2 in. |
| `S1(config)#crypto key zeroise rsa` | ⚠️ Gebruik dit om het RSA-sleutelpaar te verwijderen. |

💡Let op:

>Een domeinnaam is vereist voor SSH-configuratie op een switch, omdat deze nodig is om het RSA-sleutelpaar te genereren.
>
>Zonder domeinnaam kun je het commando `crypto key generate rsa` niet uitvoeren en dus geen SSH inschakelen.

### SSH configuratie wijzigen

| Commando | Beschrijving |
| ----- | ----- |
| `S1(config)#ip ssh time-out [tijd]` | Wijzig de time-out instelling (tijd in seconden). |
| `S1(config)#ip ssh authentication-retries [pogingen]` | Wijzig het aantal toegestane authenticatiepogingen. |

Verifieer je nieuw geconfigureerde instellingen met `S1#show ip ssh`.

## Port Security

### 🔒 Dynamische Port Security configureren

| Commando | Beschrijving |
| ----- | ----- |
| `S1(config)#interface [int-id]` | Ga naar de interfaceconfiguratie van de opgegeven poort. Je kunt ook een interfacereeks selecteren met `interface range`. |
| `S1(config-if)#switchport mode access` | Stel de interfacemodus in op access. |
| `S1(config-if)#switchport port-security` | Schakel poortbeveiliging in op de interface. |
| `S1(config-if)#switchport port-security violation [schendingsmodus]` | Stel de schendingsmodus in (protect, restrict, shutdown). |

🏆**Best practice:**
>Het is een goede beveiligings - en algemene praktijk om de `switchport mode access` expliciet in te stellen.
>
>Dit geldt ook voor Trunk-poorten (`switchport mode trunk`).

### 🔒 Sticky Port Security configureren

| Commando | Beschrijving |
| ----- | ----- |
| `S1(config)#interface [int-id]` |  Ga naar de interfaceconfiguratie van de opgegeven poort. Je kunt ook een interfacereeks selecteren met `interface range`. |
| `S1(config-if)#switchport mode access` | Stel de interfacemodus in op access. |
| `S1(config-if)#switchport port-security` | Schakel poortbeveiliging in op de interface. |
| `S1(config-if)#switchport port-security maximum [max-adressen]` | Stel het maximale aantal veilige MAC-adressen in dat op de poort is toegestaan. |
| `S1(config-if)#switchport port-security mac-address sticky` | Schakel sticky learning in. |
| `S1(config-if)#switchport port-security violation [schendingsmodus]` | Stel de schendingsmodus in (protect, restrict, shutdown). |

### 🔒 ✅ Port Security & veilige MAC-adressen verifiëren

Nu we Port Security hebben geconfigureerd, zijn de volgende commando's handig om te verifiëren en op te lossen.

| Commando | Beschrijving |
| ----- | ----- |
| `S1#show port-security interface [int-id]` | Toont de Port Security configuratie van de interface. Als er overtredingen zijn opgetreden, kunnen deze hier worden gecontroleerd. |
| `S1#show port-security address` | Toont de veilige MAC-adressen die op alle switchinterfaces zijn geconfigureerd. |
| `S1#show interface [int-id] status` | Toont de poortstatus. Handig om te verifiëren of een interface in err-disabled status is. |

### Een err-disabled interface weer activeren

💡Ter herinnering:

Na een overtreding verandert een poort in de Shutdown-overtredingsmodus zijn status in "error disabled" en wordt deze effectief uitgeschakeld. Om de werking te hervatten (verkeer verzenden en ontvangen), moeten we deze weer activeren. Hier is hoe:

- Ga naar de interface configuratiemodus met `S1(config)#interface [int-id]`.

- Sluit de interface af met `S1(config-if)#shutdown`.

- Activeer de interface weer met `S1(config-if)#no shutdown`.

## VLAN trunking protocol (VTP)

| Commando | Beschrijving |
| ----- | ----- |
| `S1(config)#vtp mode [modus]` | De modus kan `server` of `client` zijn. |
| `S1(config)#vtp password [wachtwoord]` | Optioneel wachtwoord, ⚠️ hoofdlettergevoelig. |
| `S1(config)#vtp domain [naam]` | Optioneel, ⚠️ domeinnaam is ook hoofdlettergevoelig. |
| `S1(config)#vtp pruning` | Optioneel - configureer VTP pruning op de server. |
| `S1(config)#vtp version 2` | Optioneel - schakelt VTP versie 2 in. |

❗
Vergeet hierna niet om trunk-links tussen de VTP-domein switches in te schakelen, zodat VTP-advertenties tussen de switches kunnen worden gedeeld.

Deze commandosequentie is alles wat nodig is om VTP in ons VTP-domein draaiende te krijgen. ✅

💡 **Tip:**

Er zijn 3 VTP-versies.

Versies 1 en 2 (die binnen het bereik van het CCNA-examen vallen) ondersteunen GEEN extended-range VLAN's (ID van 1006 tot 4095).

VTP-versie 3 (NIET behandeld in het CCNA-examen) ondersteunt dergelijke VLAN's wel.

### VTP verificatie

| Commando | Beschrijving |
| ----- | ----- |
| `S1#show vtp status` | Verifieer je configuratie en de status van VTP op het apparaat. |
| `S1#show vtp password` | Verifieer het geconfigureerde VTP-wachtwoord. |
| `S1#show vlan brief` | Dit VLAN-verificatiecommando kan ook nuttig zijn bij het verifiëren van de VTP-configuratie. |

## Spanning Tree Protocol (STP)

### Bridge ID configuratie

| Commando | Beschrijving |
| ----- | ----- |
| `S1(config)#spanning-tree vlan [vlan-id] root primary` | Zorgt ervoor dat deze switch de laagste prioriteitswaarde heeft. | 
| `S1(config)#spanning-tree vlan [vlan-id] root secondary` | Gebruik dit als een alternatieve bridge-configuratie gewenst is. Stelt de prioriteitswaarde van de switch in om ervoor te zorgen dat deze de root bridge wordt als de primaire root bridge faalt. |
| `S1(config)#spanning-tree vlan [vlan-id] priority [prioriteit]` | Configureer handmatig de prioriteitswaarde van de bridge. | 

💡 **Onthoud:**

Prioriteitswaarden liggen tussen 0 en 61.440.

⚠️

De prioriteitswaarde kan alleen een veelvoud van 4096 zijn.

### Bridge ID Verificatie

| Commando | Beschrijving |
| ----- | ----- |
| `S1#show spanning-tree` | Verifieer de huidige spanning-tree instanties en root bridges. |

### PortFast en BPDU guard (Bridge Protocol Data Unit)

Moet alleen worden geconfigureerd op interfaces die point-to-point zijn verbonden met een eindapparaat.

>**PortFast**
>
>is een functie van het Spanning Tree Protocol (STP) die ervoor zorgt dat een switchpoort direct in de forwarding state gaat, zonder eerst de gebruikelijke STP-stadia (listening en learning) te doorlopen.
>
>Dit is vooral handig op poorten die verbonden zijn met eindapparaten (zoals pc's of printers), zodat deze apparaten direct netwerktoegang krijgen na het aansluiten.

⚠️
> Let op:
>
> PortFast mag **alleen** worden ingeschakeld op access-poorten die verbonden zijn met eindapparaten, nooit op trunk-links tussen switches.
>Anders kunnen er netwerkloops ontstaan.

>
>**BPDU Guard**
>
>wordt vaak samen met PortFast gebruikt om te voorkomen dat er per ongeluk switches op een PortFast-poort worden aangesloten.
>Zodra er een BPDU wordt ontvangen op een PortFast-poort met BPDU Guard, wordt de poort direct uitgeschakeld (err-disabled).

| Commando | Beschrijving |
| ----- | ----- |
| `S1(config)#interface [int-id]` | Toegang tot de interface. |
| `S1(config)#interface range [int-type] [laagste-id]-[hoogste-id]` | Toegang tot een reeks aaneengesloten interfaces indien nodig. |
| `S1(config-if)#switchport mode access` | Als een goede gewoonte, hard-type dit commando zodat de switchpoort in access-modus is. |
| `S1(config-if)#spanning-tree portfast` | Schakelt PortFast in op de access-poort(en). |
| `S1(config-if)#spanning-tree bpduguard enable` | Schakelt BPDU Guard in op de access-poort(en). |
| `S1(config)#spanning-tree portfast default` | ⚠️ Configureert PortFast als standaard voor alle switchinterfaces. |
| `S1(config)#spanning-tree bpduguard default` | ⚠️ Configureert BPDU Guard als standaard voor alle switchinterfaces. |

### PortFast en BPDU guard verificatie

| Commando | Beschrijving |
| ----- | ----- |
| `S1#show running-config begin spanning-tree` | Toont het gedeelte van de huidige configuratie dat overeenkomt met spanning-tree. |
| `S1#show running-config interface [int-id]` | Toont het gedeelte van de huidige configuratie dat overeenkomt met de interface. |

### Rapid PVST+ configureren

PVST+ is de STP-variant ( spanning tree protocol ) die standaard werkt op Cisco-switches.

Om Rapid PVST+ te configureren, hoeven we alleen een globaal commando in te typen.

| Commando | Beschrijving |
| ----- | ----- |
| `S1(config)#spanning-tree mode rapid-pvst` | Configureer Rapid PVST+ als de STP-modus op de switch. |
| `S1(config-if)#spanning-tree link-type point-to-point` | Specificeer dat een link point-to-point is. |

### Algemene STP verificatie commando's

| Commando | Beschrijving |
| ----- | ----- |
| `S1#clear spanning-tree detected-protocols (interface [int-id])` | Dwingt heronderhandeling af met naburige switches op alle interfaces of de gespecificeerde interface. |
| `S1#show spanning-tree` | Toont STP-informatie - handig om informatie te vinden over de bridge waarin je je bevindt, en de root bridge in één oogopslag. |
| `S1#show spanning-tree active` | Toont STP-informatie voor alleen actieve interfaces. |
| `S1#show spanning-tree brief` | Overzichtelijke informatie voor alle STP-instanties die op de switch draaien. |
| `S1#show spanning-tree detail` | Gedetailleerde informatie voor alle STP-instanties die op de switch draaien. |
| `S1#show spanning-tree interface [int-id]` | STP-informatie voor de gespecificeerde interface. |
| `S1#show spanning-tree vlan [vlan-id]` | STP-informatie voor het gespecificeerde VLAN. |
| `S1#show spanning-tree summary` | Samenvatting van STP-poortstatussen. |

## EtherChannel

| Commando | Beschrijving |
| ----- | ----- |
| `S1(config)#interface range [start-int]-[end-int]` | Begin met het selecteren van de interfaces die gebundeld moeten worden in één enkele logische link, d.w.z. de EtherChannel. |
| `S1(config-if-range)#channel-group [nummer] mode [modus]` | Specificeer de groeps-ID (1 tot en met 6) en de [werkings modus](#beschikbare-etherchannel-modi) van de EtherChannel. |

### PortChannel interface aanvullende configuratie

| Commando | Beschrijving |
| ----- | ----- |
| `S1(config)#interface port-channel [nummer]` | Ga naar de configuratiemodus van de poortkanaalinterface om instellingen te wijzigen. |
| `S1(config-if)#switchport mode trunk` | Stel de interface in op trunk-modus, zodat deze verkeer van meerdere VLAN's kan dragen. |
| `S1(config-if)#switchport trunk native vlan [native-vlan-id]` | Specificeer de native VLAN van de link. |
| `S1(config-if)#switchport trunk allowed vlan [vlan-id-1 (,vlan-id-2,...)]` | Specificeer toegestane VLAN's (VLAN ID's) op de trunk link. |
| `S1(config-if)#switchport trunk allowed vlan add [vlan-id-1 (,vlan-id-2,...)]` | Voeg VLAN's toe aan de lijst met reeds toegestane VLAN's op de trunk link. |

⚠️

De EtherChannel-onderhandelingsprotocollen die u gebruikt voor uw interfacebundels MOETEN OVEREENKOMEN AAN BEIDE ZIJDEN, of het nu LACP, PAgP (Cisco Proprietary), of geen protocol (`on` modus) is.

**LACP** (Link Aggregation Control Protocol) en **PAgP** (Port Aggregation Protocol) zijn protocollen die gebruikt worden om meerdere fysieke switchpoorten te bundelen tot één logische verbinding, genaamd **EtherChannel**.

- **LACP** is een open standaardprotocol (IEEE 802.3ad) en werkt tussen apparaten van verschillende fabrikanten.
- **PAgP** is een Cisco-eigen protocol en werkt alleen tussen Cisco-apparaten.

Beide protocollen zorgen ervoor dat de poorten aan beide kanten van de verbinding correct samenwerken en automatisch gebundeld worden, waardoor je meer bandbreedte en redundantie krijgt.

### Beschikbare EtherChannel modi

| EC modus | Beschrijving |
| ----- | ----- |
| `active` | LACP onvoorwaardelijk inschakelen. |
| `auto` | PAgP alleen inschakelen als een ander PAgP-apparaat wordt gedetecteerd. |
| `desirable` | PAgP onvoorwaardelijk inschakelen. |
| `on` | EtherChannel alleen inschakelen. |
| `passive` | LACP alleen inschakelen als een ander LACP-apparaat wordt gedetecteerd.|

---
*Gebaseerd op Cisco CCNA Routing & Switching Essentials materiaal & vertaald van r7perezyera
