# Multicast

1. Un servidor, amb adreça IP 160.231.5.69, envia datagrames al grup de multicast 230.5.16.15.L’adreça MAC del servidor és 00:00:1B:3E:99:F0. Escriu les adreces IP i MAC d’origeni destinació dels datagrames que envia el servidor. Fes el mateix amb la resposta d’unmembre del grup.

Paquet del servidor:
```
Origen MAC: 00:00:1B:3E:99:F0
Destinació MAC: 01:00:5E:05:10:0F
Origen IP: 160.231.5.69
Destinació IP: 230.5.16.15
```

Resposta d'un membre:
```
Origen MAC: #MAC del membre
Destinació MAC: #MAC del router de la xarxa del membre
Origen IP: #IP del membre
Destinació IP: 160.231.5.69
```


2. A quin rang d’adreces IP multicast li correspon l’adreça Ethernet 01:00:5E:00:01:02? I a 01:00:5E:A5:CB:D2?

+------------------+-------------------------------------+
| Ethernet         | IPs                                 |
+------------------+-------------------------------------+
|01:00:5E:00:01:02 | 1110****.*0000000.00000001.00000010 |
+------------------+-------------------------------------+
|01:00:5E:A5:CB:D2 | 1110****.*0100101.11001011.11010010 |
+------------------+-------------------------------------+

3. A quants grups multicast pot afegir-se una interfície de xarxa sense repetir cap adreçaEthernet?

Com que la part de l'identificador de grup que es transfereix a la MAC té una mida de 23 bits, la solució és €2^23€ grups diferents.

4. Incrementa el broadcasting i multicasting el tràfic de la xarxa respecte a unicast? Justificala resposta.

Depèn del sistema físic sobre el que estigui montada la xarxa:
- En sistemes basats en circuits fer un broadcast implica establir una conexió amb cada un dels nodes i enviar un datagrama de manera que multicast i broadcast augmenten molt el tràfic.
- En Ethernet el tràfic no augmenta ja que per defecte tots els missatges físics arriben a tots els hosts de la xarxa, multicast/broadcast només determina quins hosts processaran el missatge.

5. Dibuixa un diagrama de temps on es vegin dos hosts diferents afegint-se a un grup, elrouter multicast fent el polling i els hosts deixant el grup.

Host A -> Adreça IP del grup : JOIN
Host B -> Adreça IP del grup : JOIN
Router -> Adreça IP multicast de tots els hosts de la xarxa : Polling
Host A o B (el qué hagi  obtinugt un temps aleatori més curt) -> Adreça IP del grup : Pertinença
Més tard:
Host A -> Adreça IP del grup : LEAVE
Host B -> Adreça IP del grup : LEAVE
Enviat immediatament després del segon LEAVE:
Router -> Adreça IP multicast de tots els hosts de la xarxa : Polling
Ningú respón i el router elimina les dades que tenia sobre el grup i, en cas d'estar a RPM informa a altres routers de la extinció del grup a la seva xarxa.

6. Hi ha manera de fer multicasting IP entre xarxes diferents (p.e. a Internet) si no tenimrouters multicast a totes les xarxes intermitjes?

No, per que els routers de les xarxes intermitges no sabran com enrutar els paquets multicast.

7. Si el hardware de xarxa no suporta multicast, podem utilitzar multicast IP? Com? Quineslimitacions comportarà això?

En aquest cap podem enviar paquets multicast IP (ja que només requereixen canviar l'adreça IP de destinatari) però no en podem rebre (que requereix de hardware amb suport per multicast).

8. A IGMP, què passa si es perd una resposta (del polling) d’un host? I si es perd una peticiód’enquesta?

Si es perd una resposta de polling poden passar dos coses:
- El host que ha enviat la resposta és l'únic host del grup que es troba a la xarxa. Llavors el servidor assumeix que com no ha contestat ningú es que no hi ha hosts subscrits a aquest grup a la xarxa i elimina les dades que té del grup, tancant-lo.
- Hi ha més hosts subscrits al grup multicast dins de la mateix xarxa. En aquest cas els altres hosts veuran que no s'ha respost el missatge i quan passi el seu delay de contestar (seleccionat aleatoriament) contestaran al router, obtenint el mateix comportament final que s'hagués obtingut si el paquet no s'hagués perdut.

Si es perd la petició d'enquesta els hosts no ho veuran i no contestaràn. El router, que no sap que el seu paquet s'ha perdut, considerarà la situació com que no hi ha cap host subscrit al grup en la seva xarxa i tancarà el grup.
