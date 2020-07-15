##  

Bonjour à tous! Bienvenue dans la vidéo de démarrage rapide de la plateforme CUBA.

La plateforme CUBA est un framework open source lui-même fondé sur le framework Spring bien connu.

Le but de CUBA est de rationaliser le processus de développement des applications commerciales.

Cette vidéo de démarrage rapide couvre les sujets suivants :
● Création de modèle de données et de base de données
● Développement et personnalisation de l'interface utilisateur
● Et la mise en œuvre de la logique métier

À titre d'exemple, nous allons créer une version simple mais entièrement fonctionnelle d'une application qui vous aidera à planifier la session d'une conférence.

La vidéo sera suffisante pour que vous commenciez à développer avec CUBA.
 
Pour cette vidéo, en plus du framework CUBA Platform, nous utiliserons un plugin pour IntelliJ Idea - CUBA Studio.

## 

Créons un nouveau projet CUBA avec « planificateur » comme nom de l'espace de nom du projet. Nous utiliserons Java 11 comme JDK par défaut et la dernière version stable de CUBA.

Nous utiliserons une base de données en mémoire - HSQLDB. Cela est suffisant pour un démarrage rapide. Vous pouvez sélectionner une autre base de données dans la liste déroulante. Laissez les paramètres de connexion à la base de données inchangés.

Le nom du projet sera Session Planner.

Après le premier lancement, CUBA Studio vous propose d'appliquer un abonnement d'essai à CUBA Studio Premium. Il est gratuit pendant 28 jours et nous donne quelques concepteurs optionnels utiles. Appliquons-le.

Dans la partie gauche de l'écran, vous pouvez voir l'arborescence de navigation du projet CUBA. Au centre, il y a une page d'accueil, vous pouvez l'utiliser pour accéder rapidement à certaines fonctions CUBA, à la documentation, à la marketplace, ainsi qu'aux versions et aux informations d'abonnement.

Sur le côté droit de l'écran, vous pouvez voir la fenêtre Gradle Tool. Le framework CUBA utilise Gradle pour construire et exécuter l'application.

La fenêtre de conception standard d'IntelliJ se trouve en bas. Nous pouvons fermer certaines fenêtres pour économiser de l'espace sur l'écran. Le menu principal CUBA offre un accès rapide aux fonctionnalités spécifiques à CUBA au sein de l'IDE.

## 

Nous allons maintenant créer des classes JPA ainsi que des règles métier comme les champs obligatoires, les champs uniques et les relations.

Le modèle de domaine métier ne comporte que deux classes (également appelées entités) : Speaker et Session.

Pour simplifier le processus de développement, nous avons ajouté un concepteur visuel à CUBA Studio qui facilite la création d'entités JPA. Faites un clic droit sur le «modèle de données» et sélectionnez «Nouveau -> Entité».

Tout d'abord, nous devons créer l'entité Speaker. Il comporte trois champs - prénom qui est obligatoire, nom de famille - facultatif et e-mail - obligatoire et unique. Ajoutons une validation pour le champ e-mail.

CUBA offre la possibilité de spécifier un affichage d'entité approprié au format chaîne de caractères dans l'interface utilisateur - «Nom de l'instance». Pour l'orateur, nous sélectionnerons à la fois le prénom et le nom de famille.

Examinons de plus près le concepteur d'entité. À côté de l'onglet concepteur, nous pouvons voir le texte généré – une classe java avec des annotations JPA. Les deux vues sont synchronisées, vous pouvez donc changer le texte et voir toutes les modifications dans le concepteur et vice versa. En outre, vous pouvez voir l'aperçu du DDL et créer des index si nécessaire.

Par exemple, nous nous attendons à beaucoup de recherches par nom de famille. Pour rendre cette recherche plus efficace, nous pouvons créer un index pour le champ du nom de famille.

