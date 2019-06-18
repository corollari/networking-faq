1. Comenta les restriccions que tindran tres xarxes amb espais d’adreçament diferents connectades, de manera asimètrica, a través de dos routers fent NAT d’origen i NAT de destinació. Quantes vegades es traduiran les adreces de destinació? I les d’origen? Com fa Skype, per exemple, per a connectar dos interlocutors que estan darerra de NAT?

Les limitacions (apart de les limitacions inherents de NAT) seràn que només els hosts d'una xarxa podran iniciar conexions amb els hosts de l'altra xarxa.
Les adreces de destinació seran traduides una vegada (al passar pel router DNAT)i les d'origen una vegada també (al passar per el SNAT).
Skype utilitzava (ja no utilitza P2P) NAT punchthrough per a conectar amb hosts a darrera de NAT, això funcionava a base d'enviar un paquet UDP, el qual generava una entrada a la taula de NAT que permetia a altres hosts d'internet accedir al host. 

2. Imagina que construïm una internet paralel.la a la Internet, que utilitzés el seu mateix espaid’adreçament. Podríem utilitzar NAT per interconnectar-les, considerant que totes duessón arbitràriament grans? Es podria fer un túnel d’un host de la internet a un host dela Internet? Raona les teves respostes. Es possible superar la limitació d’adreces IPv4utilitzant NAT? Fins a quin punt és escalable aquesta solució?

No es podrien interconnectar amb NAT. Demostració per contraexemple: si són arbitrariment grans significa que podrien arribar a ser el màxim gran possibles i ocupar tot l'espai d'adreçament IP, de menera que, com totes les IPs estan ocupades, no hi hauria cap adreça lliure per a ser tulitzada pel router(s) que realitzessin NAT.
Realitzar un tunel si que seria viable. Aquest es podria realitzar conectant un host de un internet amb un host de l'altre internet físicament i establint un tunel entre aquests dos.
Es possible superar la limitació d’adreces IPv4 utilitzant NAT, això es pot aconseguir creant petites xarxes que estiguin conectades a internet a través d'un NAT de manera que l'adreça pública del NAT sigui l'única que estigui a internet. D'aquesta manera podem aconseguir conectar diversos hosts a internet utilitzant només una adreça IP.

3. Tenim una xarxa privada amb un servidor (192.168.2.2). El nostre router fa NAT (SNAT) ia més permet l’accés des d’Internet (els datagrames al port 22 seran re-encaminats al port 122 del servidor intern (DNAT)). Mostra el contingut de la taula NAT després que a) unclient s’hagi connectat des d’Internet. b) un altre client, amb el mateix port, s’hagi connec-tat des d’Internet. c) el primer client, des d’un port diferent, s’hagi tornat a connectar. Pertots els casos mostra també les connexions tal com les veuen client, servidor i router (fentun netstat, per exemple). Hi hauria algun problema si el servei ofert per aquest port 22 és SSH?

a)
| IP extern   | Port extern | IP interna | Port intern | Protocol | Port NAT extern |Port NAT intern |
|-------------|-------------|------------|-------------|----------|-----------------|----------------|
| IP client 1 |Port client 1|192.168.2.2 |   122       |  TCP     | 22              | P_NAT_1        |

Client: Conexió TCP entre `IP client 1`:`Port client 1` i `IP externa NAT`:22
Servidor: Conexió TCP entre `IP interna NAT`:`P_NAT_1` i `192.168.2.2`:122
NAT:
- Conexió TCP entre `IP client 1`:`Port client 1` i `IP externa NAT`:22
- Conexió TCP entre `IP interna NAT`:`P_NAT_1` i `192.168.2.2`:122

b)
| IP extern   | Port extern | IP interna | Port intern | Protocol | Port NAT extern |Port NAT intern |
|-------------|-------------|------------|-------------|----------|-----------------|----------------|
| IP client 1 |Port client 1|192.168.2.2 |   122       |  TCP     | 22              | P_NAT_1        |
| IP client 2 |Port client 2|192.168.2.2 |   122       |  TCP     | 22              | P_NAT_2        |

Client: Conexió TCP entre `IP client 2`:`Port client 2` i `IP externa NAT`:22
Servidor: Conexió TCP entre `IP interna NAT`:`P_NAT_2` i `192.168.2.2`:122
NAT:
- Conexió TCP entre `IP client 2`:`Port client 2` i `IP externa NAT`:22
- Conexió TCP entre `IP interna NAT`:`P_NAT_2` i `192.168.2.2`:122

