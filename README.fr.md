# Lab de réponse à incident et d’investigation numérique : analyser un serveur Linux compromis

[English](README.md) | [Français](README.fr.md) | [日本語](README.ja.md) | [Español](README.es.md)

[![check](https://github.com/mitsurutech/lab-incident-response/actions/workflows/check.yml/badge.svg)](https://github.com/mitsurutech/lab-incident-response/actions/workflows/check.yml)

Un **lab de réponse à incident et d’investigation numérique** prêt à l’emploi pour la classe, issu de la [plateforme d’apprentissage MitsuruTech](https://mitsurutech.co.uk/fr/learning), où les écoles et les établissements donnent à chaque élève sa propre machine et son propre terminal dans le navigateur. Chaque élève reçoit sa propre VM Ubuntu contenant **les journaux et les fichiers d’un serveur web compromis** : un journal SSH avec une attaque par force brute, un journal d’accès du serveur web rempli de scanners, le fichier des comptes, les crontabs et une liste des dates de modification des fichiers. Il retrouve **l’adresse IP de l’attaquant, le compte qui a été piraté, l’heure de la première connexion et la persistance laissée par l’intrus**, puis consigne ses conclusions dans `answers.txt`. Rien n’est installé sur l’ordinateur de l’élève.

Il n’y a **ni logiciel malveillant ni code d’exploitation**. Tout est du texte, que l’on lit avec les outils présents sur toute machine Linux : `grep`, `awk`, `sort`, `uniq`, `cut`, `less`. Le cas contient des leurres volontaires : l’adresse qui compte le plus de connexions échouées n’entre jamais, un administrateur se trompe de mot de passe puis réussit, et un utilisateur légitime ainsi qu’une ancienne clé SSH côtoient les modifications de l’intrus. Un `grep` paresseux ne résout donc pas l’enquête, alors qu’une lecture attentive de la chronologie, si.

## Ce que fait l’élève

1. Il lit le README que le lab dépose dans le dossier personnel : il nomme les éléments de preuve et ce qu’il faut trouver, sans donner aucune réponse.
2. Il parcourt `case/var/log/auth.log` et `case/var/log/nginx/access.log` pour distinguer l’attaque du bruit et trouver l’adresse et le compte.
3. Il détermine l’heure de la première connexion réussie depuis cette adresse.
4. Il examine les crontabs, les comptes, les clés et `case/mtimes.txt` pour trouver ce que l’intrus a laissé, dans quel fichier cela se trouve et quel programme il continue d’exécuter.
5. Il écrit les six lignes étiquetées dans `answers.txt`.

## Comment il est corrigé

Le lab comporte un **check**, un script qui corrige une VM rendue sans que l’enseignant ait à la lire : il lit `answers.txt` (les majuscules et les espaces n’ont pas d’importance) et compare chaque réponse au cas, en nommant **le premier champ erroné sans jamais dire quelle est la réponse**. Il refuse aussi un cas dont les fichiers de preuve ont été modifiés pour coller à une réponse. Le cas est le même pour tous les élèves, généré à partir d’une graine fixe au premier démarrage de la VM.

Un élève dispose de `sudo` sur sa propre VM : celui qui lit les fichiers de root peut donc trouver la clé. C’est accepté : le lab est un entraînement, et ce qui est noté, c’est la réponse écrite.

## Ce que contient le fichier

`incident-response.lab.yaml` est une **définition de lab VM** complète, exactement ce que la plateforme exécute :

| Clé | Ce que c’est |
| --- | --- |
| `cpus`, `memory`, `disk` | la taille de la VM de chaque élève : 1 CPU, 1024 Mo et 10 Go |
| `open` | les ports qui s’ouvrent dans le navigateur : aucun, le lab est le terminal |
| `setup` | un script exécuté une seule fois, en tant que root, au premier démarrage de la VM de chaque élève : il crée le cas, écrit le README et un `answers.txt` vide, et installe Python depuis l’archive d’Ubuntu uniquement si l’image ne l’a pas |
| `check` | le script de correction, exécuté en tant que root sur une copie du disque enregistré après un démarrage à neuf ; son code de sortie est le résultat et `0` signifie réussi |

## L’utiliser sur la plateforme

**Pour le donner comme travail à une classe :**

1. Dans votre module, ajoutez une tâche et choisissez la carte **Lab VM Ubuntu**.
2. Collez le contenu de `incident-response.lab.yaml`, ou appuyez sur l’exemple si votre plateforme le propose. Utiliser un exemple remplace le texte, et Annuler rend le vôtre.
3. Publiez et essayez d’abord votre propre copie. Chaque élève reçoit une VM à lui, conservée d’une session à l’autre.
4. Distribuez-la comme n’importe quelle tâche. Lorsqu’elle est rendue, lancez le check sur la ligne d’un élève dans la liste de la classe : il démarre une copie de sa machine enregistrée, qui n’est pas sauvegardée en retour, et montre si elle a réussi et ce qu’elle a affiché.

La [page de ce lab](https://mitsurutech.co.uk/fr/help/lab-incident-response) dans le manuel d’aide, son [guide des labs VM Ubuntu](https://mitsurutech.co.uk/fr/help/labs) et sa [page des labs d’exemple](https://mitsurutech.co.uk/fr/help/sample-labs) disent le reste. Pour créer votre propre cas, modifiez le générateur dans `setup` : la graine, les adresses, le compte et les fichiers qu’il écrit.

Pas encore de compte d’établissement ? [La première année est gratuite](https://mitsurutech.co.uk/fr/pricing) pour 50 élèves au plus, et [s’inscrire](https://mitsurutech.co.uk/fr/signup) ne demande qu’une adresse e-mail.

## Vérification du fichier

À chaque push, `.github/workflows/check.yml` vérifie que le fichier est du YAML valide avec les cinq clés ci-dessus, que `setup` et `check` tiennent chacun dans la limite de 16 Ko de la plateforme et que le script de correction est du Python valide. Il ne démarre pas de VM : cela se fait sur les machines de la plateforme.

## Ce qui ne marche pas dans un lab

La VM atteint l’archive de paquets d’Ubuntu, PyPI, npm et GitHub, et rien d’autre : on ne peut donc pas pointer un lab vers un hôte d’Internet. C’est une seule VM avec un jeu de journaux à lire : il n’y a pas d’attaquant en direct à observer ni de seconde machine à attaquer.

## À propos

Réalisé par [Mitsuru Technologies](https://mitsurutech.co.uk/fr) pour la [plateforme d’apprentissage MitsuruTech](https://mitsurutech.co.uk/fr/learning) : Python, Java, Go, Node, SQL et Linux dans le navigateur pour les écoles et les établissements, avec des labs de conteneurs, des labs VM, des applications web déployées depuis GitHub et des quiz qui se corrigent tout seuls. Essayez du code sans compte dans le [bac à sable](https://mitsurutech.co.uk/fr/playground).

D’autres labs du même endroit : [lab-netlab](https://github.com/mitsurutech/lab-netlab), [lab-juice-shop](https://github.com/mitsurutech/lab-juice-shop), [lab-dvwa](https://github.com/mitsurutech/lab-dvwa), [lab-webgoat](https://github.com/mitsurutech/lab-webgoat), [lab-mysql-phpmyadmin](https://github.com/mitsurutech/lab-mysql-phpmyadmin), [lab-git-gitea](https://github.com/mitsurutech/lab-git-gitea) et [lab-sample](https://github.com/mitsurutech/lab-sample).

## Licence

Cette œuvre est sous licence [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/). Vous pouvez la partager et l’adapter pour apprendre et enseigner, à condition de citer Mitsuru Technologies Ltd, de ne pas l’utiliser commercialement et de partager tout ce que vous en tirez sous la même licence. Le texte complet est dans le fichier `LICENSE`.

Toutes les adresses du cas appartiennent à une plage réservée à la documentation ou à une plage privée, et rien n’y est une donnée réelle.