Allons plus loin, créez l’entité Session et liez-la à notre classe de conférenciers - Speaker. Tout d'abord, définissez l'attribut « topic » - une chaîne de caractères obligatoire. Après cela, nous ajouterons la date de début de session et l'attribut heure. Ajoutez la durée de la session avec validation. La date et l'heure de fin de session seront une valeur calculée, nous la définirons un peu plus tard.

Nous allons maintenant ajouter une référence obligatoire à l'orateur. La relation est multiple, nous allons donc définir un champ d'association appelé locuteur qui fait référence à la classe du locuteur - Speaker.
 
En plus de cela, nous allons créer un champ qui contiendra la description longue d'une session. Pour le nom de l'instance, nous sélectionnerons un champ de rubrique au lieu de la description.

## 

Maintenant, nous allons créer un attribut calculé automatiquement pour la date et l'heure de fin de la session. Ajoutez d'abord une méthode « getter » et nommez-la getEndDate. Marquez la méthode avec l'annotation @MetaProperty. Pour l'attribut calculé, nous devons spécifier les champs à charger. Dans notre cas, ce sont startDate et duration. Ensuite, ajoutez simplement la logique de calcul.

Notez que Studio met en évidence la méthode, car nous devons spécifier une étiquette de texte pour l'attribut. Ajoutons-le aux messages de localisation.

Maintenant, retournez voir le concepteur et jetez un œil. Nous voyons que le champ endDate est ajouté, il est en lecture seule et les attributs associés sont spécifiés.

Notre modèle de domaine est créé. Maintenant, nous pouvons créer une interface utilisateur simple pour effectuer des opérations CRUD sur les tables de la base de données.

## 

CUBA Studio contient un assistant de génération d'écran d'interface utilisateur qui nous aide à créer des écrans d'interface utilisateur basiques mais utiles :
● Le navigateur - pour afficher la liste des entités dans une grille
● Et l'éditeur - pour modifier une instance d'entité à l'aide d'une interface utilisateur de type formulaire

Tout d'abord, nous allons créer des écrans pour travailler avec des Speaker. Puisque cette entité est assez simple, nous pouvons accepter les paramètres par défaut pour la création d'écran.

Comme vous pouvez le voir, chaque écran se compose de deux parties: la disposition en XML qui définit l'apparence de l'écran avec la fonction d'aperçu, et un contrôleur, écrit en java, qui est responsable de la logique d'écran interne et de la gestion des événements.

Jetons un œil à la structure de la disposition de l'écran: il y a deux blocs principaux. Le premier définit la façon dont nous travaillons avec les données et le second bloc définit la disposition des composants à l'écran.

Créons maintenant un navigateur et un éditeur pour les sessions. Ici, nous devons nous arrêter un moment et expliquer certains aspects de CUBA.

Dans la plateforme CUBA, une vue d’entité - Entity View - spécifie les champs qui seront récupérés de la base de données. Vous pouvez définir des vues dans un fichier séparé pour les utiliser dans les différents modules de votre application, ou créer des vues en direct lors de la création des écrans.

Créons donc une vue en direct. Sélectionnez simplement les données nécessaires.

Vous pouvez voir que les champs correspondants sont ajoutés aux écrans.

Maintenant, définissez la valeur de durée par défaut pour la nouvelle session à une heure. Pour ce faire, accédez à l'écran de l'éditeur et abonnez-vous à l'événement InitEntity et définissez cette valeur dans le code.

Ensuite, nous devons générer des scripts pour créer la base de données.

## 

Pour générer les scripts de création de la base de données, vous devez sélectionner le menu CUBA - Générer les scripts de base de données.

Pour appliquer ces scripts et ainsi créer la base de données, cliquez simplement sur Créer une base de données. Outre les tables d'application, CUBA crée des tables système dans lesquelles sont stockées des informations sur les utilisateurs, les rôles, les tâches et autres.

## 

Nous pouvons maintenant exécuter l'application en mode développement à partir de l'EDI.

