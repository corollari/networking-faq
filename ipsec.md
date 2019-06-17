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
+--
|Capçalera IP | Capçalera ESP | Capçalera AH | Contingut | Cua ESP |
+--
**ESP**:


3. De quina manera es preserva l’identificador del protocol en el payload del datagrama quan afegim les capçaleres d’AH i d’ESP?

L'identificador de protocol original es posa dins del camp `Next Header`.

4. Per a què serveix el camp d’índex de paràmetres de seguretat a les capçaleres d’IP Sec?

Per a saber quina es la security association que s'ha d'utilitzar per desencriptar i/o autenticar el paquet.

5. En quines situacions utilitzaries SSL i en quines IP Sec? Penses que el servei de WWW del’Autònoma podria oferir-se amb IP Sec? Comenta totes les implicacions que comportaria.

Utilitzaria SSL quan 
El servei de l'Autònoma no es podria oferir amb IPSec perquè això requeriria que tots els seus usuaris haguessin definit previament les security associations necessàries per poder establir comunicació IPSec.

6. Quines implicacions funcionals té el blocat de datagrames efectuat per un firewall en escenaris on tinguem: a) Multicast, b) VPN/NAT, c) IP Sec.


