# Arch

1. Instal·la el navegador TOR (Tor Browser: https://www.torproject.org/download/download). Entra a la Deep Web connectant-te a l’adreça: http://hss3uro2hsxfogfq.onion/. Atenció! A la Deep Web et pots trobar amb tot tipus de drogues, armes i serveis il·legals.
- Fes una cerca per a veure si tenen a la venda coca de Sant Joan. Com es garanteix l’anonimat d’aquesta cerca com a client? I com a servidor? Com es podria trencar l’anonimat?
- Quina adreça fas servir? A quin domini pertany?

L'anonimat de client i servidor es garanteix gràcies a l'enrutament que es realitza dins de la xarxa tor (incloent la primera conexió entre client i servidor, que també es realitza a través d'un altre node i els circuits que es conecten a aquest). Com que totes les comunicacions passen a través de diversos nodes de la xarxa abans d'arribar al client o al servidor cap d'aquests dos està en contacte directe amb l'altre, de forma que els es impossible conèixer la identitat de l'altre, obtenint així l'anonimitat propia de Tor.

- L'adreça utilitzada és hss3uro2hsxfogfq.onion (com que l'enrutament de Tor es realitza sobre internet, el servidor també té una adreça IP, però ens es impossible conèixer-la). A Tor no hi ha dominis com a tal, hss3uro2hsxfogfq és part de la clau pública que utilitza el servidor del hidden service (aquest domini/adreça serveix tant per saber quins són els nodes a través dels quals es pot contactar amb el servidor com per autenticar el servidor). 

L'anonimat es podria trencar si un atacant controlés els 3 nodes que s'han triat per enrutar els missatges dins de la xarxa tor, ja que llavors coneixeria tot el camí dels paquets. Igualment no podria conèixer el contingut dels paquets ja que aquests estan encriptats.
Una altra possibilitat que podria eliminar l'anonimat és l'ànalisis dels temps en que els paquets es reben in s'envien per nodes enrutadors dins de Tor, però aquest atac requereix recursos de nivell estatal.

2. Compara les xarxes AdHoc, MANET i DTN. Es podrien intercanviar els diferents protocols d’enrutament per els diferents tipus de xarxes? Inventa un protocol d’enrutament per xarxes DTN.

### Comparació de Xarxes
- AdHoc: Es poden utilitzar els protocols d'enrutament utilitzats en MANET i DTN, encara que aquests resultaràn ineficients en xarxes AdHoc perquè estan disenyats per a xarxes amb canvis molt frequents les xarxes AdHoc solen ser molt més estàtiques que MANET o DTN.
- MANET: No es podrien aplicar els protocols d'enrutament utilitzats en AdHoc, ja que si utilitzem el pro-active les taules d'enrutament canvien tant ràpidament que quan arribin a un node ja estaran desactualitzades i si utilitzem enrutament reactive molt probablement els paquets no arribin al destinatari, ja que la xarxa no és totalment connexa (es possible que hi hagin parts de la xarxa aïllades durant un temps). Es poden aplicar els protocols d'enrutament de DTN sense cap problema.
- DTN: No es poden aplicar el protocols d'enrutament AdHoc per les raons explicades per MANET. Els protocols de MANET es poden aplicar, encara que es possible que hi hagi perdua de paquets perquè MANET no utilitza store-carry&forward.

### Inventa un nou protocol per DTN
- Tots els nodes mantenen un historial de tot el seu recorregut (per on han anat)
- Quan un node A es troba amb un altre node B:
	- Li transmet tots les dades de recorreguts que té (el seu propi + altres recorreguts d'altres nodes que li han sigut transmesos abans)
	- Per cada paquet que té guardat:
		- Si NO es té informació del recorregut del detinatari del paquet, es sumen les distàncies entre tots els punts dels recorreguts d'A i B. Si aquesta suma es superior a una constant A li transmet el paquet a B. Aquest sistema permet determinar com de diferent l'area per la que es mou B es de la d'A i en cas de que ho sigui molt replica el paquet. Aixó es bo perquè aconseguim augmentar molt l'àrea per on es pot distribuir el paquet i trobar el destinari.
		- Si es té informació del recorregut del detinatari del paquet, es calcula la distància mínima entre el recorregut del node B i el recorregut del node destinari. En cas de que aquesta distància sigui inferior a la distància mínima entre el recorregut del node A i el recorregut del destinari, transmet el paquet a B.

El protocol es podria millorar tenint en compte els nodes amb els que es trobarà B i que també podrien enviar el missatge al destinatari, és a dir, fent la distància entre recorreguts transitiva.
