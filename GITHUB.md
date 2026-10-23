# Publication GitHub et valorisation

## Création du dépôt

Nom proposé : `smartdiag-stm32-can`.

Description : `Conception d'un banc de diagnostic STM32 sur bus CAN : supervision, détection de défauts et interface Python prévue.`

Topics possibles : `stm32`, `embedded-c`, `can-bus`, `diagnostics`, `embedded-systems`, `python`.

1. Décompresser l'archive.
2. Créer un repository GitHub ; choisir Public si souhaité pour le portfolio.
3. Add file → Upload files : déposer le contenu de SmartDiag, avec README.md à la racine, plutôt que le ZIP.
4. Premier commit : `docs: define SmartDiag architecture and validation plan`.
5. Vérifier les liens et le rendu Mermaid.
6. Créer les issues proposées dans ROADMAP.md.

Aucun dépôt distant n'a été créé par cette livraison.

## Mises à jour

Conserver le statut réel dans le README. Ajouter les sources et captures au fur et à mesure, avec des commits ciblés : `feat: add CAN telemetry encoding`, `test: cover ECU timeout`, `docs: record bench test results`.

Ne pas ajouter de badge « build passing » sans workflow exécuté. Ajouter les versions d'outils réellement utilisées et le schéma final après validation. Choisir une licence si vous souhaitez accorder des droits de réutilisation.

## CV

À l'état actuel, mention possible : **SmartDiag — Conception d'un banc de diagnostic embarqué CAN (projet personnel en préparation)**. Ne pas le présenter comme un système fonctionnel.

Après réalisation seulement, adapter aux fonctionnalités effectivement validées :

- Développement en C d'un système à deux nœuds STM32 communiquant par CAN.
- Implémentation de la surveillance de mesures, des timeouts et de la gestion des défauts.
- Développement d'une interface Python et validation par injection de défauts.

Ajouter dates, matériel réel, lien GitHub et mesures observées. Les noms d'entreprises ciblées n'ont pas à figurer dans le nom ou les crédits du projet ; aucune affiliation n'est revendiquée.
