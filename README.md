# Cours ERP — Odoo

HELB · Bachelier, dernière année

Ce dépôt contient tout le cours : la théorie, les exercices et les ressources.
Lisez-le dans l'ordre des chapitres, un chapitre après l'autre.

## Comment utiliser ce dépôt

1. **Lisez le chapitre avant la séance.** Chaque chapitre commence par ses objectifs.
2. **Faites les exercices ✍️ au moment où vous les rencontrez.** Ils sont placés juste après
   la notion qu'ils mettent en pratique. Chaque chapitre se termine par un défi 🏁 plus complet.
3. **Les exercices construisent tous le même module**, `openacademy`, qui s'enrichit de chapitre
   en chapitre. Si vous sautez un exercice, le suivant ne marchera pas.
4. **Bloqué ? Ouvrez les indices un par un** (les blocs 💡 repliables), pas tous d'un coup.
5. **Comparez avec la réponse commentée** 👉 *après* avoir essayé. Chaque ligne y est expliquée.
6. **Répondez aux questions de compréhension** à la fin de chaque chapitre. Ce sont ce genre
   de questions qui vous seront posées en séance et à l'examen.
7. **Committez votre travail** à la fin de chaque exercice, avec un message clair.

> Le cours utilise **Odoo 18**.

## Chapitres

| # | Chapitre | Vous saurez… |
|---|---|---|
| 1 | [Introduction](chapitres/01-introduction.md) | expliquer ce qu'est un ERP et naviguer dans Odoo |
| 2 | [Installation](chapitres/02-installation.md) | lancer Odoo sur votre machine |
| 3 | [Mon premier module](chapitres/03-premier-module.md) | créer et installer un module vide |
| 4 | [Modèles](chapitres/04-modeles.md) | créer des modèles, des champs et des relations |
| 5 | [Vues, actions et menus](chapitres/05-vues.md) | afficher vos données (liste, formulaire, recherche…) |
| 6 | [Méthodes et actions](chapitres/06-methodes-actions.md) | ajouter des boutons, utiliser l'ORM, ajouter des contraintes |
| 7 | [Héritage](chapitres/07-heritage.md) | étendre un module existant sans le modifier |
| 8 | [Champs calculés et liés](chapitres/08-compute-related.md) | calculer des valeurs automatiquement |
| 9 | [Sécurité](chapitres/09-securite.md) | gérer les groupes, les droits et les règles d'accès |
| 10 | [Rapports PDF](chapitres/10-rapports-pdf.md) | générer un document imprimable |
| 11 | [Contrôleurs et site web](chapitres/11-controleurs-site-web.md) | exposer des pages web |
| 12 | [Wizards](chapitres/12-wizards.md) | créer des fenêtres d'assistant |

## Partie fonctionnelle

La [partie fonctionnelle](fonctionnel/README.md) (Odoo 19 Community) apprend à **utiliser** les applications :
stock, facturation, site web, eCommerce, tableaux de bord, point de vente et ressources humaines.

## Réponses

Les [réponses commentées](reponses/) de chaque chapitre expliquent entièrement le code :
pourquoi chaque ligne, quelles alternatives, quels pièges.

## Ressources

- [Aide-mémoire ORM](ressources/aide-memoire-orm.md) : l'essentiel sur une page
- [Commandes utiles](ressources/commandes-utiles.md) : lancer, mettre à jour, déboguer
- [Erreurs fréquentes](ressources/erreurs-frequentes.md) : « mon menu n'apparaît pas », etc.
- [Conventions de nommage](ressources/conventions.md) : les noms à utiliser dans `openacademy`

## Le fil rouge du cours

> **Un client a un besoin. Comment le satisfaire avec le moins de code possible ?**

1. Est-ce que ça existe déjà en standard dans Odoo ? → **configuration**
2. Sinon, est-ce que Studio ou une action automatisée suffit ? → **paramétrage**
3. Sinon, quel est le plus petit module qui fait le travail ? → **développement**

Un bon intégrateur Odoo ne code pas ce qui existe déjà. Avant d'écrire une ligne,
cherchez dans les modules standard.

## Évaluation

| Moment | Contenu |
|---|---|
| 1ʳᵉ rencontre client | Échange avec le client pour analyser les besoins et cadrer le projet |
| 2ᵉ rencontre client | Démonstration de la solution au client, validation des fonctionnalités |
| Examen final | Mise en production : livraison, mise en service et déploiement de la solution |

## Intelligence artificielle

**L'IA est autorisée** (ChatGPT, Copilot, Claude…) pour coder, chercher et débloquer un problème.

**Mais vous êtes responsables de chaque ligne.** Vous devez pouvoir expliquer chaque partie
de votre code et de votre implémentation. Tout code que vous ne savez pas justifier
est considéré comme non maîtrisé et sera pénalisé.

Utilisez l'IA comme un accélérateur, pas comme un substitut à votre réflexion.

## Documentation officielle

- [Documentation développeur Odoo](https://www.odoo.com/documentation/18.0/fr/developer.html)
- [Référence ORM](https://www.odoo.com/documentation/18.0/fr/developer/reference/backend/orm.html)
- [Tutoriel officiel « Server Framework 101 »](https://www.odoo.com/documentation/18.0/fr/developer/tutorials/server_framework_101.html)
- [Base de démo Odoo](https://demo.odoo.com)