c)
| IP extern   | Port extern   | IP interna | Port intern | Protocol | Port NAT extern |Port NAT intern |
|-------------|---------------|------------|-------------|----------|-----------------|----------------|
| IP client 1 |Port client 1.a|192.168.2.2 |   122       |  TCP     | 22              | P_NAT_1        |
| IP client 2 |Port client 2  |192.168.2.2 |   122       |  TCP     | 22              | P_NAT_2        |
| IP client 1 |Port client 1.b|192.168.2.2 |   122       |  TCP     | 22              | P_NAT_3        |

Client: Conexió TCP entre `IP client 1`:`Port client 1.b` i `IP externa NAT`:22
Servidor: Conexió TCP entre `IP interna NAT`:`P_NAT_3` i `192.168.2.2`:122
NAT:
- Conexió TCP entre `IP client 1`:`Port client 1.b` i `IP externa NAT`:22
- Conexió TCP entre `IP interna NAT`:`P_NAT_3` i `192.168.2.2`:122

Nota: Com que en cada conexió el port del client es triat de forma aleatoria els ports utilitzats pel client 1 en les dos reconexions seràn molt probablement diferents.
P_NAT_1, 2 i 3 són també elegits de forma aleatoria per el router NAT

**Hi hauria algun problema si el servei ofert per aquest port 22 és SSH?** No, hi hauria problemes si fossin conexions de més d'un host de la xarxa privada a un host extern però com en aquest cas són múltiples hosts externs connectats a un únic host de la xarxa privada no hi pot haver confusió.

4. En el mateix escenari del problema anterior, suposa que tenim dos hosts interns més amb aquests casos: a) tots dos hosts accedeixen al mateix servidor extern utilitzant ports locals diferents. b) igual que abans, però utilitzant el mateix port local. c) el mateix host intern, des de ports locals diferent, es connecta al mateix servidor, mateix port, d’Internet. Mostra la taula de NAT i les connexions a tots els hosts i al router en cadascun dels casos (el que mostraria un netstat, per exemple). Hi hauria algun problema si el servei extern és FTP en mode actiu?

a)
| IP extern          | Port extern      | IP interna | Port intern | Protocol | Port NAT extern |Port NAT intern     |
|--------------------|------------ -----|------------|-------------|----------|-----------------|--------------------|
| IP servidor extern |Port servei extern| IP host 1  | Port host 1 | TCP/UDP  | Port_NAT_1      | Port servei extern |
| IP servidor extern |Port servei extern| IP host 2  | Port host 2 | TCP/UDP  | Port_NAT_2      | Port servei extern |

Host: Conexió TCP entre `IP host 1`:`Port host 1` i `IP servidor extern`:`Port servei extern`
Servidor: Conexió TCP entre `IP externa NAT`:`Port_NAT_1` i `IP servidor extern`:`Port servei extern`
NAT:
- Conexió TCP entre `IP host 1`:`Port host 1` i `IP servidor extern`:`Port servei extern`
- Conexió TCP entre `IP externa NAT`:`Port_NAT_1` i `IP servidor extern`:`Port servei extern`

Pel host 2 és quasi tot igual.

b)
| IP extern          | Port extern      | IP interna | Port intern | Protocol | Port NAT extern |Port NAT intern     |
|--------------------|------------ -----|------------|-------------|----------|-----------------|--------------------|
| IP servidor extern |Port servei extern| IP host 1  | Port host 1 | TCP/UDP  | Port_NAT_1      | Port servei extern |
| IP servidor extern |Port servei extern| IP host 2  | Port host 1 | TCP/UDP  | Port_NAT_2      | Port servei extern |

Host: Conexió TCP entre `IP host 1`:`Port host 1` i `IP servidor extern`:`Port servei extern`
Servidor: Conexió TCP entre `IP externa NAT`:`Port_NAT_1` i `IP servidor extern`:`Port servei extern`
NAT:
- Conexió TCP entre `IP host 1`:`Port host 1` i `IP servidor extern`:`Port servei extern`
- Conexió TCP entre `IP externa NAT`:`Port_NAT_1` i `IP servidor extern`:`Port servei extern`

Aquestes connexions NAT no funcionaràn a no ser que el NAT utilitzi alguna tècnica complexa per l'enrutament, com, per exemple, alguna basada en els números de seqüencia, la qual pot fallar també.

