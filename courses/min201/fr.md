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

# Utilité du minage et temporalité

## L'utilité et le rôle de la preuve de travail sur Bitcoin

Comprendre *comment* fonctionne la preuve de travail est relativement simple. C'est ce que nous avons étudié dans le cours MIN 101 et revu dans le chapitre précédent. En revanche, comprendre *pourquoi* ce mécanisme existe, quel rôle il joue réellement dans Bitcoin et pourquoi il est irremplaçable, est une toute autre affaire. C'est justement l'objet de ce chapitre.

### Ce qui sécurise vraiment Bitcoin

Lorsqu'on demande à quoi sert la preuve de travail ou le minage, la réponse la plus courante est : "elle sécurise Bitcoin". Mais cette réponse est imprécise et peut induire en erreur. Elle laisse entendre que la sécurité de Bitcoin repose sur une technologie, un algorithme ou une quantité de puissance de calcul. Cependant, ce n'est pas le cas. Aucun de ces éléments, pris isolément ou même combinés, ne constitue la source réelle de la sécurité du système Bitcoin.

Un algorithme de chiffrement, aussi robuste soit-il, ne sécurise rien si la clé privée est notée sur un post-it collé à l'écran de votre ordinateur. Un nœud Bitcoin qui valide les règles de consensus ne sécurise rien si son opérateur cède à la première injonction d'un gouvernement hostile. Une ferme de minage ne protège rien si son propriétaire accepte de censurer les transactions qu'on lui demande de filtrer. Dans chacun de ces cas, la technologie reste la même, mais la sécurité a disparu.

Ce qui sécurise Bitcoin à la fin, **ce sont toujours les personnes** qui font fonctionner le système et qui acceptent les risques associés. Un opérateur de nœud qui refuse d'appliquer une mise à jour contraire à ses valeurs prend un risque : celui de se retrouver isolé, ou sous pression juridique. Un mineur qui choisit de ne pas censurer certaines transactions prend un risque : celui d'être ciblé par des régulateurs. Un commerçant qui accepte le bitcoin comme moyen de paiement prend un risque : celui de s'exposer à une législation défavorable. C'est la somme de ces décisions individuelles, prises par des milliers de personnes à travers le monde, qui constitue la véritable sécurité de Bitcoin.

La décentralisation prend ici tout son sens. Ce n'est pas uniquement une propriété technique que l'on mesure en comptant les nœuds. C'est également un mécanisme de répartition du risque. Dans un système centralisé (une banque, un service de paiement...), une seule entité porte l'intégralité de la responsabilité et de l'exposition aux risques. Si cette entité est contrainte, corrompue ou simplement défaillante, tout le système tombe. Dans Bitcoin, ce risque est fragmenté et diffusé entre des milliers de participants indépendants, répartis dans des juridictions différentes, opérant sous des cadres légaux différents. Pour compromettre le système, il ne suffit pas de cibler une entreprise ou un serveur : il faudrait contraindre simultanément un nombre suffisant de ces acteurs dispersés, ce qui est d'une difficulté bien supérieure.

012

Inversement, si demain plus personne n'était disposé à opérer un nœud, à sélectionner les transactions honnêtement ou à utiliser le bitcoin comme monnaie, aucune quantité de hashrate ne pourrait sauver le système. Un système où tous les mineurs sont concentrés dans une seule juridiction, où tous les nœuds sont hébergés chez le même fournisseur cloud, et où tous les utilisateurs passent par le même intermédiaire, dispose peut-être d'un hashrate élevé, mais sa sécurité réelle est fragile. La technologie fournit les outils (la cryptographie, le protocole, le mécanisme de consensus...), mais ce sont les individus qui décident de les utiliser, et qui acceptent les coûts et les risques que cela implique.

