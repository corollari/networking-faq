1. Per tal de reduir la congestió, quines diferències veus, a tots els nivells, entre l’aplicació de l’algorisme de van Jacobson i la utilització d’una política de gestió de cues RED? De quina manera podem relacionar la política SFQ amb l’algorisme de Karn (en el sentit de si són totalment independents, o l’un millora/altera l’altre)?

L'algoritme de van Jacobson permet als hosts respondre més ràpidament a canvis en la congestió. Aquesta adaptació ràpida millora els resultats obtinguts amb RED perquè fa que quan la cua es començi a emplenar els hosts i els paquets s'eliminin els hosts reaccionin depressa i enviin menys paquets i disminueixi ràpidament la congestió.
En cas de que no s'apliqués l'algoritme de van Jacobson, quan la cua RED començes a eliminar paquets els hosts no reaccionarien i seguirien enviant, portant a un augment de la congestió de la xarxa, que el router elimininés molts paquets i que tots els hosts entressin en Slow Start. Aixó implica un sincronitzament de Slow Start, que es justament el que volem evitar utilitzant RED.

Karn i SFQ són independents.

2. Cóm argumentarieu que tancar automàticament connexions inactives és bo? Com implementaríeu aquest mecanisme de keep alive sense afegir nous bits de control a les capçaleres TCP? Com ho fa la recomanació del RFC? Han d’estar totes dues parts d’acord per tal d’utilitzar el keep alive del RFC? Justifica les respostes.

