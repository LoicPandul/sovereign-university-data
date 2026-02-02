---
name: Maîtriser les fondamentaux du minage de Bitcoin
goal: Comprendre le rôle du temps, des incitations et de la gouvernance dans le minage de Bitcoin.
objectives:
  - Clarifier les règles temporelles du consensus et leurs contraintes techniques.
  - Analyser les incitations économiques du minage et le pouvoir des mineurs.
  - Déconstruire les critiques énergétiques et situer leurs enjeux.
  - Comprendre le fonctionnement des pools et leurs compromis.
---










# Introduction

## Aperçu du cours






## Rappels fondamentaux sur le fonctionnement du minage

Avant de commencer ce cours MIN 201, je vous propose une synthèse rapide des concepts essentiels abordés dans le cours MIN 101. Cela vous permet de vous assurer que les bases nécessaires à la compréhension des mécanismes avancés du minage sont bien acquises avant d'aller plus loin.

### Le parcours d'une transaction

Sur Bitcoin, une transaction est une structure de données qui transfère la propriété de bitcoins d'un utilisateur à un autre. Elle consomme des UTXOs (*Unspent Transaction Outputs*) existants en les référençant comme inputs, puis crée de nouveaux UTXOs en outputs. Pour autoriser la dépense d'un UTXO, l'utilisateur doit fournir une preuve cryptographique, généralement sous la forme d'une signature numérique produite à partir de sa clé privée.

Une fois construite et signée, la transaction est diffusée sur le réseau pair-à-pair de Bitcoin. Chaque nœud du réseau qui la reçoit vérifie sa validité (signatures, existence des UTXOs référencés, cohérence des montants, respect des règles...) avant de la relayer à ses pairs qui feront de même. La transaction rejoint alors la mempool des nœuds, un espace de stockage temporaire où elle attend d'être confirmée dans un bloc.

001

### La blockchain

La blockchain est un registre public qui contient l'historique ordonné des transactions. Plutôt que d'enregistrer chaque transaction individuellement, Bitcoin les regroupe dans des blocs. Chaque bloc agit comme une page d'historique et inclut l'empreinte cryptographique (le hash) du bloc précédent, ce qui crée ainsi une chaîne de dépendances. Modifier un bloc ancien impliquerait de recalculer tous les blocs suivants, ce qui rend la falsification du passé extrêmement coûteuse.

002

### La construction d'un bloc candidat

Les mineurs construisent eux-mêmes leur bloc candidat avant de tenter de le miner. Cette construction consiste à sélectionner des transactions dans leur mempool (en privilégiant celles offrant les meilleurs taux de frais), à les organiser selon les règles du protocole, et à produire l'entête du bloc.