c)
| IP extern          | Port extern      | IP interna | Port intern   | Protocol | Port NAT extern |Port NAT intern     |
|--------------------|------------ -----|------------|---------------|----------|-----------------|--------------------|
| IP servidor extern |Port servei extern| IP host 1  | Port host 1.a | TCP/UDP  | Port_NAT_1      | Port servei extern |
| IP servidor extern |Port servei extern| IP host 1  | Port host 1.b | TCP/UDP  | Port_NAT_2      | Port servei extern |

Host: Conexió TCP entre `IP host 1`:`Port host 1` i `IP servidor extern`:`Port servei extern`
Servidor: Conexió TCP entre `IP externa NAT`:`Port_NAT_1` i `IP servidor extern`:`Port servei extern`
NAT:
- Conexió TCP entre `IP host 1`:`Port host 1` i `IP servidor extern`:`Port servei extern`
- Conexió TCP entre `IP externa NAT`:`Port_NAT_1` i `IP servidor extern`:`Port servei extern`

**Hi hauria algun problema si el servei extern és FTP en mode actiu?** Sí, que quan FTP transferís el nombre de port a través del qual vol fer la transferència el servidor s'intentaria conectar a aquest port del NAT, però el NAT refusaria la conexió perquè no tindria aquest port registrat a la seva taula.

5. Podríem connectar xarxes privades diferents (p.e. Ethernet i ATM) utilitzant els conceptes bàsics del VPN? I si fosin del mateix tipus? Existeix alguna proposta que faci alguna cosa semblant? Raona la resposta, i comenta les limitacions que tindria una xarxa d’aquest tipus.

Sí, hauriem d'operar el VPN sobre el layer de IP i ja està, llavors el layer físic que s'utilitzes a la xarxa seria irrellevant. Una proposta per fer això seria crea una nova interfície de xarxa que en comptes d'obtenir els paquets de la tarjeta de xarxa física els obtingues d'un tunel IP-en-IP per Internet. Les limitacions d'aquesta xarxa serien una latencia molt alta i un cos molt elevat per a fer operacions com un broadcast (faria falta enviar paquets a tots els hosts de la xarxa).

6. Suposa que els routers que han d’establir un túnel entre dues xarxes d’una mateixa VPNnomés tenen una adreça IP associada a la interfície externa i cap per la interna (la interfícieinterna no té associat ni tan se vol protocol IP). Creus que seria tècnicament possible lainterconnexió dels segments? En aquest cas, com serien els datagrames que circularien perInternet? Quines limitacions i avantatges tindria aquest esquema?

Sí que seria possible, ja que dins dels datagrames que viatgen per internet del tunel hi podem ficar dades arbitràries. Els datagrames que circularien per internet estarien format per un header IP que tingués com a destinació i origen les adreces IP externes dels routers que formen el tunel i, com a payload, tindria les dades binàries que s'envien per la xarxa (en qualsevol protocol en el que estiguin).
Limitacions: Els paquets no passarien per xarxes que realitzen anàlisis de paquets (per exemple les que només deixen passar TCP al port 80).
Avantatges: Podriem utilitzar protocols arbitraris dins de les nostres xarxes.

7. Tenim necessitat d’interconnectar la nostra VPN (de 200 hosts) a Internet (qualsevol hostintern ha de poder accedir-hi) per tal d’utilitzar un servei que utilitza un protocol quefunciona directament sobre IP. Aquest protocol té un identificador en les capçaleres pera resoldre ambigüitats de fluxos d’informació a nivell d’aplicació. Com faries possibleaquest accés tenint en compte que estadísticament un 2% dels hosts voldran accedir almateix servidor d’Internet simultàniament?

Donat que no tinc suficient informació sobre l'identificador en les capçaleres del protocol com per saber si utilitzar-lo per NAT podria portar a conflictes, li assignaria al NAT unes (€0.02*2*200€ adreces externes diferents (per a cobrir el 2% de hosts que accedeixen al servidor i una mica més) i aplicaria NAT multi-adreça.

8. Seria possible combinar un esquema NAT per adreces amb un NAPT per tal d’augmentarel nombre de connexions simultànies entre la xarxa privada i Internet? En cas afirmatiu,quin serà el nombre màxim teòric de connexions tenint en compte els paràmetres de cadaesquema?

Sí que seria possible.

| Esquema            | Màximes conexions|
|--------------------|------------------|
| NAPT               | €2^16€           |
| NAPT amb k adreces | €k*2^16€         |
| NAT amb k adreces  | €k€              |

