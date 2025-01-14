Opmerking: deze opdrachten werken op zowel Cisco-routers als switches.

## Basisapparaatconfiguratie 

Commando|Aanvullende Notities
---|---
``CDevice>enable``|ga naar de bevoorrechte EXEC-modus
``CDevice#configure terminal``|ga naar de globale configuratiemodus
``CDevice(config)#no ip domain-lookup``|schakel DNS-lookup uit :bulb: (shift+ctrl+6) onderbreekt look-up 
``CDevice(config)#cdp run``|zorg ervoor dat CDP draait :bulb:(hoewel het standaard draait op Cisco-apparaten)
``CDevice(config)#banner motd $ message $``|stel banner in
``CDevice(config)#hostname nameofhost``|stel de hostnaam in op naamvanhost
``CDevice(config-line)#logging synchronous``|zorgt ervoor dat logberichten niet midden in een commando verschijnen
``CDevice(config-line)#history size [lines]``|specificeer de grootte (aantal regels) voor de geschiedenis van uitgevoerde opdrachten (je kunt je geschiedenis bekijken met ``R1#show history``)
``CDevice(config-line)#exec-timeout [minutes] [seconds]``|stel de time-out in voor inactiviteit van de EXEC-sessie
``CDevice(config-line)#end``|ga naar EXEC-bevoorrechte modus, waar het volgende commando zal worden uitgevoerd
``CDevice#copy running-config startup-config``|Slaat de lopende configuratie op in het NVRAM


## Beveiligingsinstellingen

| Commando                           | Aanvullende Notities                                              |
|-----------------------------------|-------------------------------------------------------------|
| `CDevice(config)#enable secret s8cr8t` | Stel het enable-wachtwoord (versleuteld bevoorrecht EXEC-wachtwoord) in op `s8cr8t` |
| `CDevice(config)#line console 0`<br>`CDevice(config-line)#password p@ssw0rd`<br>`CDevice(config-line)#login`<br>`CDevice(config-line)#exit`  | Stel het console-wachtwoord in op `p@ssw0rd`                          |
| `CDevice(config)#security password min-length 8` | Stel de minimale wachtwoordlengte in op 8                             |
| `CDevice(config)#service password-encryption` | Versleutel alle platte tekst wachtwoorden
| `CDevice(config)#username user secret cisco` | Maak gebruiker 'user' aan met wachtwoord 'cisco'

## SSH configureren
Commando|Beschrijving
---|---
| `CDevice#show ip ssh`|Gebruik dit om te verifiëren dat de switch SSH ondersteunt
| `CDevice(config)#ip domain-name [domain-name]`|stel de domeinnaam in
| `CDevice(config)#crypto key generate rsa general-keys modulus 4096`|Genereer een RSA-cryptosleutel met een modulus van 4096 bits
| `CDevice(config)#username [admin] secret [ccna]`|maak gebruiker 'admin' aan met wachtwoord 'ccna'
| `CDevice(config)#username user secret cisco` | Maak gebruiker 'user' aan met wachtwoord 'cisco'
| `CDevice(config)#line vty 0 15`<br>`CDevice(config-line)#login local`<br>`CDevice(config-line)#transport input ssh`<br>`CDevice(config-line)#exit`  | Configureer de vty-lijnen om alleen SSH-toegang te accepteren
| `CDevice(config)##ip ssh version 2`|schakel SSH versie 2 in
| `CDevice(config)##crypto key zeroise rsa`|:warning: gebruik om **RSA-sleutelpaar te verwijderen**

### SSH-configuratie wijzigen
Commando|Beschrijving
---|---
| `CDevice(config)##ip ssh time-out [time]`|Wijzig de time-outinstelling (tijd in seconden)
| `CDevice(config)##ip ssh authentication-retries [retries]`|Wijzig het aantal toegestane authenticatiepogingen

Verifieer je nieuw geconfigureerde instellingen met ``S1#show ip ssh``

----

Je kunt dezelfde reeks, die in het onderstaande tekstvak staat, kopiëren en plakken in je Cisco-apparaat, of het nu een fysiek Cisco-apparaat is of een gesimuleerd apparaat in een omgeving zoals Packet Tracer.  
```
ena
conf t
no ip domain-lookup
cdp run
line con 0
logging syn
his size 50
exec-timeout 25 0
end
copy run start

<< select and copy up to the line above this "marker" with your cursor
```
:bulb: Merk op dat de onderstaande opdrachten zijn afgekort. Ze zouden echter prima moeten werken.  
:bulb: Zorg ervoor dat je selecteert en kopieert tot aan de lege regel onder het laatste commando. Op die manier wordt dat laatste commando uitgevoerd zonder dat je handmatig op enter hoeft te drukken. 


## Andere tips en hints voor je initiële configuraties
:bulb:Als er niet-Cisco-apparaten op je netwerk zijn, wil je misschien ook LLDP (Link-Layer Discovery Protocol) inschakelen, gebruik:  
````CDevice(config)#lldp run````