Appuyez simplement sur «Exécuter» dans l'EDI pour démarrer l'application. Vous pouvez voir le journal d’application dans la fenêtre «Exécuter» du studio. Après un certain temps, vous pouvez accéder à l'application en utilisant le navigateur. Ouvrons l'URL et connectez-vous à l'application en utilisant le nom d'utilisateur "admin". Le mot de passe est «admin» par défaut.

Ajoutons quelques données de test à l'application : commençons par ajouter quelques Speaker.

Comme vous pouvez le voir, la validation des e-mails fonctionne comme prévu.

Ajoutons maintenant des sessions pour aujourd'hui et demain. Vous pouvez voir que la date de fin est calculée automatiquement.

Les écrans générés sont satisfaisant pour les opérations de base, mais dans le monde réel, l'interface utilisateur est généralement plus complexe. Ajoutons une vue agenda pour parcourir les sessions en plus de la vue de grille.

## 

Pour l'écran du navigateur, nous allons ajouter un contrôle d'onglets, y mettre un calendrier et fournir des fonctionnalités pour modifier et replanifier des sessions à l'aide de ce calendrier.

Incorporons le tableau des sessions dans une feuille d'onglets – TabSheet - dans l'écran du navigateur de sessions. Ajoutez une page à onglets de plus et placez-y un contrôle de calendrier.

Développez l'élément TabSheet sur tout l'écran. Studio demande un identifiant. Avec CUBA, il faut un identifiant pour référencer un élément d'écran dans le code.

Définissons également les identifiants et les légendes pour chaque onglet.

Mettez à jour le calendrier - attribuez un conteneur de données et développez-le.

Enfin, développez la table de session.

Dans CUBA, les composants de l'interface utilisateur sont liés aux entités et à leurs propriétés.

Nous allons lier le calendrier aux données de l'écran. Utilisez le champ de recherche pour trouver des propriétés et ainsi lier :
● startDateProperty à la date de début d'une session
● endDateProperty à la date de fin d'une session
● captionProperty au sujet d'une session
● Et descriptionProperty à la description d'une session

Faisons également en sorte que le calendrier affiche uniquement les heures de travail.

En plus du concepteur visuel, vous pouvez utiliser l'éditeur XML. Ajoutons des boutons de navigation.

Nous pouvons rouvrir le formulaire pour voir directement les changements - CUBA prend en charge le redéploiement à chaud pour les écrans. Désormais nous pouvons voir les sessions affichées sur le calendrier.

## 

Appelons maintenant l'écran de l'éditeur de session lorsque nous cliquons sur un événement du calendrier.

Lorsque nous interagissons avec l'interface utilisateur, des événements sont générés. Nous pouvons souscrire à ces événements pour y réagir. Faisons le pour un clic sur une entrée d'agenda.

Nous devons appeler l'écran de l'éditeur pour modifier les propriétés de la session. Utilisons le EditorScreenFacet. Il s'agit d'un composant non visuel qui permet de préconfigurer un écran d'éditeur.

Placez-le sous l'élément « window » dans la fenêtre Hiérarchie des composants. Et définissez les propriétés suivantes :
- un identifiant
- Nous avons besoin d'un éditeur pour la classe de session. Définissez la classe Entity - session et le conteneur de données correspondant
- Classe d'écran - SessionEdit
- Réglez le mode d'édition
- L'écran s'ouvrira en mode dialogue

Revenez au gestionnaire d'événements. Injectez EditorScreenFacet. Passez l'entité de session reçue dans l'objet événement pour modification. Ensuite il faut montrer l'écran d’édition.

Rouvrons l'écran et appelons l'éditeur.

Comme vous pouvez le voir, nous devons affiner la boîte de dialogue de l'éditeur de session pour lui donner une meilleure apparence en ajustant sa largeur et sa hauteur. La façon la plus simple de le faire est de définir «auto» pour la largeur et la hauteur.

