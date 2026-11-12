# Cours ERP — Partie fonctionnelle (Odoo 19 Community)

[⬅ Accueil du cours](../README.md)

Cette partie apprend à **utiliser** Odoo comme le ferait un client ou un consultant fonctionnel :
configurer les applications, suivre les flux métiers, et comprendre ce qui se passe d'une application à l'autre.

> Ces chapitres utilisent **Odoo 19 Community** (gratuite et open source).
> Quand une fonctionnalité n'existe que dans l'édition Enterprise, c'est indiqué par 🔒.

## Le fil rouge : Torréfaction Sablon

Tous les exercices suivent la même entreprise fictive :

> **Torréfaction Sablon** est un torréfacteur bruxellois. Il achète du café vert à des importateurs,
> le torréfie, et vend des paquets de café en grains et moulu :
> - à des **cafés, restaurants et hôtels**, que sa responsable commerciale prospecte (factures mensuelles) ;
> - sur son **site web** (boutique en ligne) ;
> - dans sa **boutique** du Sablon (caisse enregistreuse).
>
> L'entreprise compte 8 personnes : la gérante, deux torréfacteurs, deux vendeurs en boutique,
> une responsable commerciale, un magasinier et un comptable à temps partiel.

Chaque chapitre ajoute une brique. À la fin, vous aurez configuré tout le système d'information de l'entreprise.

## Chapitres

L'ordre suit le parcours d'un client : il est **prospecté** (CRM), **livré** (Stock), **facturé** (Facturation),
puis on vend aussi **en ligne** et **en boutique**. On termine par les **analyses**, qui exploitent tout le reste.

| # | Chapitre | Vous saurez… |
|---|---|---|
| 1 | [Odoo en général](01-odoo-general.md) | installer Odoo, naviguer, rechercher, filtrer, configurer la société et les utilisateurs |
| 2 | [CRM et ventes](02-crm.md) | suivre les prospects dans un pipeline, transformer une opportunité en devis signé en ligne |
| 3 | [Stock](03-stock.md) | gérer les produits, les réceptions, les livraisons, les emplacements et le réassort |
| 4 | [Facturation](04-facturation.md) | facturer, encaisser, gérer les avoirs, les factures fournisseurs et les taxes |
| 5 | [Site web](05-site-web.md) | créer et modifier les pages d'un site, gérer les menus et le référencement |
| 6 | [eCommerce](06-ecommerce.md) | publier des produits, configurer la boutique, suivre les commandes en ligne |
| 7 | [Point de vente](07-point-de-vente.md) | configurer une caisse, vendre en boutique, clôturer une session |
| 8 | [Ressources humaines](08-rh.md) | gérer les employés, les congés, le recrutement, les dépenses et les présences |
| 9 | [Tableaux de bord](09-tableaux-de-bord.md) | analyser avec les vues graphique et pivot, les favoris et les tableaux de bord |

Les [réponses commentées](reponses/) expliquent chaque manipulation pas à pas.

## Comment travailler

1. **Faites les manipulations dans votre propre base Odoo**, en même temps que vous lisez.
2. **Faites les exercices ✍️** au moment où vous les rencontrez, puis le **défi 🏁** en fin de chapitre.
3. **Les 🔍 enquêtes** vous mettent face à un problème réel d'utilisateur : trouvez l'explication.
4. **Comparez avec la réponse commentée** 👉 *après* avoir essayé.
5. **Les questions de compréhension** sont du même type que celles posées à l'examen.

## Les images

Les captures ont été faites sur une base de démonstration d'Odoo 19 Community, en français.
La société de démo est américaine (*My Company (San Francisco)*, montants en dollars).
Si vous créez votre base avec le pays **Belgique**, vous aurez des euros et la TVA belge : c'est normal
que vos écrans diffèrent légèrement.
