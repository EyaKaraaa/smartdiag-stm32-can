# Plan de validation

**Aucun test firmware ou matériel n'a été exécuté à ce stade.** La validation de cette livraison porte uniquement sur la cohérence et les liens des documents.

Pour chaque essai, relever référence carte, révision câblage, commit, version des outils, alimentation, procédure, résultat réel et preuve. Les délais sont des objectifs à mesurer et non des résultats acquis.

| ID | Scénario | Résultat attendu | État |
|---|---|---|---|
| T01 | Redémarrage de l'ECU | LED OFF, pas de reprise ON | Non exécuté |
| T02 | Acquisition et émission pendant 60 s | Mesures cohérentes, période proche de 100 ms avec jitter mesuré | Non exécuté |
| T03 | ON renouvelée, système sain | LED ON et télémétrie ACTIVE | Non exécuté |
| T04 | ON avec défaut actif | Refus, LED OFF | Non exécuté |
| T05 | Cesser les ON CAN, heartbeat présent | Sortie OFF après timeout de 1 000 ms, latence mesurée | Non exécuté |
| T06 | Monter température simulée à 70 °C puis redescendre | Défaut à 70, disparition sous 65, pas de redémarrage spontané | Non exécuté |
| T07 | Maintenir bouton défaut | Mesure invalide et sortie OFF | Non exécuté |
| T08 | Éteindre ECU | Diagnostic signale absence et mesure périmée après timeout | Non exécuté |
| T09 | Cesser heartbeat diagnostic, ECU alimenté | Défaut liaison et sortie OFF après timeout | Non exécuté |
| T10 | CLEAR pendant défaut actif | Défaut actif conservé ; historique contient au moins les défauts actifs | Non exécuté |
| T11 | Mauvais DLC, version, ID, réservés ou opération | Rejet conforme ; aucun maintien ON induit | Non exécuté |
| T12 | Exporter les événements | CSV lisible, horodatage PC et valeurs invalides explicites | Non exécuté |
| T13 | Fermer/bloquer le programme PC pendant ON | Passerelle expire ON après 750 ms, envoie OFF ; timeout ECU en secours | Non exécuté |
| T14 | Défaut disparu, ON sans OFF préalable | Reste arrêté ; OFF puis ON permet reprise | Non exécuté |
| T15 | Couper seulement la télémétrie, heartbeat ECU présent | ECU présent mais données périmées | Non exécuté |
| T16 | Requêtes série trop longues ou inconnues | Rejet et resynchronisation au LF suivant | Non exécuté |
| T17 | Redémarrer l'ECU après défaut mémorisé | Historique RAM perdu, limitation documentée | Non exécuté |
| T18 | Déclencher erreur CAN/bus-off selon procédure contrôlée | Sortie OFF, erreur signalée si transport disponible, reset manuel | Non exécuté |

## Tests logiciels à écrire

- Encodage/décodage et vecteur d'exemple 38,1 °C.
- Température invalide 0xFFFF.
- Hystérésis 70/65 °C et conditions de récupération.
- Timeouts indépendants : sortie, heartbeat, fraîcheur.
- Calculs de temps robustes au débordement de l'horloge.
- Parseur série fragmenté et multiples lignes dans un même bloc reçu.

## Démonstration finale proposée — 2 à 3 minutes

Présenter le banc et l'architecture ; faire varier le potentiomètre ; commander la LED ; injecter le défaut capteur ; provoquer le seuil thermique ; interrompre la présence ECU ; montrer le journal et les résultats mesurés. Ne pas annoncer de performances sans preuve.
