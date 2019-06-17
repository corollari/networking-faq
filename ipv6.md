1. Compara les capçaleres de IP versió 4 i 6. Quins camps ja no hi són? Quins s’han mantingut? Quins han canviat de nom o han canviat la seva semàntica?

- TTL passa a ser Hop Limit
- Identification s'elimina perquè ara la fragmentació es realitza en una extensió
- ToS passa a ser Traffic Class i Flow Label (headers utilitzats per classificar tràfic)
- S'elimina Header Length perquè ara la capçelera és de mida fixa
- Protocol s'elimina
- S'elimina el camp de flags i les opcions de IPv4 i es substitueix per un sistema que permet afegir headers opcionals on cadasqun d'aquests determina el següent (mitjançant Next Header).
- S'elimina checksum

2. El checksum ja no hi és en la capçalera d’IPv6. Per què penses que l’han tret? Pot afectar això a la integritat dels datagrames (i.e. és IPv4 més segur)?

**Per què penses que l’han tret?** Per a evitar que els routers hagin de recalcular la checksum de cada paquet al reduir el TTL.

**Pot afectar això a la integritat dels datagrames (i.e. és IPv4 més segur)?** No, perquè les comprovacions de la integritat dels datagrames es realitzen en altres nivells, com per exemple en el transport (TCP o UDP), de manera que les comprovacions d'integritat es realitzen igualment. Mentres a IPv4 era possible no utilitzar checksum en UDP en IPv6 és obligatori per aquesta raó.

3. En IPv6 es multiplica per 2 la longitud de la capçalera mínima (sense opcions) respecte a IPv4, però el temps de processament és molt menor. Com explicaries aquesta aparent paradoxa?

Passa això per 2 raons principals:
- Els routers no poden fragmentar de manera que tot el processament utilitzat en fragmentació s'elimina
- No hi ha checksum a nivell de IP de manera que s'elimina tot el processament associat a computar noves checksums per cada paquet (requerides per la modificació del TTL)

4. Considera un host que pren una adreça link-local codificant la seva adreça Ethernet de 48bits juntament amb el prefix link-local estàndard. Podrien utilitzar ja aquesta adreça a laInternet global? Per què? Serà única aquesta adreça?

No, l'adreça obtinguda a través de la MAC no serveix per l'internet global perquè, mentres l'adreça seria probablement única a tot internet (es possible que un altre node agafi la mateixa adreça però la probabilitat de que això passi es molt baixa) seria impossible a internet enrutar correctament els paquets dirigits a aquesta adreça, ja que fer això requeriría que es tinguessi en compte aquesta adreça a les taules d'enrutament de tots els routers del món.

5. IPv6 introdueix el concepte de fluxos. Explica per a que serveixen i si en IPv4 tenien algun equivalent.

Són simplement una altra forma de classificar el trafic, permetent que tràfic que requereix una latencia menor o pertany a aplicacions més importants tingui preferència sobre altres tipus de tràfic. L'equivalent a IPv4 és el camp ToS (Type of Service).

6. La capçalera IPv6 pot no portar l’identificador del protocol receptor de dades. Com és possible doncs l’arribada concreta dels datagrames als protocols finals?

És possible perquè l'identificador del protocol està contingut al camp Next Header de l'última extensió IPV6. Aixó significa que per obtenir l'identificador de protocol només s'ha de processar els headers d'extensió fins a arribar a l'últim.

7. Suposa que tenim dos hosts, A (111.1.1.111) i B (55F1:1F55:1221::2112), que volen comunicar-se. A té una pila dual IPv4/IPv6, i està situat en una xarxa IPv4. B només té IPv6 i està en una xarxa IPv6. El router R està connectat a totes dues xarxes i permet l’establiment de túnels. Disenya un esquema que permeti l’intercanvi de datagrames entreA i B. Mostra els detalls d’un datargama circulant d’A a B i d’un altre en sentit contrari.