Cette entête, d'une taille fixe de 80 octets, contient six champs :
- la version,
- l'empreinte du bloc précédent,
- la racine de Merkle (qui engage l'ensemble des transactions via un arbre de hachage),
- l'horodatage,
- la cible de difficulté encodée (`nBits`),
- et le nonce.

003

### Le mécanisme de la preuve de travail

Le minage consiste à trouver une entête de bloc dont le hash (calculé avec `SHA256d`, c'est-à-dire SHA256 appliqué deux fois) est numériquement inférieur ou égal à une valeur cible. Le mineur procède par tâtonnement : il calcule le hash de l'entête, vérifie s'il satisfait la condition, puis modifie le nonce et recommence jusqu'à trouver une empreinte valide. Ce processus est probabiliste et chaque essai constitue un tirage aléatoire indépendant.

004

L'intérêt de ce mécanisme réside dans son asymétrie : produire une preuve de travail nécessite un grand nombre de calculs et donc une dépense en énergie, tandis que vérifier cette preuve ne demande qu'un seul hachage. La preuve de travail permet ainsi de sceller l'historique économique et de définir la règle de consensus selon le principe "un CPU = une voix", qui pondère les votes par la puissance de calcul réellement dépensée (nous détaillerons justement cela dans la suite du cours).

### L'ajustement de la difficulté

Bitcoin vise un rythme moyen d'un bloc toutes les 10 minutes. Pour maintenir cet intervalle malgré les variations du hashrate global, le protocole ajuste la cible de difficulté tous les 2016 blocs (environ deux semaines). Si les blocs ont été trouvés trop rapidement, la cible est abaissée (difficulté plus grande) ; s'ils ont été trouvés trop lentement, elle est relevée (difficulté plus petite). 

La formule appliquée est :

```txt
Tn = To * (Ta / 1 209 600)
```

Où `Tn` est la nouvelle cible, `To` l’ancienne, `Ta` le temps observé pour les 2016 derniers blocs, et `1 209 600` secondes correspond à deux semaines.

Cet ajustement est borné pour éviter des variations trop brutales : le temps réel pris en compte ne peut varier que d'un facteur 4 par rapport au temps cible.

### La récompense de bloc

Le mineur qui produit un bloc valide reçoit une récompense composée de deux éléments :
- La subvention de bloc, qui correspond à des bitcoins nouvellement créés, dont le montant est défini par le protocole et divisé par deux tous les 210 000 blocs lors d'un événement appelé halving. Cette subvention était de 50 BTC au lancement de Bitcoin, puis 25 BTC après le premier halving, et ainsi de suite jusqu'à atteindre zéro aux alentours de l'an 2140 ;

005

- Les frais de transaction, qui correspondent à la différence entre le total des inputs et le total des outputs de chaque transaction incluse dans le bloc. Ce sont les frais payés par l'émetteur de la transaction.

006

### La transaction coinbase

Cette récompense de bloc est perçue par le mineur gagnant via une transaction particulière appelée "coinbase", toujours placée en première position dans le bloc. Elle possède un input fictif (référençant un UTXO inexistant avec un TXID composé uniquement de zéros) et crée des outputs au bénéfice du mineur.

007

Le champ `scriptSig` de cet input fictif est libre et sert notamment à inscrire la hauteur du bloc (obligation depuis le BIP-34), un extra-nonce pour étendre l'espace de recherche, et diverses informations techniques ou messages arbitraires. Au niveau des outputs, l’un d’eux permet au mineur de percevoir sa récompense, et l’on trouve également un `OP_RETURN` qui, depuis SegWit, sert à inclure la racine de Merkle des témoins de transaction.

008

Les UTXOs créés par la coinbase sont soumis à une période de maturité de 100 blocs avant de pouvoir être dépensés.

009

### L'évolution du matériel de minage

Le minage a connu une spécialisation progressive du matériel. Au lancement de Bitcoin, le minage s'effectuait avec des processeurs (CPU) classiques. L'utilisation de cartes graphiques (GPU) a ensuite permis d'effectuer davantage de calculs en parallèle. Après une brève phase intermédiaire utilisant des FPGA, les ASIC (*Application-Specific Integrated Circuits*) se sont imposés : ces puces conçues exclusivement pour le calcul de `SHA256d` offrent une meilleure efficacité énergétique. Cette évolution a transformé le minage en une activité industrielle, avec des fermes regroupant des milliers de machines dans des infrastructures dédiées.

010

*L’un des sept bâtiments dédiés au minage de Bitcoin sur le site de Rockdale de Riot Platforms, à proximité d’Austin, au Texas. Celui-ci est spécifiquement dédié au minage par immersion.*

Maintenant que nous avons revu ensemble les bases du minage de Bitcoin, nous pouvons passer à la suite. Dans la partie suivante, nous allons d’abord clarifier le rôle de la preuve de travail sur Bitcoin. En effet, si nous avons déjà décrit son fonctionnement dans MIN 101, il reste à comprendre pourquoi tout cela est nécessaire. Nous verrons ensuite comment le temps est géré sur Bitcoin : nous avons évoqué l’horodatage et l’ajustement de la difficulté en fonction du temps, mais comment ces notions temporelles sont-elles coordonnées à l’échelle du réseau, et selon quelles règles ? Découvrons cela ensemble !

# Sécurité cumulative et temporalité


## L'utilité et le rôle de la preuve de travail sur Bitcoin

Utilité concrète de la preuve de travail dans le système (sécurité, attaque sybil...).

## Le principe d'accumulation de sécurité

Accumulation de sécurité économique globale et cumulative (analogie gravité).

## La gestion du temps sur Bitcoin

Horodatage, validité des timestamps, MTP et notions réseau liées au temps (NAT).




# Économie et gouvernance du consensus
## Les principes d'économie appliqués au minage

Incitations, coûts, équilibres, et débunk des sophismes récurrents sur le minage.

## Le pouvoir des mineurs sur Bitcoin

Rapports de force et interactions mineurs / nœuds / utilisateurs / commerçants / devs ; mise en perspective historique (Blocksize War) et théorique (crypto économie).

## L'activation des soft forks

BIP9/BIP8, UASF, Speedy Trial, clarification de qui décide lors des mises à niveau (et en quoi cela nuance/encadre le pouvoir décrit au chapitre précédent).





# Minage et débat environnemental

## Le minage de Bitcoin face aux critiques

Débunk des critiques : consommation électrique, comparaison aux usages concurrents. Précisions techniques nécessaires sur Bitcoin pour éviter les contresens.

## Les externalités et effets bénéfiques du minage

Bitcoin comme ultime utilisation des énergies fatales : pourquoi le minage est un acheteur d’énergie particulier (interruptible, localisable, monétisation du surplus/du perdu) + valorisation et combustion du gaz d'extraction. Développer les bénéfices écologiques liés à cette flexibilité, la valorisation de la chaleur produite (chauffage, usages industriels), idée de monnaie saine et ses bienfaits écologiques (incitations temporelles, allocation du capital, horizon long terme) autres ?


# Pools de minage

## Les principes et mécanismes des pools de minage

Rôle des pools

## La mesure du travail dans les pools

shares

## Les mécanismes de rémunération

schémas de distribution PPS, FPPS, PPLNS...

## Les protocoles réseau de pool

protocoles réseau (Stratum, Stratum V2, négociation de jobs, implications en matière de centralisation et de censure...).