*Ce passage s’inspire du chapitre [Risk Sharing Principle](https://github.com/libbitcoin/libbitcoin-system/wiki/Risk-Sharing-Principle) du livre Cryptoeconomics: Fundamental Principles of Bitcoin, d’Eric Voskuil (2020).*

Une fois cette réalité posée, on peut reformuler notre problème avec plus de précision : la preuve de travail n'est donc pas ce qui sécurise Bitcoin. Mais alors, à quoi sert-elle ? Examinons chacun de ses rôles, puis voyons pourquoi la preuve de travail est particulièrement adaptée à chacune de ses fonctions.

### Premier rôle : établir un consensus sur l'historique des transactions

#### Le problème fondamental

Si l'on se fie au titre du White Paper, Bitcoin est un système de cash électronique pair-à-pair. Son but est donc de permettre des échanges monétaires ("cash") sans recourir à un tiers de confiance ("pair-à-pair"). Mais cet objectif se heurte immédiatement à un problème technique : la double dépense.

Dans un système monétaire physique, reposant par exemple sur des pièces d’or, la double dépense ne pose pas de problème. Le simple fait de remettre physiquement sa pièce empêche mécaniquement de la réutiliser ultérieurement de manière frauduleuse, puisqu’on n’en a tout simplement plus la possession.

013

Dans un système de monnaie numérique, en revanche, rien n’empêche un utilisateur de dupliquer les mêmes données et de tenter de dépenser les mêmes unités monétaires deux fois, auprès de deux destinataires différents. Pour s’assurer qu’aucune fraude n’a lieu, lorsqu’on reçoit une unité monétaire via une transaction, il est donc nécessaire de pouvoir vérifier que cette unité n’a jamais été dépensée auparavant. Autrement dit, le problème revient à être capable de vérifier l’absence d’un paiement dans le passé. Or, le seul moyen d’y parvenir consiste à disposer d’une connaissance complète de l’ensemble des dépenses passées.

Satoshi Nakamoto résume cet enjeu dans le White Paper par cette célèbre phrase :

> *The only way to confirm the absence of a transaction is to be aware of all transactions.*

[Nakamoto, S. (2008). *Bitcoin: A Peer-to-Peer Electronic Cash System.*](https://bitcoin.org/bitcoin.pdf)

Dans un système de monnaie numérique centralisée, ce problème est facile à gérer : une entité tient un registre unique et fait autorité (la banque par exemple).

014

Mais dans un système pair-à-pair comme Bitcoin, sans serveur central et sans autorité, il faut que tous les participants du réseau soient au courant de toutes les transactions et, surtout, qu'ils s'entendent sur un historique unique. Autrement dit, pour savoir qu'un bitcoin n'a pas déjà été dépensé, il faut disposer d'un historique complet, ordonné et consensuel de toutes les transactions passée.

Cet historique commun, c’est la blockchain. Elle enregistre l’ensemble des transactions effectuées, les organise au sein de blocs, et sert de référence à partir de laquelle les nœuds mettent à jour leur UTXO set. L'UTXO set permet ensuite de vérifier rapidement, lors de la validation de nouvelles transactions, que les fonds utilisés n’ont pas déjà été dépensés par le passé.

Mais il y a encore un problème : pour que cet historique qu’est la blockchain soit réellement efficace contre la double dépense, il faut que l’ensemble des nœuds s’accordent sur une version unique de celle-ci. Il faut que les nœuds fassent consensus sur une blockchain unique.

C'est précisément ici qu'intervient le premier rôle de la preuve de travail. Le principe de consensus de Nakamoto définit une règle simple : les nœuds honnêtes doivent s'accorder sur la blockchain qui dispose de la plus grande quantité de travail accumulé. C'est cette version qui est considérée comme l'unique version valide de l'historique. Les nœuds s'entendent ainsi spontanément sur une version unique de la blockchain, sans besoin de se connaître, de se faire confiance, ni de passer par un coordinateur central.

**Remarque :** beaucoup de personnes pensent que c’est la blockchain la plus longue qui fait foi. Cette idée reçue provient d’une phrase mal interprétée de Satoshi Nakamoto dans la partie 4 du White Paper. Si c'était le cas, Bitcoin serait vulnérable, car la chaîne la plus longue (sous-entendue celle ayant la plus grande hauteur de bloc) n’est pas nécessairement celle qui cumule le plus de travail. En réalité, c'est bien la blockchain ayant accumulé le plus de travail qui est suivie par les nœuds.

011

#### La résistance aux attaques Sybil

La preuve de travail ne se contente pas de fournir un critère de sélection entre chaînes concurrentes. Elle résout un problème plus profond dans les mécanisme de décision collective : celui de la résistance aux attaques Sybil.

Une attaque Sybil consiste à créer un grand nombre de fausses identités au sein d’un système afin d’exercer une influence indue ou d’obtenir un avantage non autorisé. Par exemple, s’il y a un vote dans un village pour élire le maire, et que Bob, habitant de ce village, parvient à glisser plusieurs bulletins dans l’urne en se faisant passer pour différentes personnes, alors qu’il ne devrait voter qu’une seule fois, il s’agit d’une forme d’attaque Sybil. Bob a exercé une influence indue sur un mécanisme de prise de décision en multipliant artificiellement les identités.

015

Et justement, dans le fonctionnement de Bitcoin, il existe un mécanisme de prise de décision. Nous disposons d’un historique commun des dépenses réalisées, mais se pose alors une question : qui a le droit d’écrire cet historique ? Il a été décidé que cette prise de décision se ferait de manière alternée. À chaque nouveau bloc, une nouvelle entité est désignée pour ajouter une portion supplémentaire à l’historique commun.

Pour déterminer qui obtient ce droit, on aurait pu imaginer un principe du type "une personne = une voix", ou un équivalent. Mais dans un système informatique, ce type de règle est facilement contournable. Si l’on considère que chaque nœud, ou chaque adresse IP, dispose d’une voix, un attaquant peut facilement créer des milliers de nœuds ou des milliers d’adresses IP afin de s’octroyer une majorité frauduleuse. Satoshi Nakamoto identifie précisément ce risque dans le White Paper :

> *If the majority were based on one-IP-address-one-vote, it could be subverted by anyone able to allocate many IPs.*

[Nakamoto, S. (2008). *Bitcoin: A Peer-to-Peer Electronic Cash System.*](https://bitcoin.org/bitcoin.pdf)

Il a donc l’idée de fonder la détermination de la décision majoritaire sur le travail, à travers le principe de la preuve de travail. Plutôt que de considérer que chaque personne dispose d’un vote (une approche vulnérable aux attaques Sybil) le droit de proposer un bloc est attribué par une loterie proportionnelle à la puissance de hachage effectivement dépensée. Chaque hash calculé correspond en quelque sorte à un ticket de loterie, puisque la fonction de hachage utilisée n'est pas réversible. Il l'explique par cette phrase :

> *Proof-of-work is essentially one-CPU-one-vote.*

[Nakamoto, S. (2008). *Bitcoin: A Peer-to-Peer Electronic Cash System.*](https://bitcoin.org/bitcoin.pdf)

Dans ce cadre, déployer des milliers de nœuds n’apporte aucun avantage en soi : sans puissance de calcul supplémentaire, aucun travail supplémentaire n’est réalisé. L’attaque Sybil, qui repose sur la multiplication d’identités à coût quasi nul, devient alors inefficace face à un système où le "vote" se mesure en énergie réellement dépensée, et non en identités déclarées. Le problème de l’attaque Sybil n’est donc pas résolu au sens strict, mais contourné.

C'est ce qui fait de la preuve de travail le socle du consensus sur Bitcoin : elle permet de choisir, de manière décentralisée et sans permission, qui a le droit de publier le prochain bloc, c'est-à-dire d'écrire la prochaine page de l'historique économique accepté par tous.

### Deuxième rôle : sceller l'historique économique

Le consensus de Nakamoto par preuve de travail ne permet pas, à proprement parler, de valider des transactions. Les transactions sont validées par les nœuds du réseau, qui vérifient indépendamment que chacune respecte les règles du protocole. Le deuxième rôle la preuve de travail est différent : elle permet de sceller un historique économique, c'est-à-dire de rendre progressivement irréversible l'ordre dans lequel les transactions ont été enregistrées.

Pour comprendre ce mécanisme, il faut revenir à la structure de la blockchain. Chaque bloc contient l'empreinte cryptographique du bloc précédent. Modifier une transaction dans un bloc ancien implique de recalculer l'empreinte de ce bloc, puis celle du bloc suivant, puis du suivant, et ainsi de suite. L'attaquant devrait refaire la preuve de travail de tous ces blocs, puis rattraper et dépasser le travail que la chaîne honnête continue d'accumuler pendant ce temps. Plus les blocs s'accumulent au-dessus d'une transaction, plus la réécriture de l'historique devient coûteuse et improbable.

016

Comme l'explique LaurentMT dans son article [*Gravity*](https://medium.com/@laurentmt/gravity-10e1a25d2ab2), la preuve de travail possède deux propriétés importante : elle est **globale** et **cumulative**. Lorsqu'un nouveau bloc est miné, la protection apportée par sa preuve de travail ne s'applique pas uniquement aux transactions contenues dans ce bloc. Elle s'applique simultanément et uniformément à tous les UTXOs existants dans le système. Chaque UTXO accumule ainsi une quantité croissante de protection face à la réécriture, à chaque nouveau bloc miné, indépendamment du moment de sa création.

> *In the case of Bitcoin: when a new block is mined, the security provided by its PoW is simultaneously and equally applied to all the existing UTXOs.*

[LaurentMT (2018). *Gravity.*](https://medium.com/@laurentmt/gravity-10e1a25d2ab2)

Pour bien visualiser cela, l'analogie proposée par LaurentMT dans cet article est intéressante : la preuve de travail agit comme un champ gravitationnel dont la masse augmente avec chaque nouveau bloc. Cette "gravité" exerce une influence simultanée et homogène sur tous les corps (UTXOs) présents dans son champ. Plus la masse s'accumule, plus il devient difficile de déplacer un objet qui y est enraciné (c'est-à-dire de réécrire une transaction passée).

017

Pour résumer, attaquer l'historique de Bitcoin implique donc deux types de coûts :
- Le premier est un coût matériel et énergétique direct : il faut acquérir suffisamment de puissance de calcul pour reconstruire une chaîne alternative avec plus de travail que la chaîne honnête, ce qui nécessite des investissements considérables en machines et en électricité.
- Le second est un coût d'opportunité : pendant toute la durée de l'attaque, l'attaquant renonce aux revenus qu'il aurait pu obtenir en minant honnêtement.

Plus une transaction Bitcoin accumule de confirmations, plus le coût combiné de la réécriture devient prohibitif, et plus la probabilité de succès d'une telle attaque diminue.

### Troisième rôle : distribuer les unités monétaires

#### Un problème inédit dans l'histoire monétaire

Avant Bitcoin, aucune monnaie n'a véritablement eu besoin d'un mécanisme de distribution initiale programmé. La raison en est simple : les monnaies historiques se sont toujours adossées, au moins à l'origine, à des ressources naturelles préexistantes. Les coquillages *cauris*, utilisés comme monnaie d'échange en Afrique de l'Ouest, en Chine et en Asie du Sud pendant des siècles, n'ont pas eu de distribution initiale. Ils étaient collectés, échangés, et leur acceptation comme instrument monétaire s'est faite progressivement, par un processus social spontané. L'or a suivi un chemin similaire : sa valeur en tant que matériau décoratif et artisanal a précédé son usage monétaire. Les premières pièces d'or frappées, comme celles du royaume de Lydie au VIIe siècle avant notre ère, formalisaient un usage qui existait déjà de fait. Même les monnaies fiat modernes, bien qu'elles ne soient plus directement convertibles en métal précieux comme vous le savez, tirent leur origine de certificats de dépôt représentant une quantité d'or ou d'argent physiquement détenue.

Dans tous ces cas, la distribution initiale de la monnaie ne posait pas de problème conceptuel. Les unités monétaires existaient déjà dans la nature sous forme de ressource physique, et leur mise en circulation s'opérait naturellement. Personne n'a eu besoin de décider comment distribuer l'or aux humains : ceux qui le trouvaient, l'extrayaient ou l'obtenaient par l'échange en disposaient, et le marché se chargeait du reste.

Bitcoin est donc la première forme de monnaie créée intentionnellement à partir de rien, dans le but explicite de servir de système de cash électronique pair-à-pair. Il n'existe pas de bitcoin dans la nature attendant d'être découvert. Chaque unité doit être créée via le protocole lui-même. Cela pose donc un nouveau problème : par quel mécanisme mettre ces unités en circulation de manière compatible avec les principes d'un système sans autorité centrale ?

#### Le théorème de régression et son invalidation

Cette situation entre justement en tension avec une théorie économique très connue des bitcoiners : le théorème de régression, formulé par Ludwig von Mises dans *The Theory of Money and Credit* en 1912. Selon ce théorème, la valeur d'une monnaie peut toujours être "régressée", c'est-à-dire retracée, jusqu'à sa valeur d'usage en tant que bien non monétaire. L'idée est qu'un bien acquiert d'abord une valeur d'échange grâce à son utilité intrinsèque (l'or comme métal décoratif, le sel comme conservateur alimentaire...), puis, progressivement, cette valeur d'échange conduit à son adoption comme instrument monétaire. Mises considérait cette progression comme une nécessité logique et non simplement une observation historique.

Or Bitcoin vient directement contredire cette affirmation. Satoshi Nakamoto a explicitement conçu Bitcoin comme une monnaie dès l'origine, comme en témoigne le titre même du White Paper. Le bitcoin n'a jamais eu de valeur d'usage préalable en tant que bien non monétaire. Il n'a pas été d'abord un objet décoratif, un matériau industriel, ou une denrée alimentaire avant de devenir une monnaie. Sa première valorisation a été faite directement en tant que monnaie, par des individus qui ont estimé, subjectivement, que ce système avait de la valeur en tant qu'instrument d'échange et de réserve.

Le théorème de régression contient en réalité une faille : il prétend s'appuyer sur la théorie subjective de la valeur, tout en imposant une contrainte objective sur ce qui peut ou ne peut pas être valorisé comme monnaie. Si la valeur est véritablement subjective, alors elle peut reposer sur n'importe quelle raison, y compris l'anticipation d'un usage monétaire futur. Le théorème, formulé comme une loi a priori, est en réalité fondé sur une observation empirique de l'évolution monétaire passée, et Bitcoin constitue précisément le contre-exemple qui l'invalide.

018

#### La distribution par la preuve de travail

Confronté à ce problème de distribution initiale sans précédent, Satoshi a adopté un mécanisme plutôt élégant : les nouveaux bitcoins sont créés et distribués en échange du travail fourni pour faire fonctionner le système de consensus. Lorsqu'un mineur produit un bloc valide, le protocole l'autorise à créer un certain nombre de nouveaux bitcoins et à se les attribuer via la transaction coinbase. Ces bitcoins sont créés ex nihilo.

Cette subvention est dégressive grâce aux halvings : elle commence à 50 BTC par bloc, puis est divisée par deux tous les 210 000 blocs, jusqu'à atteindre zéro aux alentours de l'an 2140. Ce calendrier est inscrit dans le code du protocole, et il garantit que la politique monétaire soit prévisible, vérifiable par tous, et non soumise à l'arbitraire d'une autorité.

### Pourquoi la preuve de travail est irremplaçable dans ces rôles ?

Après avoir identifié les trois rôles de la preuve de travail sur Bitcoin, une question se pose : pourquoi ce mécanisme en particulier ? Pourquoi n'a-t-il jamais été remplacé depuis la création de Bitcoin ?

#### Pour le consensus

Le premier avantage de la preuve de travail pour le consensus est qu'elle fonctionne dans un réseau entièrement ouvert, sans identité ni permission préalable. N'importe qui peut participer au minage à tout moment, sans s'inscrire, sans révéler son identité, et sans demander l'autorisation à qui que ce soit. C'est une conséquence directe du fait que le "vote" est proportionnel à la puissance de calcul, et non à une identité enregistrée.

Le second avantage est la facilité de vérification. Produire une preuve de travail est extrêmement coûteux, mais la vérifier est trivial : il suffit de hacher l'entête du bloc et de vérifier que l'empreinte obtenue est inférieure à la cible. Cette asymétrie est importante, car elle signifie que n'importe quel nœud du réseau, même un appareil léger, peut vérifier rapidement la validité d'un bloc sans avoir à reproduire le travail. Il n'est pas nécessaire de faire confiance au mineur, ni à aucune autorité intermédiaire.

Enfin, la preuve de travail ne nécessite aucune coordination préalable entre les participants. Chaque mineur travaille de son côté, et le réseau converge naturellement vers la chaîne avec le plus de travail accumulé.

#### Pour la protection de l'historique

L'avantage de la preuve de travail pour la protection de l'historique tient à la nature de la ressource mobilisée. Le minage repose à la fin sur la consommation d'électricité, une ressource physique, tangible, universelle et surtout **externe** au système. C'est ce point qui distingue fondamentalement la preuve de travail de la preuve d'enjeu.

Dans un système de preuve d'enjeu, la ressource utilisée pour participer au consensus (la monnaie mise en jeu) est la même que celle distribuée en récompense. Cela signifie qu'une coalition détenant une part majoritaire de la monnaie peut maintenir indéfiniment sa position dominante. Un censeur qui acquiert la majorité de l'enjeu dans un système de preuve d'enjeu ne peut pas être évincé, car il contrôle la ressource même qui détermine l'autorité sur le système. La résistance à la censure devient alors impossible.

Avec la preuve de travail, aucune coalition d'acteurs en place, même regroupant 100 % des participants actuels, ne peut empêcher un nouvel acteur d'entrer dans le jeu. La position de chacun dans cette compétition est toujours dynamique et dépend principalement des ressources énergétiques, financières et technologiques investies. L'électricité est une ressource naturellement distribuée dans l'univers : personne n'en a le monopole, et de nouvelles sources peuvent toujours être mobilisées.

019

La preuve de travail est ainsi irremplaçable parce qu'elle implique un coût marginal établi sur l'utilisation d'une ressource découverte, et non d'une ressource inventée. C'est la seule ressource naturelle que l'on peut solliciter sur un système informatique pour créer un coût marginal réel à la multiplication des votes. C'est cette propriété qui rend le système intrinsèquement ouvert et résilient dans un environnement hostile, y compris face à des attaques étatiques.

#### Pour la distribution monétaire

La distribution des bitcoins via la preuve de travail présente une propriété importante : elle est entièrement anonyme et ne requiert aucune identification. N'importe qui peut obtenir des bitcoins nouvellement créés en participant au minage, sans donner son nom, son adresse, sa nationalité ou toute autre information personnelle. Il suffit de fournir le travail. Cette caractéristique rend le processus de distribution fondamentalement égalitaire, au sens où il n'existe aucune discrimination ni aucun favoritisme possible dans l'attribution des récompenses. C'est d'ailleurs une des différences fondamentales entre Bitcoin et les altcoins. 

Cela ne signifie pas nécessairement que cette distribution initiale est la plus juste en tout sens philosophique du terme. On pourrait imaginer des critères de justice alternatifs, par exemple une distribution proportionnelle aux besoins ou à la contribution (comme la contribution au code par exemple). Mais ces critères supposent l'existence d'une autorité capable de les évaluer et de les appliquer, ce qui est incompatible avec un système pair-à-pair sans tiers de confiance. Dans le cadre des contraintes de Bitcoin, la distribution par preuve de travail constitue le mécanisme le plus neutre et le plus résistant à la capture : personne ne décide qui reçoit les bitcoins, et le protocole applique les mêmes règles à tous.

Ce mécanisme présente également un alignement incitatif. Les bitcoins sont distribués précisément aux personnes qui contribuent au minage. Au début, lorsque le réseau était vulnérable et avait besoin d'être protégé, la subvention était élevée (50 BTC par bloc), ce qui a permis d'attirer des mineurs malgré l'incertitude sur la valeur future de la monnaie. À mesure que le réseau grandit, que le nombre d'utilisateurs augmente et que la confiance dans le système se renforce, la subvention diminue progressivement. Les frais de transaction, proportionnels à l'usage réel du système, prennent alors progressivement le relais comme source principale de rémunération des mineurs. Cette transition, programmée dès l'origine dans le protocole, assure une cohérence entre le niveau de résistance requis et les incitations économiques fournies aux acteurs qui la produisent.


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












