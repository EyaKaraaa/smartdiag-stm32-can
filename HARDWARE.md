# Matériel et préparation du câblage

## Nomenclature

| Matériel | Quantité | Critère |
|---|---:|---|
| Carte STM32 avec CAN classique | 2 | Contrôleur intégré, broches CAN accessibles, programmation/debug disponibles |
| Module transceiver CAN 3,3 V | 2 | Exemple : SN65HVD230 ; vérifier le schéma du module |
| Potentiomètre 10 kΩ | 1 | Simulation de mesure analogique |
| Bouton poussoir | 1 | Injection de défaut capteur |
| LED + résistance 330 Ω | 1 | Sortie de démonstration, ou LED intégrée adaptée |
| Résistance 120 Ω | 2 | Seulement si absente des modules |
| Câbles USB, fils, breadboard | Selon montage | Alimentation et connexions |

Aucun prix ni disponibilité locale n'est garanti. Choisir les cartes avant d'acheter les modules et fixer ensuite les GPIO dans le dépôt.

## CAN : contrôleur et transceiver

Le contrôleur CAN prépare et reçoit les trames dans le STM32. Le transceiver convertit TX/RX logiques en signaux différentiels CANH/CANL. Les deux sont nécessaires.

| Carte STM32 | Module transceiver |
|---|---|
| CAN_TX configuré dans CubeMX | Entrée TXD / D |
| CAN_RX configuré dans CubeMX | Sortie RXD / R |
| 3,3 V | VCC du module 3,3 V |
| GND | GND |

Relier CANH à CANH et CANL à CANL entre les deux modules. Ajouter une référence GND commune pour ce banc non isolé. Maintenir les fils courts, utiliser une paire torsadée pour CANH/CANL et minimiser les dérivations.

Deux terminaisons 120 Ω au total, une à chaque extrémité du bus. Vérifier les résistances embarquées pour ne pas en ajouter en double. Hors tension, la mesure entre CANH et CANL doit être proche de 60 Ω lorsque les deux terminaisons sont présentes.

Pour SN65HVD230, configurer la broche de mode RS selon la documentation et le circuit du module. Ne pas laisser un contrôle de mode indéfini.

## Entrées et sortie ECU

- Potentiomètre : extrémités à 3,3 V et GND, curseur vers entrée ADC compatible.
- Ne jamais envoyer 5 V sur une entrée analogique 3,3 V.
- Bouton : entrée GPIO avec pull-up, bouton vers GND ; anti-rebond prévu de 30 ms.
- LED externe : GPIO → résistance 330 Ω → LED → GND ; adapter si LED active bas.

## Liaison série PC

Préférer une carte disposant d'un ST-LINK avec Virtual COM Port. Configurer l'UART correspondante à 115200 bauds, 8N1, sans contrôle de flux. Vérifier le routage des jumpers et broches dans le manuel de la carte. La prise USB d'une carte ne fournit pas automatiquement une liaison série applicative.

## Paramètres à compléter après choix matériel

| Paramètre | Valeur |
|---|---|
| Référence exacte des cartes | À choisir |
| GPIO CAN_TX / CAN_RX et fonction alternative | À définir avec CubeMX et manuel |
| Horloge périphérique CAN | À mesurer/vérifier |
| Prescaler, BS1, BS2, SJW | À calculer pour 500 kbit/s |
| GPIO ADC, bouton et LED | À définir |
| UART VCP et GPIO | À définir |

Les valeurs de bit timing dépendent de l'horloge CAN : ne pas copier celles d'une autre carte sans calcul.

## Première validation

1. Tester LED et ADC sans CAN.
2. Tester le contrôleur en loopback interne : cela ne valide pas les transceivers.
3. Couper l'alimentation avant câblage externe.
4. Vérifier alimentations, masses et terminaisons.
5. Tester en mode normal avec les deux nœuds au même débit ; le second nœud permet l'acquittement des trames.

Référence transceiver : https://www.ti.com/product/SN65HVD230