9. Suposa que un router està fent NAPT bidireccional per a permetre l’accés a un host intern des d’Internet, a més de les connexions en sentit invers. Serà possible que aquest host es connecti amb si mateix utilitzant l’adreça i port públics que utilitzaran la resta de hosts a Internet? Raona la resposta i explica els detalls d’aquest cas sobre la taula de NAT.

Depen de la implementació de NAT però si no realitza cap comprovació si que seria possible. El que passaria seria:
1. Host envia datagrama a xarxa interna
2. NAT rep el datagrama i canvia les dades de Source per les seves, envia el datagrama per Internet
3. Un router d'internet rep el datagrama i l'enruta cap al NAT
4. NAT canvia les dades de Destination per la IP i port del host a la xarxa interna i les de Source per les del NAT a la xarxa interna
5. El paquet li arriva al host

Taula NAT:
| IP extern          | Port extern         | IP interna | Port intern          | Protocol | Port NAT extern |Port NAT intern       |
|--------------------|---------------------|------------|----------------------|----------|-----------------|----------------------|
| IP NAT             | Port associat a host| IP host    | Port host            | TCP/UDP  | Port_NAT_1      | Port associat a host |
| IP NAT             | Port_NAT_1          | IP host    | Port associat a host | TCP/UDP  | Port_NAT_1      | Port_NAT_2           |

10. Com podrem interconnectar dues VPNs totalment independents que comparteixen l’espaide direccionament? Quines limitacions tindira l’esquema que proposes? Explica les difer-ències que hi hauria en un cas com aquest el considerar un esquema de VPN d’etiquetes(com MPLS/VPN) respecte un més clàssic d’imbricació (túnels).

Per fer aquesta interconnexió simplement hem de realitzar traduccions entre espais de direccionament al NAT (una forma de fer això seria fer que el NAT traduís cada adreça a una diferent que no estigués a l'espai de direccionament utilitzat i que a la xarxa s'enrutissin totes aquestes noves adreces cap al NAT). La limitació d'aquesta esquema es que els hosts d'internet que tinguessin IPs en el rang de les noves adreces utilitzades no serien contactables des de la xarxa (ja que les seves adreces han estat 'segrestades' pel NAT). MPLS també funcionaria en aquest cas però un esquema basat en tunels no.

11. Podem tenir una intranet a n nivells (VPNs imbricades). En el nivell més extern, comafecta n en el càlcul del MTU del primer túnel? Quin és, si existeix, el màxim pràctic pera n si volem assegurar un mínim de 476 bytes per datagrama? Realitza els mateix càlculssi en comptes d’imbricació de túnels fessim NAT a n nivells.

Per a cada nivell hem d'afegir un header IP (ja que el paquet d'un nivell es encapsulat en un paquet IP), de manera que per cada nivell el MTU disminueix en 20 bytes.
MTU del primer Túnel = €MTU - 20*n€
Assumim que el MTU de les xarxes es de 1500 bytes (són xarxes Ethernet v2). Llavors el màxim per a n és: €1500-20*n>=476 -> n<=(476-1500)/(-20)=51.2€, màxim valor de n és 51.
Si utilitzem NAT no estem afegint nous headers, només modifiquem headers que ja existeixen, de forma que el MTU no depen del valor n. Això significa que n pot ser infinit i el MTU seguirà sent de 1500.

12. Un error de configuració en els routers d’una VPN ens porta a una situació límit. En lafigura les fletxes mostren les entrades de default dels routers, les interficies i0 connectena les xarxes físiques, i les tun0 són túnels. Explica que passa amb un datagrama queenvia 10.2.0.20 a 10.5.0.5 i quina serà la seva situació final. Tingues en compte la sevamida, fragmentació, adreces, ICMP, capa d’aplicacions, etc. Quin penses que és l’error deconfiguració? Com afectaria què els routers senars fessin NAT?

El datagrama passarà per tots els routers, serà enroutat per l'entrada de default i enviat al següent router fins a arribar a la xarxa original des d'on ha sigut enviat, moment en el que tornarà a ser enroutat pel primer router. El resultat d'això serà que el datagrama entrarà en un bucle de routers en el que es quedarà fins que el TTL arribi a 0 i s'envii un missatge ICMP a la xarxa d'origen.
No hi ha cap error de configuració, el problema es que la xarxa a la que pertany la IP a la qual s'està enviant el datagrama no existeix en aquesta sistema, de forma que es impossible entregar el datagrama.
NAT canviaria les adreces de source cada vegada que el datagrama passes per un router senar però com que l'adreça de destinació seguiría sent no assolible el problema persistiría.