Rouvrons l'écran à nouveau et voyons les modifications.

Il est maintenant temps d'ajouter de la logique métier à notre application.

## 

Dans cette section, nous allons utiliser CUBA Studio pour créer un service qui implémente une partie de la logique métier et utiliser ce service dans un contrôleur d'écran. Ce sera un service de reprogrammation de session qui garantira qu’un orateur n’aura pas plus de deux sessions en une journée.

Cliquez avec le bouton droit sur le nœud de service dans l'arborescence du projet CUBA et créez une interface SessionService et une implémentation SessionServiceBean. Créez une méthode dans l'interface et implémentez-la dans ServiceBean.

La méthode acceptera une session et une nouvelle date et heure de session. Le service renverra l'instance de session mise à jour.

Commencez par calculer l'heure de début et de fin de la journée où la session est planifiée.

Nous utiliserons l'API CUBA pour l'accès aux données - la classe DataManager.

Avec DataManager, nous créons une requête JPQL pour vérifier s'il y a des sessions planifiées pour le locuteur dans un laps de temps défini. Nous spécifions les valeurs de paramètres.

Ensuite, nous vérifions le résultat de la requête et, selon le résultat, nous mettons à jour la session avec une nouvelle date de début ou renvoyons simplement l'instance de session d'origine.

## 

Le service est prêt, ajoutons-le maintenant à l'écran du navigateur de sessions. Il sera invoqué pour l'événement glisser-déposer dans le calendrier.

Dans la méthode abonnée à l'événement glisser-déplacer, nous ajoutons du code pour extraire l'entité de session depuis l'événement de calendrier et la transmettons au service pour replanifier cette session. Après cela, nous mettons simplement à jour cet élément dans le conteneur de données du navigateur.

Pour que le nouveau service soit redéployé, nous devons redémarrer l'application.

Après le redémarrage, nous pouvons ouvrir le calendrier des sessions - et le tour est joué! Nous avons une fonctionnalité de replanification par glisser-déposer pour le calendrier! Voyons voir comment cela fonctionne. Ajoutez simplement quelques sessions supplémentaires et essayez de reprogrammer l'une des sessions à une journée qui est déjà pleine.  

## 

Dans cette section, nous allons personnaliser les messages standards dans l'application, en ajoutant un logo.

Dans CUBA, vous pouvez mettre à jour les fichiers de ressources et remplacer les textes standard. Mettons à jour le texte en fonction du domaine d'activité de l'application - Planification de conférences.

Avec la fonctionnalité de déploiement à chaud de CUBA, tout ce que nous avons à faire est de nous reconnecter à l'application pour voir les modifications.

## 

Le framework est livré avec une marketplace qui contient de nombreux composants, de sorte que vous pouvez facilement ajouter des fonctionnalités utiles comme des graphiques ou des cartes géographiques à votre application.

Vous pouvez installer ces composants directement depuis Studio en utilisant le menu «Marketplace».

Ajoutons ensuite le module complémentaire Helium. Il s'agit d'un nouveau thème visuel que vous pouvez utiliser à la place des thèmes standards. Cliquez simplement sur installer et appliquez les modifications.

Nous devons maintenant arrêter l'application et appliquer les scripts d'initialisation du module complémentaire.

Exécutez l'application et accédez à l'écran des paramètres. Vous pouvez trouver le thème ajouté dans la liste déroulante. Sélectionnez-le et appliquez. Connectez-vous à nouveau à l'application - le thème est bien appliqué. Vous pouvez également ouvrir l'écran des paramètres du thème et le modifier avec un aperçu des changements.

## 

Le framework CUBA fournit de nombreuses API utiles pour vous aider à créer rapidement des applications métier. Ce démarrage rapide ne montre que les bases du framework CUBA et de CUBA Studio.

Vous pouvez trouver plus d'exemples et de tutoriels sur notre site Web: cuba-platform.fr.

Merci d'avoir regardé!