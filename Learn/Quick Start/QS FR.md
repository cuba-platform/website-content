<div id="chapter0" class="section"></div>

## Introduction

CUBA Platform est un framework open source destiné à rationaliser le processus de développement d'applications métier. Il combine une architecture éprouvée, des composants de qualité professionnelle prêts à l'emploi et des outils productifs, afin que vous puissiez fournir des applications Web modernes plus rapidement que jamais.

Dans ce tutoriel de démarrage rapide, nous allons gratter la surface de CUBA et développer une application de planification de conférence très simple mais entièrement fonctionnelle. Il montrera trois choses principales pour créer une application Web: comment concevoir un modèle de données, comment manipuler les données, comment créer la logique métier et, enfin, comment créer une interface utilisateur. À titre d'exemple, nous allons créer une version simple mais fonctionnelle d'une application qui vous aidera à planifier une session pour une conférence. En fait, ce tutoriel sera suffisant pour que vous démarriez votre propre application CUBA, alors commençons. Dans ce tutoriel, nous utiliserons <a target="_self" href="/tools/">CUBA Studio</a>, veuillez donc l'installer avant de commencer et accepter un abonnement d'essai pour avoir accès aux concepteurs visuels.

Code source: [https://github.com/cuba-platform/sample-session-planner](https://github.com/cuba-platform/sample-session-planner).
<div id="chapter1" class="section"></div>

## Créer un projet vide

<div class="timestamp-wrapper">
   <a id="stamp1" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">0:49</div>
  </div></a>
</div>

Créons un projet CUBA vide à l’aide du menu IntelliJ IDEA correspondant. Nommez-le SessionPlanner avec l'espace de noms du projet - planificateur. Nous utiliserons Java 11 comme JDK par défaut.

![1](http://192.168.45.58:1337/uploads/d5e5688d10df4f7895e727786e54e858.png)

Nous utiliserons une base de données en mémoire - HSQLDB.

![2](http://192.168.45.58:1337/uploads/96ee03070bf04167b78b0a9dda4e950e.png)

Après le premier lancement, CUBA Studio vous propose d'appliquer un abonnement d'essai à CUBA Studio Premium.

![3](http://192.168.45.58:1337/uploads/5b77f5dea19a457e96ab3feab4784ed2.png)

Appliquons un abonnement d'essai. Il est gratuit pendant 28 jours et nous donne quelques concepteurs optionnels utiles.


<div id="chapter2"  class="section"></div>

## Création de modèles de données

<div class="timestamp-wrapper">
   <a id="stamp2" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">2:31</div>
  </div></a>
</div>

La première tâche consiste à créer des entités. Le modèle de domaine métier ne comprend que deux classes: Speaker et Session. La relation est de type un-à-plusieurs. Un orateur peut diriger plusieurs sessions.

![4](http://192.168.45.58:1337/uploads/5379b3de22a043719690a520aafb9157.png)

Pour commencer, l’entité ```Speaker```. Pour ce faire, nous pouvons utiliser un lien sur la page d'accueil du projet:

![5](http://192.168.45.58:1337/uploads/91fd78c155c345298d18168de91054a7.png)

Ou faites simplement un clic droit sur le nœud **Data Model** dans l'arborescence du projet CUBA sur le côté gauche de l'écran et sélectionnez **New -> Entity**.

![6](http://192.168.45.58:1337/uploads/756992b8e9e54c7ca6898a61904c7185.png)

Entrez le nom de l’entité – ```Speaker``` et ajoutez les attributs selon la spécification:


<div class="scrollable-table ">
<table>

<tbody>

<tr>

<td>Nom</td>

<td>Type</td>

<td>Obligatoire</td>

<td>Autres contraintes</td>

</tr>

<tr>

<td>firstName</td>

<td>String (255)</td>

<td>Oui</td>

<td></td>

</tr>

<tr>

<td>lastName</td>

<td>String (255)</td>

<td></td>

<td></td>

</tr>

<tr>

<td>email</td>

<td>String (1024)</td>

<td>Oui</td>

<td>“Email” validator</td>

</tr>

</tbody>

</table>
</div>

Dans CUBA, nous utilisons des entités JPA standard, vous pouvez donc les créer à l'aide d'un éditeur de code ou d'un concepteur visuel. Cliquez simplement sur l'icône «+» et ajoutez des attributs à l'entité, CUBA Studio générera des membres de classe pour vous.

![7](http://192.168.45.58:1337/uploads/dbd351074e2b4932ae1b9a861de79858.png)

Dans CUBA, vous pouvez définir un format approprié pour l’affichage d'une entité sous forme de chaîne de caractères dans l'interface utilisateur appelée **Instance Name**. Pour l'orateur, nous sélectionnons le prénom et le nom de famille.

![8](http://192.168.45.58:1337/uploads/6c685b716b59497da43369d414334700.png)

Si nous jetons un œil à l'onglet **Text** en bas du concepteur d'entités, nous pouvons voir une classe java annotée par JPA. Le code généré peut être mis à jour si nécessaire, le concepteur sera mis à jour en conséquence lorsque vous basculerez vers l'onglet **Designer**.

En outre, vous pouvez voir l’aperçu DDL et créer des indexes si nécessaire. Par exemple, nous nous attendons à beaucoup de recherches par nom de famille. Pour rendre cette recherche plus efficace, nous pouvons créer un index pour le champ du nom.

![9](http://192.168.45.58:1337/uploads/09616e46cc7a4fd9aa301a59f2a8b2cc.png)

Allons plus loin et associons l’entité **Session** à notre classe **Speaker**. Le tableau de spécification des attributs est ci-dessous. L'heure de fin de session sera une valeur calculée, correspondant à la date de début plus la durée.

<br>
<div class="scrollable-table ">
<table>

<tbody>

<tr>

<td>Nom</td>

<td>Type</td>

<td>Obligatoire</td>

</tr>

<tr>

<td>topic</td>

<td>String (255)</td>

<td>Oui</td>

</tr>

<tr>

<td>startDate</td>

<td>LocalDateTime</td>

<td>Oui</td>

</tr>

<tr>

<td>endDate</td>

<td>LocalDateTime</td>

<td></td>

</tr>

<tr>

<td>speaker</td>

<td>Association avec l’entité “Speaker”, cardinalité « many-to-one » </td>

<td>Oui</td>

</tr>
<tr>

<td>duration</td>

<td>Integer</td>

<td>Oui</td>

</tr>
<tr>

<td>description</td>

<td>String (2000)</td>

<td></td>

</tr>

</tbody>

</table>
</div>

Regardez l'ajout d'une référence obligatoire à l'orateur. La relation est « many-to-one », nous allons donc définir un champ d'association appelé « speaker » qui fait référence à la classe Speaker. Enfin, la définition du champ devrait ressembler à ceci:

![10](http://192.168.45.58:1337/uploads/eeb0b3e1aed24c97b54af7c091c730c4.png)

<div id="chapter3" class="section"></div>

## Création d’un attribut calculé 
<div class="timestamp-wrapper">
   <a id="stamp3" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">5:05</div>
  </div></a>
</div>

À présent, nous devons créer un attribut calculé automatiquement pour la date et l'heure de fin de la session. Ajoutez d'abord la méthode getter et nommez-la ```getEndDate```. Marquez la méthode avec l'annotation ```@MetaProperty```. Pour l'attribut calculé, nous devons spécifier les champs à charger. Dans notre cas, ce sont ```startDate``` et ```duration```. Ensuite, ajoutez simplement une logique de calcul.

```java
@Transient
@MetaProperty(related = {"startDate", "duration"})
public LocalDateTime getEndDate() {
   return (startDate != null && duration != null) ? startDate.plusHours(duration) : null;
}
```    

Notez que Studio met en évidence la méthode car nous devons spécifier une étiquette de texte pour l'attribut. Appuyez sur **Alt + Entrée** et ajoutez un label texte à l'ensemble des messages.

![11+](http://192.168.45.58:1337/uploads/1560ae3e7f7949b996597ba4f357b288.png)

C'est tout. Notre modèle de domaine a été créé.

<div id="chapter4" class="section"></div>

## Screens Génération d'écrans CRUD

<div class="timestamp-wrapper">
   <a id="stamp4" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">06:08</div>
  </div></a>
</div>

CUBA Studio contient un assistant de génération d'écran d'interface utilisateur qui nous aide à créer des écrans d'interface utilisateur basiques mais fonctionnels:
*   **Browser** (Navigateur) - pour afficher la liste des entités dans une grille
*   **Editor** (Éditeur) - pour modifier une instance d'entité à l'aide d'une interface utilisateur de type formulaire

Tout d'abord, nous allons créer des écrans pour travailler avec des Speaker. Puisque cette entité est assez simple, nous utiliserons les paramètres par défaut pour la création de l'écran. Démarrez un assistant en cliquant sur l'élément de menu **Create Screen** dans le menu **Screen** en haut du concepteur d'entités.

![11](http://192.168.45.58:1337/uploads/ff8c24fa973d4b0daef88170d6880ffd.png)

Vous pouvez également lancer l'assistant de génération d'écran en cliquant avec le bouton droit sur une entité dans l'arborescence du projet CUBA et en sélectionnant **New  -> Screen** dans le menu contextuel.

![12](http://192.168.45.58:1337/uploads/c52eed9e30664a82b9f1780b291543a2.png)

Pour l'entité ```Speaker```, nous créerons un navigateur et un éditeur par défaut. Sélectionnez **Entity browser and editor screens** dans l'onglet **Screen Templates** de l'assistant et cliquez sur **Suivant**. Puisque cette entité est assez simple, nous pouvons accepter les paramètres par défaut pour la création de l'écran.

![13](http://192.168.45.58:1337/uploads/fc622c87468c42608b0ddba6338e6884.png)

Comme vous pouvez le voir, chaque écran se compose de deux parties: un contrôleur, écrit en java, qui est responsable de la logique interne de l'écran et de la gestion des événements et une mise en page XML qui définit l'apparence de l'écran. Dans notre cas, le navigateur comprendra les fichiers ```speaker-Browse.xml``` et ```SpeakerBrowse.java``` et pour l’éditeur - ```speaker-edit.xml``` et ```SpeakerEdit.java```. Les fichiers sources se trouvent sous **Generic UI -> Screens** dans l'arborescence du projet CUBA.

![14](http://192.168.45.58:1337/uploads/4bbfcb7f14e4422794d52e427e9b9680.png)

Veuillez noter la section des données dans les descripteurs d'écran XML - elle définit la manière dont les données sont extraites de la base de données.

```java
<data readOnly="true">
   <collection id="speakersDc"
               class="com.company.planner.entity.Speaker"
               view="_local">
       <loader id="speakersDl">
           <query>
               <![CDATA[select e from planner_Speaker e]]>
           </query>
       </loader>
   </collection>
</data>
```


Vous pouvez facilement naviguer entre le contrôleur d'écran, le descripteur et les entités liées avec CUBA Studio à l'aide des boutons situés en haut de la fenêtre:

![15](http://192.168.45.58:1337/uploads/d057028a89b6442e84d599c76fa7e3d9.png)

![16](http://192.168.45.58:1337/uploads/b9f005732859488cae0c0b3a0f0103da.png)

![17](http://192.168.45.58:1337/uploads/df24f4e6db384060ade3013b7a0b87b9.png)

<div id="chapter5" class="section"></div>

## Créer un navigateur et un éditeur pour les sessions

<div class="timestamp-wrapper">
   <a id="stamp5" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">07:11</div>
  </div></a>
</div>

Exécutez l'assistant de génération d'écran, sélectionnez **Entity browser and editor screens** et arrêtez-vous à l'étape d'affichage du Entity Browser.

Dans la plate-forme CUBA, Entity View spécifie les champs qui seront extraits de la base de données. Vous pouvez définir des vues dans un fichier séparé pour les utiliser dans les différents modules de votre application, ou créer des vues en ligne lors de la création des écrans.

Créons une vue en ligne. Sélectionnez simplement les données nécessaires: date de fin et référence de l'orateur.

![18](http://192.168.45.58:1337/uploads/841fb5cfd74e4e8193bc5b5c657564dd.png)

À l'étape suivante, les champs nécessaires sont déjà sélectionnés.

![19](http://192.168.45.58:1337/uploads/75a9d27b0bff4654a05c06c29d1deda4.png)

Vous pouvez voir que les champs correspondants sont ajoutés aux écrans.

![20](http://192.168.45.58:1337/uploads/3591ca65c95b43caa3b195799d7323e5.png)

En outre, définissez la durée par défaut de la nouvelle session sur une heure. Pour ce faire, accédez à la fenêtre **Component inspector** et abonnez-vous à l'événement ```InitEntity``` dans l'onglet Gestionnaires.

![21](http://192.168.45.58:1337/uploads/d2e2c0d53ec34b6b908e166d034e069a.png)

Définissez ensuite cette valeur dans le code.

```java
@Subscribe
public void onInitEntity(InitEntityEvent<Session> event) {
   event.getEntity().setDuration(1);
}
```

<div id="chapter6" class="section"></div>

## Création de la base de données

<div class="timestamp-wrapper">
   <a id="stamp6" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">8:14</div>
  </div></a>
</div>

Sélectionnez le menu **CUBA -> Generate database scripts** pour créer du SQL pour la création de la base de données. Vous pouvez consulter les scripts générés dans la fenêtre contextuelle avant de les enregistrer en tant que fichiers de projet. Veuillez noter que ces scripts font partie du projet, vous pouvez les trouver sous le nœud **Main Data Store** dans l'arborescence du projet.

![22](http://192.168.45.58:1337/uploads/1ad8691035714078b1ccb74187771249.png)

Vous pouvez modifier les scripts si vous souhaitez ajouter des éléments spécifiques comme des index supplémentaires ou insérer des instructions pour les données initiales.

![23](http://192.168.45.58:1337/uploads/13f1902068594db0bebdc5c536c13b57.png)

Cliquez sur le bouton **Create database** pour appliquer ces scripts et créer la base de données. Outre les tables d'application, CUBA crée des tables système dans lesquelles nous stockons des informations sur les utilisateurs, les rôles, les tâches, etc.

C'est tout. La base de données a été créée.

<div id="chapter7" class="section"></div>

## Exécution de l'application en mode développement

<div class="timestamp-wrapper">
   <a id="stamp7" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">8:43</div>
  </div></a>
</div>

Pour exécuter l'application CUBA, vous pouvez utiliser l'une ou l'autre configuration d'exécution en haut de la fenêtre IDE.

![24](http://192.168.45.58:1337/uploads/5805a55479554e9980f5bc834f3cdebd.png)

Ou sélectionnez **CUBA -> Start application server** dans le menu principal.

![25](http://192.168.45.58:1337/uploads/5623110aa63d4e339d4034ab94af1a02.png)

Après un certain temps, vous pouvez accéder à l'application à l'aide du navigateur. L'URL sera affichée dans la boîte à outils Exécuter dans IDEA. Dans notre cas, ce sera http://localhost: 8080/app. Ouvrez l'URL dans votre navigateur Web et connectez-vous à l'application en utilisant «admin» comme nom d'utilisateur. Le mot de passe est «admin» par défaut. Vous pouvez trouver des écrans de manipulation d'entités dans le menu **Application**. Ajoutons ensuite quelques données à la base de données: deux orateurs et deux sessions pour eux prévues pour le reste de la semaine. 

Vous pouvez essayer de saisir un e-mail non valide pour un orateur et voir que le validateur d'e-mail fonctionne comme prévu.

![26](http://192.168.45.58:1337/uploads/0964cbc14ae148fd9af1bc0a92346760.png)

![27](http://192.168.45.58:1337/uploads/9539f76e705041f884ca130e1068a2c6.png)

Les écrans générés conviennent aux opérations de base, mais dans le monde réel, l'interface utilisateur est généralement plus complexe.

<div id="chapter8" class="section"></div>

## Personnalisation de l'interface utilisateur

<div class="timestamp-wrapper">
   <a id="stamp8" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">10:06</div>
  </div></a>
</div>

Ajoutons une vue de calendrier pour parcourir les sessions en plus de la vue en grille. Pour l'écran du navigateur, nous ajouterons un contrôle d'onglet, y placerons un calendrier et fournirons une fonctionnalité permettant de modifier et de replanifier les sessions à l'aide de ce calendrier, comme dans l'image ci-dessous:

![28](http://192.168.45.58:1337/uploads/7d7434f0f5b6425c91a436f2c5258f06.png)

Ouvrez le fichier ```session-Browse.xml``` dans le concepteur et encapsulez la table des sessions dans une feuille d'onglets dans la fenêtre Hiérarchie des composants.

![29](http://192.168.45.58:1337/uploads/93527dbe315c489d9046ec945713966c.png)

Ajoutez une autre page à onglet sous ```TabSheet```.

![30](http://192.168.45.58:1337/uploads/29b393fa4d4748d3890ceb38f37c881f.png)

Mettez un contrôle de calendrier dessus.

![31](http://192.168.45.58:1337/uploads/089eb1bd177c4d7daef8aacfd07cb985.png)

Sélectionnez l'élément ```TabSheet``` dans la fenêtre **Component hierarchy** et sélectionnez ```développé``` dans le **Component inspector**. Studio demande un identifiant. Dans CUBA, nous avons besoin d'identifiants pour référencer un élément d'écran dans le code.

![32](http://192.168.45.58:1337/uploads/c6c060d1a87c43d988554093e87fd5f5.png)

Attribuez les identifiants ```calendarTab``` et ```tableTab``` pour les onglets. Définissez ensuite respectivement les légendes du calendrier des sessions et du tableau des sessions pour ces onglets.

![33](http://192.168.45.58:1337/uploads/c463299f4c6d4667a8fdf307c65cd940.png)

Mettez à jour le calendrier - attribuez un conteneur de données et développez-le.

![34](http://192.168.45.58:1337/uploads/876aad2b5ba94fab894e6c675f1b57ec.png)

Enfin, développez le tableau des sessions.

![35](http://192.168.45.58:1337/uploads/4a3da112870e491484340a44fb3e913d.png)

Dans CUBA, les composants de l'interface utilisateur peuvent être liés à des entités et à leurs propriétés.

Lions l'agenda à la collection de données récupérée à l'écran. Utilisez le champ de recherche pour trouver des propriétés et lier:
- ```startDateProperty``` à la date de début d'une session
- ```endDateProperty``` à la date de fin d'une session
- ```captionProperty``` du sujet d'une session
- Et ```descriptionProperty``` de la description d'une session

![36](http://192.168.45.58:1337/uploads/2286f20bc0c8429d8531b7803bcd3fe4.png)

Faisons en sorte que l'agenda affiche uniquement les heures de travail.

![37](http://192.168.45.58:1337/uploads/b4aca1431e1045a3a45b6c377551cd56.png)

En plus du concepteur visuel, vous pouvez utiliser l'éditeur XML. Ajoutons des boutons de navigation.

![37_1](http://192.168.45.58:1337/uploads/13767361646345ccb67e99a4f3d6ebf6.png)

Pour voir les modifications dans l'interface utilisateur, vous n'avez pas besoin de redémarrer l'application, il vous suffit de rouvrir l'écran dans l'application. Le framework CUBA prend en charge le déploiement à chaud pour les écrans. Vous pouvez maintenant voir les sessions affichées sur le calendrier.

<div id="chapter9" class="section"></div>

## Utilisation de l'API Screen (écrans) 

<div class="timestamp-wrapper">
   <a id="stamp9" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">12:12</div>
  </div></a>
</div>

Lorsque nous interagissons avec l'interface utilisateur, des événements sont générés. CUBA vous propose une API que vous pouvez utiliser pour vous abonner à ces événements afin de les gérer. Traitons un clic sur l'entrée d'un calendrier et appelons l'éditeur de session. Pour les manipulations d'écran, CUBA fournit une API de création d'écran que nous utiliserons.

Dans le fichier ```session-Browse.xml```, sélectionnez ```sessionsCalendar``` et accédez à l'onglet Handlers de la fenêtre **Component Inspector**. Sélectionnez ```CalendarEventClickEvent``` et cliquez sur la flèche pour accéder au contrôleur.

![38](http://192.168.45.58:1337/uploads/d591b22d225f485c943df9dbea42b964.png)

La méthode vide sera générée pour vous.

```java
@Subscribe("sessionsCalendar")
public void onSessionsCalendarCalendarEventClick(Calendar.CalendarEventClickEvent<LocalDateTime> event) {
  
}
```

Nous devons ouvrir l’écran de l’éditeur pour modifier les propriétés de la session. Utilisons ```EditorScreenFacet```. C'est un composant qui permet de préconfigurer un écran d'éditeur.

Accédez au fichier ```session-Browse.xml``` et recherchez l'éditeur dans la palette de composants et déplacez-le sous l'élément de fenêtre dans la fenêtre Hiérarchie des composants.

![39](http://192.168.45.58:1337/uploads/7bdaa35d581840c3b427247174432e31.png)

Définissez les paramètres suivants:

- ID - ```sessionEditDialog```;
- conteneur de données - ```sessionsDc```;
- mode d'édition - ```EDIT```;
- classe d'entité  - ```Session```;
- mode ouverture - ```DIALOG```;
- classe d'écran - ```SessionEdit```.

![40](http://192.168.45.58:1337/uploads/116842df5cc4404f9c9c76d393f9424c.png)

Revenez au gestionnaire d'événements. Cliquez sur le bouton **Injecter** en haut de la fenêtre et sélectionnez la section API de l'écran du formulaire de service ```sessionEditDialog``` dans la fenêtre contextuelle.

Une autre façon pour l'injection (et l'abonnement) - appuyez sur **Alt + Insérer** la combinaison de touches dans l'éditeur et sélectionnez **Injecter** dans le menu contextuel:

![40_1](http://192.168.45.58:1337/uploads/e4af00081f7c4dec959e401a4d8dbbd4.png)

Pour rechercher le service, commencez simplement à taper son nom, puis utilisez les touches Haut et Bas pour naviguer entre les services disponibles pour l'injection.

![40_2](http://192.168.45.58:1337/uploads/8d645c92e6634e0e823490a9c77c78c3.png)

Transmettez l'entité de session reçue dans l'objet événement pour modification. Après cela, nous devrions montrer l'éditeur.

Dans le code du gestionnaire, cela ressemblera à ceci:

```java
@Subscribe("sessionsCalendar")
public void onSessionsCalendarCalendarEventClick(Calendar.CalendarEventClickEvent<LocalDateTime> event) {
   sessionEditDialog.setEntityProvider(() -> (Session) event.getEntity());
   sessionEditDialog.show();
}
```

C'est tout. Vous pouvez rouvrir l'écran du navigateur de sessions dans l'application en cours d'exécution et appeler l'éditeur en cliquant sur la session dans le calendrier.

![41](http://192.168.45.58:1337/uploads/727064a12db4440eb3018a0c15349f7f.png)

Le dialogue de l’éditeur n’est pas correctement dimensionné, nous devons donc ajuster sa largeur et sa hauteur. Dans l'EDI, ouvrez le descripteur XML de l'écran, sélectionnez la propriété ```dialogMode``` et définissez les propriétés de largeur et de hauteur sur ```auto```.

![42](http://192.168.45.58:1337/uploads/6cfb628bc3344893a4f84f126d1e8e72.png)

Allez dans l'application et rouvrez l'éditeur en le fermant et en cliquant à nouveau sur la session dans le calendrier. La fenêtre est maintenant correctement dimensionnée.

![43](http://192.168.45.58:1337/uploads/f87d1d51ffb04a41a86de11fda131326.png)

<div id="chapter10" class="section"></div>

## Ajouter la logique métier

<div class="timestamp-wrapper">
   <a id="stamp10" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">13:58</div>
  </div></a>
</div>

Nous allons maintenant utiliser CUBA Studio pour créer un service qui implémente la logique métier et utiliser ce service dans un écran. Ce sera un service de reprogrammation de session qui garantira qu’un orateur n’aura pas plus de deux sessions en une journée.

Cliquez avec le bouton droit sur le nœud de service dans l'arborescence du projet CUBA et sélectionnez **New -> Service**. Cela ouvrira une boîte de dialogue de création de service. Entrez ```SessionService``` comme nom d'interface, ```SessionServiceBean``` comme nom d'implémentation sera généré automatiquement.

![44](http://192.168.45.58:1337/uploads/18f1b069cf264ae48f795e163cb175d7.png)

Créez une méthode rescheduleSession dans l'interface comme dans l'extrait de code ci-dessous:


```java
public interface SessionService {
   String NAME = "planner_SessionService";

   Session rescheduleSession(Session session, LocalDateTime newStartDate);
}
```

La méthode acceptera une session et une nouvelle date et heure de session. Le service renverra l'instance de session mise à jour.

Pour implémenter la méthode, ouvrez l'éditeur de code pour la classe ```SessionServiceBean```, vous pouvez le trouver sous **Middleware - Services** dans l'arborescence du projet CUBA:

![45](http://192.168.45.58:1337/uploads/5632dcb82f0440d6907ea8817ed1f251.png)

Vous verrez que la classe est vide et invalide:

![461](http://192.168.45.58:1337/uploads/cdbdd0a5e8924ead889d7d967709181f.png)

Appuyez sur **Alt + Insérer** dans le corps de la classe et sélectionnez Implémenter les méthodes dans le menu contextuel:

![46](http://192.168.45.58:1337/uploads/c5c2e8ec7dc641fa922c8ff86de1ec17.png)

Sélectionnez la méthode ```rescheduleSession```, le stub de code sera généré:

```iava
@Service(SessionService.NAME)
public class SessionServiceBean implements SessionService {

   @Override
public Session rescheduleSession(Session session, LocalDateTime newStartDate) {
       return null;
   }
}
```

Tout d'abord, calculez l'heure de début et de fin de la journée où la session est prévue.

```java
@Override
public Session rescheduleSession(Session session, LocalDateTime newStartDate) {
   LocalDateTime dayStart = newStartDate.truncatedTo(ChronoUnit.DAYS).withHour(8);
   LocalDateTime dayEnd = newStartDate.truncatedTo(ChronoUnit.DAYS).withHour(19);
return null;   
}
```

Pour le service, nous utiliserons l'API CUBA pour l'accès aux données – la classe ```DataManager```. Avec cette classe, nous créons une requête JPQL pour vérifier s'il y a des sessions programmées pour le locuteur dans un laps de temps défini et y ajouter des valeurs de paramètres. Ensuite, nous vérifions le résultat de la requête et, en fonction du résultat, nous mettons à jour la session avec une nouvelle date de début ou nous renvoyons simplement l'instance de session d'origine.

Injectez le DataManager dans le service en appuyant sur **Alt + Insérer** dans le corps de la classe et sélectionnez **Injecter** dans le menu contextuel:

![481](http://192.168.45.58:1337/uploads/0098502fde934b20bc993736e2a54790.png)

Sélectionnez ```DataManager``` dans la fenêtre contextuelle:

![48](http://192.168.45.58:1337/uploads/1b2760f8585e4cbb90b1e1d1f824d8b0.png)

L'implémentation de la méthode se trouve sur l'extrait ci-dessous:

```java
@Override
public Session rescheduleSession(Session session, LocalDateTime newStartDate) {
   LocalDateTime dayStart = newStartDate.truncatedTo(ChronoUnit.DAYS).withHour(8);
   LocalDateTime dayEnd = newStartDate.truncatedTo(ChronoUnit.DAYS).withHour(19);
   Long sessionsSameTime = dataManager.loadValue("select count(s) from planner_Session s where " +
           "(s.startDate between :dayStart and :dayEnd) " +
           "and s.speaker = :speaker " +
           "and s.id <> :sessionId", Long.class)
           .parameter("dayStart", dayStart)
           .parameter("dayEnd", dayEnd)
           .parameter("speaker", session.getSpeaker())
           .parameter("sessionId", session.getId())
           .one();
   if (sessionsSameTime >= 2) {
       return session;
   }
   session.setStartDate(newStartDate);
   return dataManager.commit(session);
}
```

Le service est prêt, ajoutons-le maintenant à l'écran du navigateur de session. Il sera appelé pour l'événement glisser-déposer dans le calendrier.

Dans le fichier ```session-Browse.xml```, sélectionnez ```sessionsCalendar``` et accédez à l'onglet **Handlers** de la fenêtre **Component Inspector**. Sélectionnez ```CalendarEventMoveEvent``` et cliquez sur la flèche pour accéder au contrôleur.

Dans la méthode qui est abonnée à l'événement de glisser-déposer, nous ajoutons du code pour extraire l'entité de session de l'événement de calendrier et la transmettons au service pour replanifier cette session. Après cela, nous mettons simplement à jour cet élément dans le conteneur de données du navigateur.

```java
@Subscribe("sessionsCalendar")
public void onSessionsCalendarCalendarEventMove(Calendar.CalendarEventMoveEvent<LocalDateTime> event) {
   Session session = sessionService.rescheduleSession((Session) event.getEntity(), event.getNewStart());
   sessionsDc.replaceItem(session);
}
```

Pour redéployer le nouveau service, nous devons redémarrer l'application, nous pouvons utiliser le bouton **Exécuter** dans IDEA.

![49](http://192.168.45.58:1337/uploads/29aa319f7cec4b959ad179186086f31f.png)

Après le redémarrage, nous pouvons ouvrir le calendrier des sessions - et le tour est joué! Nous avons une fonctionnalité de reprogrammation par glisser-déposer pour le calendrier! Testons-le en ajoutant une session supplémentaire et en essayant de la replanifier.

<div id="chapter11" class="section"></div>

## Définir la marque commerciale

<div class="timestamp-wrapper">
   <a id="stamp11" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">16:31</div>
  </div></a>
</div>

Dans CUBA, vous pouvez mettre à jour les fichiers de ressources et remplacer le texte standard. Mettons à jour le texte en fonction du domaine d’activité de l’application - planification de conférences.

Ouvrez le fichier principal du pack de messages - ```messages.properties``` qui se trouve sous **Generic UI - Main Message Pack** dans l'arborescence du projet CUBA.

![50](http://192.168.45.58:1337/uploads/086d63a8de4b47409358c33dac907544.png)

Il s'agit d'un fichier ```java .properties``` standard, vous pouvez donc le modifier librement, en ajoutant de nouvelles clés de message et en mettant à jour celles existantes. Mettez à jour les messages pour refléter l'objectif de l'application. L'exemple est ci-dessous:

```java
application.caption = Session Planner
application.logoLabel = Session Planner
application.logoImage = branding/app-icon-menu.png
application.welcomeText = Welcome to Session Planner!

loginWindow.caption = Session Planner Login
loginWindow.welcomeLabel = Welcome to Session Planner!
loginWindow.logoImage = branding/app-icon-login.png

menu-config.application-planner = Sessions and Speakers
menu-config.planner_Speaker.browse=Speakers
menu-config.planner_Session.browse=Sessions
```

Avec la fonctionnalité de déploiement à chaud de CUBA, tout ce que nous avons à faire est de nous reconnecter à l'application pour voir les modifications.

<div id="chapter12" class="section"></div>

## Marketplace

<div class="timestamp-wrapper">
   <a id="stamp12" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">17:00</div>
  </div></a>
</div>

Le framework est livré avec une <a target="_self" href="/marketplace/">marketplace</a> qui contient de nombreux composants, vous pouvez donc facilement ajouter des fonctionnalités utiles telles que la prise en charge de graphiques ou de cartes géographiques à l'application existante. Vous pouvez installer ces composants directement depuis le studio en utilisant le menu CUBA -> Marketplace.

![51](http://192.168.45.58:1337/uploads/f5e7ecea2a404fc1b984c8c5b0adeb99.png)

Ajoutons le module complémentaire [Helium](https://www.cuba-platform.fr/marketplace/helium/). Il s'agit d'un nouveau thème visuel que vous pouvez utiliser à la place des thèmes standard. Cliquez simplement sur installer et appliquez les modifications.

Maintenant, nous devons arrêter l'application et mettre à jour la base de données pour appliquer les scripts d'initialisation du module complémentaire.

![52](http://192.168.45.58:1337/uploads/07d40cf677f34df8b7f360f6f8bba299.png)

Exécutez l'application et accédez à l'écran des paramètres. Vous pouvez trouver le thème ajouté dans la liste déroulante. Sélectionnez-le et appliquez.

![53](http://192.168.45.58:1337/uploads/bc7b0d6480f847158fe3b75101859450.png)

Connectez-vous à nouveau à l'application - le thème est appliqué.

![54](http://192.168.45.58:1337/uploads/5255eb8521cc447ca76606537767f880.png)

Nous pouvons également ouvrir l'écran des paramètres du thème et modifier les paramètres avec un aperçu.

<div id="chapter13" class="section"></div>

## Conclusion

<div class="timestamp-wrapper">
   <a id="stamp13" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">17:57</div>
  </div></a>
</div>

Le framework CUBA fournit de nombreuses API utiles pour vous aider à créer rapidement des applications métier. Ce guide de démarrage rapide montre uniquement les bases du framework CUBA et CUBA Studio. Vous pouvez trouver plus <a target="_self" href="/learn">d'exemples</a> et de <a target="_self" href="/documentation">tutoriels</a> sur notre site Web: cuba-platform.com.

Merci de l'intérêt que vous portez à la plate-forme CUBA. Profitez du développement avec CUBA!
