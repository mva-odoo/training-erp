# Réponses — Chapitre 1 : Odoo en général

[⬅ Retour au chapitre](../01-odoo-general.md)

---

## Exercice 1.1

**Installer les applications.**

**Apps** → rechercher chaque application → **Activer**. Installez-les une par une : chaque installation prend
quelques dizaines de secondes.

### 1. Applications supplémentaires

Vous verrez apparaître notamment **Discussion**, **Contacts**, **Calendrier** (et parfois **Tableaux de bord**).
Ce sont des **dépendances** : chaque application déclare les modules dont elle a besoin.
- Ventes a besoin de Facturation (on vend pour facturer) et de Contacts (on vend à quelqu'un).
- eCommerce a besoin de Site Web et de Ventes.
- Point de Vente a besoin d'Inventaire et de Facturation.

C'est le principe de l'**ERP intégré** : les applications s'appuient les unes sur les autres.

### 2. « Mettre à niveau »

Les applications avec le bouton **Mettre à niveau** (Studio, Comptabilité complète, Paie, Signature…)
font partie de l'**édition Enterprise** 🔒. Le bouton propose de passer à cette édition payante.
En Community, on ne peut pas les installer.

> Remarquez que **Facturation** est en Community, mais **Comptabilité** (rapprochement bancaire avancé,
> rapports légaux complets) est en Enterprise. C'est une question fréquente des clients.

---

## Exercice 1.2

**Chercher l'information.**

**Ventes → Commandes → Commandes**.

### 1. Commandes de *Gemini Furniture*

Tapez `Gemini` dans la barre de recherche → choisissez **Rechercher Client pour : Gemini**.
Le compteur en haut à droite (`1-19 / 19`, par exemple) donne le nombre de commandes.

### 2. Le meilleur vendeur

Retirez le filtre client. Flèche de la recherche → **Regrouper par → Vendeur**.
Chaque groupe affiche le **total** dans la colonne Total. Cliquez sur l'en-tête **Total** pour trier
les groupes du plus grand au plus petit.

### 3. Les gros devis non confirmés

**Ventes → Commandes → Devis** (cette action filtre déjà les devis).
Flèche → **Ajouter un filtre personnalisé** → règle `Total` `>` `1000` → **Confirmer**.
Puis **Favoris → Enregistrer la recherche actuelle** → nom « Gros devis » → **Enregistrer**.

Le favori réapplique les filtres en un clic. Cochez **Partager avec tous les utilisateurs** pour le rendre
visible par toute l'équipe (c'est le défi 1.5).

---

## Exercice 1.3

**Travailler en équipe.**

1. **Note** : bouton **Note** dans le chatter → tapez `@` puis le début du nom d'un collègue →
   sélectionnez-le → **Envoyer**. Le collègue reçoit une notification dans **Discussion**.
   Le client, lui, ne voit rien : c'est une note **interne**.
2. **Activité** : bouton **Activité** → type **Appel** → échéance demain → assigné à vous → **Enregistrer**.
   Elle apparaît en haut du chatter (« Activités planifiées »).
3. Icône 🕒 en haut à droite → l'activité est listée sous « Ventes » → cliquez dessus pour ouvrir la commande →
   dans le chatter, **✓ Terminé** sur l'activité. Elle disparaît de la liste et laisse une trace dans l'historique.

**À retenir :** les activités sont la « to-do list » partagée d'Odoo. Un commercial qui n'utilise pas les activités
oublie ses relances.

---

## Exercice 1.4

**La société Torréfaction Sablon.**

### 1. La société

**Paramètres → Paramètres généraux** → section **Sociétés** → **Mettre à jour les informations** :
nom, adresse, pays Belgique, numéro de TVA → **Enregistrer**. Ajoutez un logo si vous en avez un.

Pour vérifier : **Ventes → Nouveau** → un client, un produit → **Imprimer** : l'en-tête reprend ces informations.

### 2. Les utilisateurs

**Paramètres → Utilisateurs & Sociétés → Utilisateurs → Nouveau**. Nom, e-mail (sert d'identifiant), puis l'onglet **Droits d'accès** :

| Utilisateur | Ventes | Inventaire | Facturation | Autres |
|---|---|---|---|---|
| Sophie (gérante) | Administrateur | Administrateur | Administrateur | **Paramètres : Administrateur** |
| Karim (commercial) | Tous les documents | Utilisateur | *(vide)* | — |
| Lucas (magasinier) | *(vide)* | Utilisateur | *(vide)* | — |
| Inès (comptable) | Tous les documents *(ou lecture)* | *(vide)* | Facturation : Administrateur | — |

> En Community, il n'y a pas de rôle « lecture seule » sur les ventes : Inès aura le rôle « Utilisateur : tous les documents ».
> C'est un compromis à expliquer au client.

Pour tester un utilisateur sans connaître son mot de passe : sur sa fiche, **Action → Changer le mot de passe**,
puis connectez-vous avec son identifiant dans une fenêtre de navigation privée.

---

## Enquête

**« Je ne vois pas l'application ! »**

Dans l'ordre :

1. **Les droits de l'utilisatrice** : sur la fiche d'Inès, onglet **Droits d'accès**, le rôle de l'application Ventes
   est-il vide ? C'est la cause dans 9 cas sur 10. Une application sans rôle est **invisible**.
2. **Le rafraîchissement** : les droits sont lus à la connexion. Demandez-lui de **rafraîchir** la page (ou de se reconnecter).
3. **La société** : dans une base multi-société, travaille-t-elle dans la bonne société (sélecteur en haut à droite) ?

**Moralité :** installer une application ne la rend pas visible à tout le monde. Chaque utilisateur doit recevoir un rôle.

---

## Exercice 1.5

**Favori partagé.**

**Ventes → Commandes → Commandes** :
1. Flèche de recherche → filtre **Date de la commande** → choisissez le **mois en cours**.
2. **Regrouper par → Vendeur**.
3. **Favoris → Enregistrer la recherche actuelle** → nom « Commandes du mois par vendeur » →
   cochez **Partager avec tous les utilisateurs** → **Enregistrer**.

**Pourquoi « mois en cours » et pas « novembre » ?** Le filtre de période d'Odoo est **relatif** :
le favori affichera toujours le mois courant, sans devoir le refaire chaque mois.

---

[⬅ Retour au chapitre](../01-odoo-general.md)
