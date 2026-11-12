# Cours ERP — Odoo

HELB · Bachelier, dernière année

Ce dépôt contient tout le cours : la théorie, les exercices, les réponses commentées et les ressources.
Il a deux parties complémentaires :

| Partie | Vous apprenez à… | Version |
|---|---|---|
| **[Partie fonctionnelle](#partie-fonctionnelle)** | **utiliser** et configurer les applications d'Odoo : CRM, stock, facturation, site web, caisse, RH… | Odoo 19 Community |
| **[Partie technique](#partie-technique)** | **développer** un module Odoo : modèles, vues, sécurité, rapports… | Odoo 18 |

On commence par **comprendre ce qu'Odoo fait déjà** (partie fonctionnelle), avant de **développer ce qui manque** (partie technique).

## Le fil rouge du cours

> **Un client a un besoin. Comment le satisfaire avec le moins de code possible ?**

1. Est-ce que ça existe déjà en standard dans Odoo ? → **configuration** (partie fonctionnelle)
2. Sinon, est-ce qu'une action automatisée ou une action serveur suffit ? → **paramétrage**
3. Sinon, quel est le plus petit module qui fait le travail ? → **développement** (partie technique)

Un bon intégrateur Odoo ne code pas ce qui existe déjà. Avant d'écrire une ligne,
cherchez dans les applications standard.

## Comment utiliser ce dépôt

1. **Lisez le chapitre avant la séance.** Chaque chapitre commence par ses objectifs.
2. **Faites les exercices ✍️ au moment où vous les rencontrez.** Ils sont placés juste après
   la notion qu'ils mettent en pratique. Chaque chapitre se termine par un défi 🏁 plus complet.
3. **Bloqué ? Ouvrez les indices un par un** (les blocs 💡 repliables), pas tous d'un coup.
4. **Comparez avec la réponse commentée** 👉 *après* avoir essayé.
5. **Faites les 🐛 bugs à trouver** (partie technique) et les **🔍 enquêtes** (partie fonctionnelle) :
   ce sont des problèmes réels d'utilisateurs ou de développeurs.
6. **Répondez à l'encadré 📁 Dans votre projet** : il applique chaque chapitre à votre projet client.
7. **Répondez aux questions de compréhension** à la fin de chaque chapitre. Ce sont ce genre
   de questions qui vous seront posées en séance et à l'examen.

---

## Partie fonctionnelle

> **Odoo 19 Community.** Les fonctions réservées à l'édition Enterprise sont signalées par 🔒.

Tous les exercices suivent la même entreprise fictive, **Torréfaction Sablon**, un torréfacteur bruxellois
qui prospecte les restaurants, et vend son café sur son site web et dans sa boutique.
[Présentation complète du fil rouge](fonctionnel/README.md).

| # | Chapitre | Vous saurez… |
|---|---|---|
| 1 | [Odoo en général](fonctionnel/01-odoo-general.md) | installer Odoo, naviguer, rechercher, filtrer, configurer la société et les utilisateurs |
| 2 | [CRM et ventes](fonctionnel/02-crm.md) | suivre les prospects dans un pipeline, transformer une opportunité en devis signé en ligne |
| 3 | [Stock](fonctionnel/03-stock.md) | gérer les produits, les réceptions, les livraisons, les emplacements et le réassort |
| 4 | [Facturation](fonctionnel/04-facturation.md) | facturer, encaisser, gérer les avoirs, les factures fournisseurs et les taxes |
| 5 | [Site web](fonctionnel/05-site-web.md) | créer et modifier les pages d'un site, gérer les menus et le référencement |
| 6 | [eCommerce](fonctionnel/06-ecommerce.md) | publier des produits, configurer la boutique, suivre les commandes en ligne |
| 7 | [Point de vente](fonctionnel/07-point-de-vente.md) | configurer une caisse, vendre en boutique, clôturer une session |
| 8 | [Ressources humaines](fonctionnel/08-rh.md) | gérer les employés, les congés, le recrutement, les dépenses et les présences |
| 9 | [Tableaux de bord](fonctionnel/09-tableaux-de-bord.md) | analyser avec les vues graphique et pivot, les favoris et les tableaux de bord |

[Réponses commentées de la partie fonctionnelle](fonctionnel/reponses/)

---

## Partie technique

> **Odoo 18.** Les exercices construisent tous le même module, `openacademy`, qui s'enrichit de chapitre
> en chapitre. Si vous sautez un exercice, le suivant ne marchera pas.
> **Committez votre travail** à la fin de chaque exercice, avec un message clair.

| # | Chapitre | Vous saurez… |
|---|---|---|
| 1 | [Introduction](chapitres/01-introduction.md) | expliquer ce qu'est un ERP et naviguer dans Odoo |
| 2 | [Installation](chapitres/02-installation.md) | lancer Odoo sur votre machine |
| 3 | [Mon premier module](chapitres/03-premier-module.md) | créer et installer un module vide |
| 4 | [Modèles](chapitres/04-modeles.md) | créer des modèles, des champs et des relations |
| 5 | [Vues, actions et menus](chapitres/05-vues.md) | afficher vos données (liste, formulaire, kanban, recherche…) |
| 6 | [Méthodes et actions](chapitres/06-methodes-actions.md) | ajouter des boutons, utiliser l'ORM, des contraintes et des séquences |
| 7 | [Héritage](chapitres/07-heritage.md) | étendre un module existant sans le modifier |
| 8 | [Champs calculés et liés](chapitres/08-compute-related.md) | calculer des valeurs, créer des boutons intelligents et des actions planifiées |
| 9 | [Sécurité](chapitres/09-securite.md) | gérer les groupes, les droits et les règles d'accès |
| 10 | [Rapports PDF](chapitres/10-rapports-pdf.md) | générer un document imprimable |
| 11 | [Contrôleurs et site web](chapitres/11-controleurs-site-web.md) | exposer des pages web |
| 12 | [Wizards](chapitres/12-wizards.md) | créer des fenêtres d'assistant |

[Réponses commentées de la partie technique](reponses/)

### Ressources techniques

- [Aide-mémoire ORM](ressources/aide-memoire-orm.md) : l'essentiel sur une page
- [Commandes utiles](ressources/commandes-utiles.md) : lancer, mettre à jour, déboguer
- [Erreurs fréquentes](ressources/erreurs-frequentes.md) : « mon menu n'apparaît pas », etc.
- [Conventions de nommage](ressources/conventions.md) : les noms à utiliser dans `openacademy`

---

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

- [Documentation utilisateur Odoo 19](https://www.odoo.com/documentation/19.0/fr/applications.html)
- [Documentation développeur Odoo 18](https://www.odoo.com/documentation/18.0/fr/developer.html)
- [Référence ORM](https://www.odoo.com/documentation/18.0/fr/developer/reference/backend/orm.html)
- [Tutoriel officiel « Server Framework 101 »](https://www.odoo.com/documentation/18.0/fr/developer/tutorials/server_framework_101.html)
- [Base de démo Odoo](https://demo.odoo.com)
