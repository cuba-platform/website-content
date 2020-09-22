# Gestion des versions stables

CUBA Platform suit une numération sémantique standard pour ses versions stables - majeure.mineure.maintenance

Version majeure
Version mineure
Version de maintenance

## VERSION DE MAINTENANCE
Une version de maintenance est rétrocompatible, apportant principalement des corrections de bogues, des améliorations mineures, des correctifs pour les problèmes critiques de performances et de sécurité. Dans de rares cas, il peut inclure des fonctionnalités mineures ou des améliorations d'API, qui n'introduisent pas de modifications importantes.

## VERSION MINEURE
Une version mineure est la plupart du temps rétrocompatible, cependant, elle peut inclure quelques changements incompatibles au niveau fonctionnel et API. Toutes les modifications importantes sont répertoriées dans les notes de publication ainsi qu'un moyen de les résoudre. Souvent, les modifications requises dans le code source et les configurations d'application sont appliquées automatiquement par CUBA Studio après une mise à niveau de version. Une version mineure est destinée à introduire de nouvelles fonctionnalités, tout en maintenant la procédure de mise à niveau rapide et facile.

## VERSION MAJEURE
Une version majeure est destinée à mettre-à-jour les paradigmes de développement, les approches et apporte des améliorations massives de l'architecture et des fonctionnalités. En général, elle introduit des changements incompatibles dans l'architecture de base, les fonctionnalités, l'API au niveau de application, les bibliothèques sous-jacentes et leurs versions. Les mises-à-jour majeures n'ont aucune obligation d'être rétrocompatibles. Néanmoins, dans les nouvelles versions majeures, l'ancienne API n'est pas supprimée mais devient obsolète, ce qui signifie qu'une mise à niveau formelle peut être effectuée relativement facilement.

# Versions instables et aperçu
Vous devrez peut-être également utiliser des versions en phase de développement:

## VERSION INSTANTANÉE (SNAPSHOT)
est marquée comme
majeure.mineure-SNAPSHOT.
Il s'agit d'une version nocturne de la branche de développement. Ces versions peuvent être utilisées pour un accès anticipé aux fonctionnalités les plus récentes.

## VERSION ALPHA
est marquée comme
majeure.mineure [.maintenance] .ALPHAx
c’est un aperçu de la prochaine version.

## VERSION BÊTA
est marquée comme
majeure.mineure [.maintenance] .BETAx
c’estun aperçu final et complet de la prochaine version.

# Conditions de support

## 5 ANS 
Maintenance gratuite
Fournie pendant 5 ans pour chaque dernière version mineure. Cela signifie que les versions de maintenance seront publiées dans les 5 ans suivant la publication de la dernière mise à jour mineure stable pour chaque version majeure de CUBA Platform. Une maintenance gratuite est également fournie pendant 3 mois pour la version mineure précédente de chaque version majeure depuis la sortie de la nouvelle version mineure. Une telle période est supposée être suffisante pour mettre à jour votre application vers la dernière version mineure.
Par exemple, la version 100.8.x sortira le 1er septembre 3000. L'avant-dernière version mineure (100.7.x) sera sous maintenance gratuite jusqu'au 30 novembre 3000. La version 100.8.x devient la dernière version mineure de la version majeure 100. Ainsi, la version 100.8.x sera sous support de maintenance gratuit pendant encore 5 ans jusqu'au 31 août 3005; ou jusqu'à ce que la nouvelle version mineure (100.9.x) soit publiée.
Veuillez noter que même si nous nous engageons à respecter cette politique de maintenance gratuite, il ne s'agit pas d'une offre juridiquement contraignante et peut éventuellement changer.

## 10 ANS 
Maintenance commerciale
Disponible pour toute version mineure de CUBA Platform, publiée au cours des 10 dernières années. Cette option peut être utile pour les projets avec un long cycle de mise-à-jour. De plus, en restant sur la dernière version mineure pendant plus de 5 ans, il est également possible d'utiliser cette option pour prolonger la maintenance de 5 ans supplémentaires.
Vous pouvez en savoir plus sur le service de maintenance commerciale ici.

## AU DELA 
Fin de vie
Elle se produit après 10 ans (3650 jours) depuis la date de sortie. En d'autres termes, les versions de plus de 10 ans ne sont pas maintenues, même sur une base commerciale.
