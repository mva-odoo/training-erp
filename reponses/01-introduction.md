# Réponses — Chapitre 1

[⬅ Retour au chapitre](../chapitres/01-introduction.md)

> Essayez **vraiment** l'exercice avant de lire la réponse. Lire une solution donne l'impression
> d'avoir compris ; la refaire seul est la seule façon de le savoir.

---

## Exercice 1.1

**Suivre un flux de vente complet.**

### Le déroulé

| Étape | Application | Document | Ce qui se passe en coulisse |
|---|---|---|---|
| 1. Créer le devis | Ventes | Devis (`sale.order`, état *Devis*) | Rien d'autre : un devis n'engage encore personne. |
| 2. Confirmer | Ventes | Bon de commande (`sale.order`, état *Bon de commande*) | Odoo crée **automatiquement** un bon de livraison dans Inventaire. Le bouton intelligent **Livraison** apparaît. |
| 3. Valider la livraison | Inventaire | Bon de livraison (`stock.picking`) | Le stock des produits diminue. Sur la commande, la colonne « Livré » se remplit. |
| 4. Créer la facture | Ventes → Comptabilité | Facture client (`account.move`) | Odoo reprend les lignes de la commande. À la confirmation, il génère les écritures comptables (client, chiffre d'affaires, TVA). |
| 5. Enregistrer le paiement | Comptabilité | Paiement (`account.payment`) | La facture passe à « Payé » (ou « En paiement » tant que le relevé bancaire n'est pas rapproché). |

### Ce qu'il faut retenir

- **Un seul document de départ** (le devis) a déclenché des actions dans **trois applications** : Ventes, Inventaire, Comptabilité.
- Personne n'a ressaisi le client, les produits ni les quantités. C'est exactement ce que signifie « intégré ».
- Les **boutons intelligents** (en haut à droite des formulaires) matérialisent les liens entre les documents.
  Vous les retrouverez partout dans Odoo, et vous apprendrez à en créer.

### Pourquoi la livraison n'apparaît pas toujours

Un produit de type **service** (ou non suivi en stock) ne génère pas de livraison. C'est logique :
on ne livre pas une heure de consultance.

### Pourquoi la facture n'est pas toujours possible tout de suite

La politique de facturation d'un produit est soit **quantités commandées** (on peut facturer dès la confirmation),
soit **quantités livrées** (on ne facture que ce qui est parti). C'est un choix de **configuration**, pas de code.

---

## Exercice 1.2

**Découvrir un module inconnu.** Exemple avec l'application **Projet** :

1. **Besoin :** organiser le travail d'une équipe en projets et en tâches, suivre l'avancement et le temps passé.
2. **Objets principaux :**
   - le **projet** (`project.project`) : un client, une équipe, des étapes ;
   - la **tâche** (`project.task`) : assignée à quelqu'un, avec une échéance, déplacée d'étape en étape (vue kanban) ;
   - la **feuille de temps** (`account.analytic.line`, application Feuilles de temps) : les heures passées sur une tâche.
3. **Lien avec une autre application :** un produit de type service configuré avec « Créer une tâche » génère
   automatiquement une tâche dans un projet quand on confirme une commande de vente. Les heures pointées
   peuvent ensuite être refacturées au client.

Ce qu'on attend dans votre présentation : le **besoin métier** d'abord, les écrans ensuite.
« Ce module sert à… pour quelqu'un qui… », pas « il y a un menu ici et un bouton là ».

---

## Exercice 1.3

**Conseiller un client.**

1. **Boulangerie → Odoo Online (SaaS).**
   Pas de besoin spécifique, pas d'informaticien : il faut que ça marche sans maintenance.
   Les mises à jour et les sauvegardes sont incluses. Si un besoin particulier apparaît, Studio suffit souvent.

2. **PME avec modules sur mesure, sans équipe serveur → Odoo.sh.**
   Odoo Online n'accepte pas de modules Python personnalisés. On-Premise les accepterait, mais il faudrait
   gérer serveur, sauvegardes et mises à jour. Odoo.sh accepte le code (via Git) **et** gère l'infrastructure.

3. **Administration avec données sur ses serveurs → On-Premise.**
   C'est la seule option où les données ne quittent pas l'infrastructure du client. En contrepartie, le client
   (ou son intégrateur) doit gérer l'installation, la sécurité, les sauvegardes et les mises à jour.

Remarquez que la question de départ n'était pas technique : on part **toujours** des contraintes du client.

---

[⬅ Retour au chapitre](../chapitres/01-introduction.md)