Es pot solucionar aquest problema establint un tunel IPv6-over-IPv4 a la xarxa de A entre A i R, el qual actuarà de la següent forma:
A envia a B:
1. A crea un paquet IPv6 de A a B i l'empaqueta dins d'un paquet IPv4 de A a R, el qual envia.
2. R rep el paquet i el desempaqueta, obtenint el paquet IPv6 que va de A a B, el qual envia a B
3. B rep el paquet IPv6 de A a B

Paquet de A a B a la xarxa de A:
+--------------------+--------------------+---------+
| Header IPv4 (A->R) | Header IPv6 (A->B) | Payload |
+--------------------+--------------------+---------+
Paquet de A a B a la xarxa de B:
+--------------------+---------+
| Header IPv6 (A->B) | Payload |
+--------------------+---------+

B envia a A:
1. B crea un paquet IPv6 de B a A i l'envia
2. R rep el paquet i l'empaqueta dins d'un paquet IPv4 de R a A. Envia el paquet IPv4
3. A rep el paquet IPv4 de R a A, el desempaqueta i obté el paquet IPv6 de B a A

Paquet de B a A a la xarxa de A:
+--------------------+--------------------+---------+
| Header IPv4 (R->A) | Header IPv6 (B->A) | Payload |
+--------------------+--------------------+---------+
Paquet de A a B a la xarxa de B:
+--------------------+---------+
| Header IPv6 (B->A) | Payload |
+--------------------+---------+

8. Creus que totes les novetats introduïdes a IPv6 poden afectar el throughput de TCP? Justifica la teva resposta, tenint en compte els diferents tipus d’escenari LAN, WAN, LFN,wireless, ...

Si, el throughtput de TCP es pot millorar ja que ara els routers que enrutin els missatges no hauran d'utilitzar tant poder de processament, evitant així que arribin a la seva capacitat de processament i tinguin que dropejar paquets, la qual cosa empitjora molt el throughput de TCP (a no ser que tingui una finestra molt gran).

9. Si canviem la nostra xarxa de IP v4 a v6, haurem de canviar els commutadors (switchs) si no suporten aquesta nova versió del protocol? Afecta el fet de tenir VLANs aquesta decisió?

Depen:
- Si són switches que operen al layer 2 i 1 no fa faltan canviar-los.
- Si els switches arriben a operar al layer 3 si que fa falta canviar-los.

Si tenim una LAN els switches necessariament han d'operar a layer 3 de manera que s'hauran de canviar.

10. Serà possible tenir una VPN on circulin datagrames de totes dues versions de IP? En cas negatiu explica-ho. En cas positiu comenta els detalls sobre l’encaminat i tipus de túnels.

Sí que és possible, un simple tunel IP-over-IP podria transportar datagrames IPv6 i IPv4 ja que:
- A internet el tipus de datagrames que hi hagin dins del tunel són irrellevants, ja que els routers d'internet només veuen el header IP que indica els dos endpoints del túnel.
- Els endpoints del túnel poden empaquetar dades arbitraries dins del datagrama que s'envia pel túnel, així que no hi ha cap problema en empaquetar IPv4 o IPv6.

11. Podem afegir tantes extensions de capçalera com vulguem, o hi ha un límit? Donat que les extensions formen una llista encadenada, seria possible crear un cicle per a fer malfuncionar els routers?

Hi ha un RFC que diu que totes les extensions de capçalera han d'anar dins del primer fragment d'un paquet (abans no era així però a partir d'aquest RFC si, el qual es va crear per solucionar problemes de seguretat), i el tamany dels fragments d'un paquet està limitat de manera que el nombre de extensions que poden haver-hi en un paquet (ja que no hi ha cap extensió que tinguina mida nula).
No es possible crear un cicle ja que Next Header només descriu quin és el següent header, no apunta a cap lloc. Això significa que es impossible que el processament d'aquests headers sempre avançarà i serà O(n) on n és el nombre d'extensions.

12. L’extensió de fragmentació permet fragmentar extensions? És a dir, permet realment fragmentar datagrames o payloads de datagrames?

Només permet fragmentar payloads de datagrames. En les primeres especificacions de IPv6 es podien fragmentar datagrames (incloent extensions) però es va deprecar aquest comportament per problemes de seguretat.