Tancar connexions inactives es bo perquè permet estalviar memòria en el servidor, ja que el servidor alloca certa memòria a cada connexió oberta.
Implementaría keep alives enviant un missatge TCP que només contingués un byte i després esperaría el ACK. Així no es necessàri cap canvi en el protocol.
Sí, el keepalive del RFC requereix que la part a la que se li envia el keep alive tingui keep alive activat (i segons el RFC per defecte keep alive ha d'estar desactivat).


3. Si enviem dades de byte en byte en TCP, amb un byte per cada segment de dades, quin tant per cent de xarxa estaríem utilitzant pel cap alt? Com variarà en aquest cas el utilitzar una finestra d’1 byte a utilitzar una de 64 KBytes? I amb la mida màxima que permet l’opció d’escalar la finestra? I si la tasa de pèrdua és del 5%?

Si només enviem 1 byte per segment estaríem utilitzant €1/(40+1)*100€ de la xarxa (on 40 és la mida dels headers IP+TCP). Per contra, si la finestra és de 64K, enviariem segments a min(64K, MSS), els quals, considerant un MSS de 536 (el per defecte segons l'estandard), utilitzent un €536/(536+40)*100€.
Si augmentem la mida màxima de la finestra utilitzant l'escalat de finestres l'utilització de la xarxa no canvia, ja que la maximum segment size ens limita més que la finestra. L'utilització és de €536/(536+40)*100€.
La tasa de pérdua de paquets no modifica significativament el percentatge d'ús de la xarxa, ja que els paquets perduts són simplement retransmessos.

4. Tenim tres routers en seqüència (tots els datagrames que surten d’un entren en el següent),un funcionant amb PFIFO, l’altre amb RED, i l’altre amb TBF. En quin ordre els posaríeu?I si fossin RED, SFQ i TBF? Utilitza els teus coneixements i la informació que puguis trobar per a donar una justificació dels ordres proposats.

En aquest cas ens interessa evitar el problema de la sincronització dels hosts entrant en estat de congestió, el qual portaria a que tots els hosts fessin Slow Start a la vegada i es desaprofités molta bandwidth. Per tal d'evitar aixó ficarem primer els routers amb polítiques més permissives i després els routers amb polítiques més estrictes, donat lloc al següent ordre:
1. TBF
2. RED
3. PFIFO
Aquest ordre ens permet dropejar paquets de forma progressiva per evitar sincronitzacions.
Raonament: Si no possesim PFIFO últim es podrien acumular paquets i causar eliminacions d'aquests massives, causant sincronitzacions de Slow Start. 
Triant entre TBF i RED pel primer lloc agafem TBF primer perquè si fos al revés RED bloquejaria rafàgues espòradiques dels hosts (la probabilitat augmentaria i els paquets es dropejarien).

En el cas de RED, SFQ i TBF:
1. TBF
2. RED
3. SFQ

5. Imagina que en el router de l’Autònoma volem utilitzar cues diferents per a cada connexió per a que el tràfic d’excés sigui descartat de manera justa. Veus algun problema en buscarla justicia total? Com ho fa SFQ? En cas afirmatiu, com ho podries solucionar? Troba o dissenya una política també basada en la justífica que sigui diferent de SFQ, i explica el seu funcionament.

Un possible problema es que un host podria aconseguir transmetre moltes més dades que els altres a base d'obrir moltes conexions diferents. A més depenent de com s'implementés aquesta cua podria no funcionar bé amb ràfegues.
SFQ crea una cua per cada conexió i utilitza round robin sobre aquestes cues per a decidir quin paquet transmetre.
Una solució podria ser crear una cua com la SFQ amb els següents canvis:
- En comptes de classificar els paquets per conexió es classificarien els paquets pel host del que provinguessin.
- S'implementaría una solució similiar a la del PBF (uns tokens que es poguessin acumular i gastar en ràfagues) per tal de deixar passar ràfagues.
El mecanisme per decidir quin paquet enviar seria round robin amb la modificació de que si un host té molts tokens acumulats s'enviarien k paquets seus en comptes de només 1 quan li arribés el torn per round robin, on k es determinaria en funció dels tokens acumulats.

6. Assumint que tant la capacitat de l’enllaç com la finestra de recepció són infinits, quants RTTs calen per a enviar els primers 20 segments de TCP si s’utilitza slow start amb un valor inicial de 1 MSS? i si el valor inicial és de 4 MSS? Podries generalitzar per cas dels k primers segments i valor inicial i MSS? Descriu alguna proposta d’eixamplar la finestrainicial i comenta-la.

Com que capacitat i recepció són infinites només estem limitats per el mecanisme de Slow Start, el qual farà que cada vegada que reben confirmacions enviem el doble de paquets enviats abans:
1 MSS
2 MSS
4 MSS
8 MSS -> 15 MSS enviats
16 MSS -> 31 MSS enviats, ja arribem als 20 MSS
Observem que el temps total és de €4*RTT€. En cas de començar amb €4*MSS€, tardaríem €2*RTT€.En el cas general, si comencem amb €i€ MSS i volem enviar €k€, el temps serà superior o igual a €log2(k/i)€.

7. Quin és el problema que tindria RED si apliquès la funció de probabilitat directament sobrela mida de la cua i que afectaria als darrers datagrames de les ràfagues? De quina maneraho soluciona? Quina relació té aquesta solució amb el càlcul de l’estimació del RTT pertal de calcular el timeout de retransmissió en TCP? Tindria sentit aplicar van Jacobson enaquest context? Quin serien els efectes?

El problema seria que al tenir una ràfega la cua s'emplenaria molt despressa i al últims paquets de les ràfegues s'els hi aplicaria una probabilitat de dropeig molt alta. Es soluciona aplicant la probabilitat basada en una mitjana de la mida de la cua, de manera que un pic puntual (causat per una ràfaga per exemple) no alteri la probabilitat molt.


8. Quin és el throughput màxim de TCP si el RTT promig és de 15ms, les capçaleres de laxarxa física són sempre 66 bytes, i el MTU és 128 KBytes? Fes ara el promig en funció delMTU i del RTT. En aquesta xarxa, a partir de quin RTT quedaria justificada la utilitzacióde la opció d’escalat de la finestra?

Assumim que la finestra té de mida €N€ MTU. Llavors el throughput màxim serà de €(N*MTU)/(RTT)=N*((128*1000)/(15/1000))€ bytes/s.
No hi ha suficients dades com per decidir a partir de quin RTT seria millor escalar la finestra. En cas de que la transmissió fos perfecta la finestra s'hauria d'escalar a l'infinit.

9. Seria possible tenir TCP per sobre d’Ethernet directament, sense utilitzar IP? I tenir TCPsobre un altre TCP sobre IP (els MSS haurien de ser diferents, és clar)? Raona els motius iles limitacions que trobis en aquestes dues situacions considerant els diferents mecanismesque hem vist al llarg del tema.



10. Si estem connectats a Internet utilitzant wireless, i estem descarregant un fitxer gran ambun protocol sobre TCP, explica quin efecte sobre el throughput tindrà embolicar l’antenadurant exactament un minut i mig amb un troç de paper de plata connectat a terra. Tinguesen compte els diferents mecanismes que utilitza TCP. Podries quantificar d’alguna manera(encara que sigui aproximadament) la pèrdua de throughput ocasionada per aquest event?Com es podria minimitzar?

Es perdria tota la informació que s'hagués pogut enviar durant el minut i mig a la velocitat, i, a més la pérdua de paquets activaria el mecanisme de congestió de TCP implementat en el sistema, que al fallar també acabaria portant la finestra a la mida mínima. Finalment el temps de timeout dels paquets augmentaria de forma massiva.
Tot aixó portaria a que després de retirar el paper de plata de l'antena 

11. Quines són les diferències i semblances entre el backoff del timeout de l’algorisme deKarn, el timeout del mecanisme de KeepAlive, i el timer de persistència amb backoff deles emissions en finestra zero del protocol TCP? Per a cada cas, detalla també quin ésel problema que volen solucionar, com funcionen, i quin impacte tenen en el throughputglobal de TCP.


