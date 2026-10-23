# Contrat de communication proposé — version 1

Ce protocole est une spécification à implémenter, pas une API déjà disponible. CAN classique, trames de données standard 11 bits, débit cible 500 kbit/s. Aucun identifiant étendu ni remote frame accepté. Les octets réservés valent zéro. Les entiers multioctets sont little-endian.

## Trames

| ID | Sens | DLC | Période / déclenchement | Rôle |
|---|---|---:|---|---|
| 0x100 | ECU → diagnostic | 8 | 100 ms | Mesure, sortie et défauts |
| 0x101 | ECU → diagnostic | 4 | 500 ms | Présence ECU |
| 0x200 | Diagnostic → ECU | 4 | Commande ; ON renouvelée toutes les 250 ms | Sortie / historique |
| 0x201 | Diagnostic → ECU | 4 | 250 ms | Présence diagnostic |
| 0x300 | ECU → diagnostic | 4 | Après commande | Acquittement applicatif |

## 0x100 — télémétrie

| Octet | Signification |
|---:|---|
| 0 | Version = 1 |
| 1 | Compteur modulo 256 |
| 2–3 | Température simulée en dixièmes de °C, uint16 ; 0xFFFF = invalide |
| 4 | État : 0 INIT, 1 READY, 2 ACTIVE, 3 FAULT |
| 5 | Sortie : 0 OFF, 1 ON |
| 6 | Masque défauts actifs |
| 7 | Masque historique des défauts |

Bits défaut : bit0 mesure invalide injectée ; bit1 température excessive ; bit2 heartbeat diagnostic expiré ; bit3 erreur CAN locale détectée. Bits4–7 réservés. Les défauts détectés par le diagnostic, comme ECU absent, sont locaux au diagnostic et ne sont pas attribués à tort à l'ECU silencieux.

Exemple : `01 2A 7D 01 01 00 00 00` correspond à version 1, séquence 42, 38,1 °C, READY et sortie OFF.

## 0x101 et 0x201 — présence

DLC 4 : octet0 version 1, octet1 compteur modulo 256, octets2–3 réservés zéro. Une réception doit respecter ID, DLC, version et champs réservés pour renouveler le timeout correspondant.

L'interface marque les mesures périmées si la télémétrie 0x100 manque depuis plus de 1 000 ms, même si le heartbeat ECU est encore reçu. La présence ECU et la fraîcheur des mesures sont deux indicateurs distincts.

## 0x200 — commande

DLC 4 : octet0 version 1, octet1 numéro de requête modulo 256, octet2 opération, octet3 réservé zéro.

| Opération | Signification |
|---:|---|
| 0 | OFF et autorisation de récupération si aucun défaut actif |
| 1 | ON, autorisé seulement en READY ou ACTIVE sans défaut |
| 2 | Effacer historique, sans modifier les défauts actifs |

Une ON valide reçue en ACTIVE renouvelle le timeout de sortie de 1 000 ms. Ni les heartbeats, ni l'effacement historique ne le renouvellent. Le diagnostic retransmet ON toutes les 250 ms uniquement tant que la demande PC est renouvelée : la commande série ON expire côté passerelle après 750 ms sans nouveau ON. À expiration, envoyer OFF et cesser les ON périodiques. Ainsi une application PC bloquée ne laisse pas la sortie activée indéfiniment.

Pas de reprise ON automatique après redémarrage, reconnexion ou défaut. Demander OFF, puis une nouvelle ON.

## 0x300 — acquittement

DLC 4 : octet0 version 1, octet1 numéro de requête reçu, octet2 résultat, octet3 état ECU.

Résultats : 0 accepté, 1 refusé pour défaut/état, 2 opération inconnue. Une trame de longueur/version invalide est ignorée et n'est pas acquittée. Une commande valide mais refusée ne renouvelle pas le timeout ON.

Ne pas confondre cet acquittement applicatif avec l'ACK physique CAN. La réception d'un ACK applicatif ne démontre pas qu'un actionneur réel s'est déplacé.

## Série PC ↔ diagnostic

115200 bauds, 8N1, UTF-8 limité ici à ASCII, une ligne terminée par LF. Le parseur peut supprimer CR avant LF, taille maximale 128 octets ; à dépassement, rejeter jusqu'au LF suivant. Accepter uniquement les commandes complètes suivantes :

```text
ON
OFF
CLEAR
```

L'application renouvelle ON toutes les 250 ms tant que l'utilisateur maintient explicitement la demande. Elle envoie OFF au relâchement et avant déconnexion.

La passerelle fournit des lignes JSON, par exemple :

```json
{"type":"telemetry","seq":42,"temp_c":38.1,"state":"READY","output":0,"active_faults":0,"stored_faults":0}
{"type":"link","ecu_present":false,"telemetry_fresh":false}
{"type":"ack","request_id":7,"result":"accepted","state":"READY"}
```

Température invalide : `temp_c` vaut `null`. Les acquittements sont corrélés par request_id et peuvent expirer ; l'interface n'affiche ON confirmé qu'après télémétrie correspondante. Enregistrer l'horodatage de réception PC ; il ne s'agit pas d'une horloge ECU synchronisée.

## Validation et limites

Tester bornes, endianess, valeurs réservées, trames inconnues et compteurs modulo. Un saut de compteur peut signaler une perte ; ce n'est pas une preuve suffisante de sa cause. Les compteurs ne sont ni une authentification ni une protection anti-rejeu. Bus réservé au banc expérimental.
