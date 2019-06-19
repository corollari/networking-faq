# IPSec

1. En quins protocols es basa IPSec?

L'arquitectura està parcialment inspirada per SSL i TSL (encara que IPsec s'aplica a un layer diferent que aquests).
L'encriptació i autentificació utilitzada en IPsec està basada en protocols críptogràfics símetrics (per l'encriptació) i asímetrics (utilitzats en l'intercanvi Diffie-Hellman i l'autentificació).

2. Suposa que un datagrama IP entra en un túnel IPSec. Escriu les seves capçaleres si s’està utilitzant:
- a) Només autenticació (AH)
- b) Autenticació i xifrat (AH+ESP)
- c) Només xifrat (ESP)

**AH**:
+-------------+--------------+----------+
|Capçalera IP | Capçalera AH | Contingut |
+-------------+--------------+----------+
**AH+ESP**:
+-------------+--------------+---------------+-----------+---------+
|Capçalera IP | Capçalera AH | Capçalera ESP | Contingut | Cua ESP |
+-------------+--------------+---------------+-----------+---------+
**ESP**:
+-------------+---------------+-----------+---------+
|Capçalera IP | Capçalera ESP | Contingut | Cua ESP |
+-------------+---------------+-----------+---------+

3. De quina manera es preserva l’identificador del protocol en el payload del datagrama quan afegim les capçaleres d’AH i d’ESP?

L'identificador de protocol original es posa dins del camp `Next Header`.

4. Per a què serveix el camp d’índex de paràmetres de seguretat a les capçaleres d’IP Sec?

Per a saber quina es la security association que s'ha d'utilitzar per desencriptar i/o autenticar el paquet.

5. En quines situacions utilitzaries SSL i en quines IP Sec? Penses que el servei de WWW del’Autònoma podria oferir-se amb IP Sec? Comenta totes les implicacions que comportaria.

Utilitzaria SSL quan volgués autentificar i encriptar el contingut dels paquets enviats a internet enviats o rebuts per qualsevol altre host d'internet. En canvi, utilitzaria IPSec quan volgués encriptar i autentificar els paquets sencers (no només el contigut) dels paquets enviats entre hosts que controlés jo (perquè hauria de crear les SA necessàries).
El servei de l'Autònoma no es podria oferir un servei web amb IPSec perquè això requeriria que tots els seus usuaris haguessin definit previament les security associations necessàries per poder establir comunicació IPSec.

6. Quines implicacions funcionals té el blocat de datagrames efectuat per un firewall en escenaris on tinguem: a) Multicast, b) VPN/NAT, c) IP Sec.

a) Els firewalls no bloquejen multicast.
b) Els NAT solen aplicar un firewall íntrinsec al ser SNAT (quasi tots els NAT a internet són SNAT) el qual bloqueja totes les conexions que no siguin iniciades per a hosts de dins de la xarxa.
c) Molts firewalls només deixen passar certs protocols amb certs ports concrets (especialment molt firewalls només deixen TCP amb els ports 80 i 443 per tal de només deixar passar comunicacions web) de manera que els paquets IPsec quedarien bloquejats pel firewall.
