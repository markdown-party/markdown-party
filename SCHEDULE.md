# Planning

Ce document énumère les différents jalons de mon travail de bachelor, les échéances principales et les points notables des [directives sur les travaux de bachelor](GUIDELINES/Directives.pdf). Ce planning sera amené à évoluer, et a comme objectif de donner un cadre temporel au travail.

## Calendrier semestriel

| | # | Date | Objectifs |
|---|:-:|:----:|-----------|
| | 1 | 22.02.2021 | <ul><li>Définition des **objectifs** et du **workflow**<ul><li>Il existe de la documentation indiquant clairement quels sont les objectifs fonctionnels et non-fonctionnels du projet. La méthodologie de travail est établie (Agile, Waterfalling, etc.). Ces documents sont publiés sur GitHub.</li></ul></li><li>Définition du planning<ul><li>Un calendrier définit les jalons principaux, ainsi qu'une séparation par semaine des différentes étapes à atteindre. Les activités principales et de soutien sont indiquées et distinctes.</li></ul></li><li>_Mise en place d'une organisation GitHub_<ul><li>Un repository est mis en place pour chaque package qui sera développé lors du projet. Un système d'intégration continue simple est mis en place; il permet au moins d'effectuer des tests unitaires dans chaque module. Des pipelines de déploiement automatisées publient le code et les paquets sur [markdown.party](https://markdown.party).</li></ul></li></ul> |
| | 2 | 01.03.2021 | <ul><li>Mise en place de **mockups** pour l'application web. Il est possible de naviguer dans le mockup, afin de comprendre les interactions entre les écrans et la structure de l'expérience utilisateur. _Une charte graphique pour l'application web est définie_.</li><li>_Mise en place de la landing page de l'application web_</li><li>**Travail préparatoire sur le protocole de réplication**. Une spécification haut-niveau du protocole de réplication est rédigée. Elle explique le découpage entre les différents niveaux d'abstraction, les technologies ou approches utilisées (Websocket, HTTP, REST, RPC). Cette spécification établit une bibliographie qui sert de base théorique au travail de bachelor.</li></ul> |
| | 3 | 08.03.2021 | <ul><li>**Conception et prototypage du protocole de réplication.** Un début d'implémentation du protocole est rédigé en **Kotlin**. L'implémentation découle directement du travail préparatoire de la semaine 2.</li><li>J'envisage de commencer le travail d'implémentation et de prototypage du protocole de réplication plus tôt, afin d'identifier plus rapidement les éléments les plus complexes de l'implémentation. De plus, ce temps supplémentaire me permettra de mettre en place une API plus Kotlin-friendly, qui sera bénéfique pour la suite.</li></ul> |
| | 4 | 15.03.2021 | <ul><li>Finalisation du prototype du procole de réplication<ul><li>Il est possible pour plusieurs sites de communiquer par un transport défini. Plusieurs structures de données distribuées (un compteur, un ensemble et une liste) sont implémentées au-dessus du prototype pour évaluer la faisabilité de la réplication de texte structuré. Les APIs principales du prototype sont testées afin de garantir son comportement.</li></ul></li></ul> |
| | 5 | 22.03.2021 | <ul><li>Définition des opérations spécifiques à l'édition de documents Markdown.<ul><li>La spécification est ensuite implémentée au-dessus du protocole de synchronisation. Dans un premier temps, la sémantique d'édition est proche à celle de texte classique; les versions suivantes peuvent approfondir les notions liées au formattage, au déplacement de texte, etc.</li></ul></li></ul> |
| | 6 | 29.03.2021 | <ul><li>Finalisation de la définition et du prototypage **Kotlin** des opérations spécifiques à l'édition de documents Markdown.</li></ul> |
| | | | **VACANCES DE PÂQUES** |
| :star: | 7 | 12.04.2021 | <ul><li>Préparation de la présentation intermédiaire pour la séance commune de feedback.</li><li>**Vendredi 15.04.2021** Vendredi 15.04.2021</li></ul> |
| | 8 | 19.04.2021 | |
| | 9 | 26.04.2021 | <ul><li>Présentation et feedback session (approx. milieu de semestre, date à confirmer)<ul><li>Avec les autres étudiants et quelques assistants.</li></ul></li></ul> |
| | 10 | 03.05.2021 | |
| | 11 | 10.05.2021 | |
| | 12 | 17.05.2021 | |
| | 13 | 24.05.2021 | |
| :star: | 14 | 31.05.2021 | <ul><li>**Vendredi 04.06.2021** Milestone 2 : collaboration simple via le navigateur web</li></ul> |
| | 15 | 07.06.2021 | |
| | 16 | 14.06.2021 | <ul><li>Présentation intermédiaire (à confirmer)</li></ul> |
| | |  | **FIN DES COURS** |
| | 17 | 21.06.2021 | <ul><li>Semaine réservée à la rédaction du rapport.</li></ul> |
| | 18 | 28.06.2021 | |
| :star: | 19 | 05.07.2021 | <ul><li>**Vendredi 09.07.2021** Milestone 3 : finalisation du produit</li></ul> |
| | 20 | 12.07.2021 | <ul><li>Semaine "tampon" pour finaliser ou améliorer le produit.</li></ul> |
| | 21 | 19.07.2021 | <ul><li>Semaine "tampon" pour finaliser ou améliorer le produit.</li></ul> |
| :star: | 22 | 26.07.2021 | <ul><li>Semaine réservée à la rédaction du rapport.</li><li>**Vendredi 30.07 : rendu du travail**</li></ul> |
| | |  | **INTERRUPTION DE COURS** |
| |    | 30.08.2021 | <ul><li>Début des soutenances des TBs</li></ul> |
| |    | 19.09.2021 | <ul><li>Fin des soutenances</li></ul> |

Les milestones sont marquées d'une étoile :star:. Les activité de "soutien" sont marquées _en italique_.