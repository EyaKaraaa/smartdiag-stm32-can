# Plan de réalisation

Estimation indicative : 5 à 6 semaines, 8 à 10 heures par semaine, à adapter au niveau en C/STM32 et à la disponibilité du matériel. Le périmètre initial reste LED + température simulée.

| Étape | Travail | Critère de passage |
|---|---|---|
| 1 — Prise en main | Choisir cartes, installer outils, GPIO, ADC, UART, debug | LED et mesure ADC vérifiées |
| 2 — CAN | Loopback puis deux nœuds physiques, filtres, débit | Trames 0x100 reçues sans défaut observé sur essai documenté |
| 3 — ECU | Machine à états, défauts, timeouts, historique RAM | Défauts injectés et arrêt observés |
| 4 — Passerelle et PC | Série, affichage tkinter, commandes, CSV | Chaîne PC → CAN → ECU → PC démontrée |
| 5 — Validation | Tests nominaux et dégradés, corrections | Rapport de tests et limites à jour |
| 6 — Portfolio | Schéma final, captures réelles, vidéo, README | Un tiers peut reproduire la démonstration |

## Organisation logicielle prévue

Créer ces dossiers seulement lorsqu'ils contiennent des fichiers réels :

- `firmware/ecu/` : projet CubeIDE ECU et code applicatif.
- `firmware/diagnostic/` : projet CubeIDE passerelle.
- `common/` : constantes de protocole et encodeurs/décodeurs partagés.
- `pc/` : application Python et dépendances figées.
- `tests/` : tests de protocole, logique de défaut et scripts d'essai.
- `hardware/` : schéma exact, brochage et nomenclature finale.
- `media/` : photographies et vidéo de démonstration.

## Issues GitHub proposées

1. Choisir les cartes et documenter les broches disponibles.
2. Valider ADC, GPIO et UART.
3. Configurer CAN loopback puis réseau physique.
4. Implémenter encodeurs et décodeurs du protocole.
5. Implémenter machine à états et défauts ECU.
6. Développer passerelle CAN/série et ses expirations de commande.
7. Développer interface PC et export CSV.
8. Exécuter le plan de tests et publier les résultats.
9. Ajouter schéma réel, captures et démonstration.

## Définition de « terminé »

Une fonctionnalité est terminée quand le code est présent, compilé, essayé selon un scénario reproductible, documenté avec résultat réel et committé. Un texte de conception seul n'est pas une implémentation.

## Extensions après validation

FreeRTOS, capteur réel avec diagnostic électrique, moteur et encodeur, watchdog matériel, historique persistant, automatisation des tests. Aborder UDS seulement après stabilisation du protocole pédagogique. Une extension ne doit pas retarder la validation de la base.
