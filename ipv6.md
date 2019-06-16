1. Compara les capçaleres de IP versió 4 i 6. Quins camps ja no hi són? Quins s’hanmantingut? Quins han canviat de nom o han canviat la seva semàntica?
2. El checksum ja no hi és en la capçalera d’IPv6. Per què penses que l’han tret? Pot afectaraixò a la integritat dels datagrames (i.e. és IPv4 més segur)?
3. En IPv6 es multiplica per 2 la longitud de la capçalera mínima (sense opcions) respectea IPv4, però el temps de processament és molt menor. Com explicaries aquesta aparentparadoxa?
4. Considera un host que pren una adreça link-local codificant la seva adreça Ethernet de 48bits juntament amb el prefix link-local estàndard. Podrien utilitzar ja aquesta adreça a laInternet global? Per què? Serà única aquesta adreça?
5. IPv6 introdueix el concepte de fluxos. Explica per a que serveixen i si en IPv4 tenien algunequivalent.
6. La capçalera IPv6 pot no portar l’identificador del protocol receptor de dades. Com éspossible doncs l’arribada concreta dels datagrames als protocols finals?
7. Suposa que tenim dos hosts, A (111.1.1.111) i B (55F1:1F55:1221::2112), quevolen comunicar-se. A té una pila dual IPv4/IPv6, i està situat en una xarxa IPv4. B nomésté IPv6 i està en una xarxa IPv6. El router R està connectat a totes dues xarxes i permetl’establiment de túnels. Disenya un esquema que permeti l’intercanvi de datagrames entreA i B. Mostra els detalls d’un datargama circulant d’A a B i d’un altre en sentit contrari.
8. Creus que totes les novetats introduïdes a IPv6 poden afectar el throughput de TCP? Jus-tifica la teva resposta, tenint en compte els diferents tipus d’escenari LAN, WAN, LFN,wireless, ...
9. Si canviem la nostra xarxa de IP v4 a v6, haurem de canviar els commutadors (switchs)si no suporten aquesta nova versió del protocol? Afecta el fet de tenir VLANs aquestadecisió?
10. Serà possible tenir una VPN on circulin datagrames de totes dues versions de IP? En casnegatiu explica-ho. En cas positiu comenta els detalls sobre l’encaminat i tipus de túnels.
11. Podem afegir tantes extensions de capçalera com vulguem, o hi ha un límit? Donat queles extensions formen una llista encadenada, seria possible crear un cicle per a fer malfun-cionar els routers?
12. L’extensió de fragmentació permet fragmentar extensions? És a dir, permet realment frag-mentar datagrames o payloads de datagrames?
