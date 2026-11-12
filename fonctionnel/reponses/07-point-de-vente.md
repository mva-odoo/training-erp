# Réponses — Chapitre 7 : Point de vente

[⬅ Retour au chapitre](../07-point-de-vente.md)

---

## Exercice 7.1

**La caisse de la boutique du Sablon.**

1. **Point de Vente → Configuration → Point de vente → Nouveau** : nom `Boutique Sablon` → **Enregistrer**.
2. **Configuration → Produits → Catégories PdV** (ou depuis la fiche produit, en tapant le nom) : `Cafés`, `Accessoires`.
3. Pour chaque produit, onglet **Point de Vente** : cochez **Disponible dans le PdV**, choisissez la **catégorie PdV**.
   Pour le sac kraft, mettez le **prix de vente** à 0,20 € (onglet Informations générales).
4. **Configuration → Paramètres** → en haut, choisissez **Boutique Sablon** → section *Paiement* → **Modes de paiement** :
   *Espèces* et *Carte* → **Enregistrer**.

> Par défaut, un point de vente propose **tous** les produits disponibles en caisse. Dans les paramètres, on peut limiter
> une caisse à certaines catégories (option *Restreindre les catégories*), utile si une même base gère une boutique et un bar.

---

## Exercice 7.2

**La première matinée.**

1. Tableau de bord → **Boutique Sablon → Ouvrir la caisse** → **Espèces à l'ouverture** : `150` → **Ouvrir la caisse**.
2. Vente 1 : cliquez 2 fois sur *Café Maison moulu*, 1 fois sur *Sac kraft* → **Paiement** → **Espèces** →
   tapez `20` sur le pavé → **Valider**.
   Total : 2 × 7,50 + 0,20 = **15,20 €**. Monnaie : 20 − 15,20 = **4,80 €** (affichée en vert, ligne *Monnaie*).
3. Vente 2 : **Nouvelle commande** → *Café Éthiopie* → **Paiement** → **Carte** → **Valider**.

> Avec l'**arrondi des espèces** activé (Paramètres), un total de 15,22 € serait arrondi à 15,20 € pour un paiement
> en espèces, comme les commerçants belges le font avec les pièces de 1 et 2 cents.

---

## Exercice 7.3

**Client, remise et facture.**

1. **Nouvelle commande** → **Client** → recherchez *Café du Sablon* → sélectionnez-le.
   Le nom du client apparaît sur le bouton. (Si le client a une **liste de prix** spécifique, elle est appliquée automatiquement.)
2. Cliquez 5 fois sur *Café Brésil* (ou 1 fois, puis **Qté** → `5`).
3. Ligne sélectionnée → **%** → `10`. La ligne affiche la remise et le total diminue.
4. **Paiement** → **Facture** (activé) → **Carte** → **Valider**.
   Odoo crée la facture et propose de l'imprimer (PDF).

Total : 5 × 24,00 € = 120 €, − 10 % = **108 € TVAC**.

> Pourquoi un client est obligatoire pour la facture : une facture doit être **nominative** (nom, adresse, numéro de TVA
> du client). Un ticket de caisse anonyme ne suffit pas pour la comptabilité d'une entreprise.

---

## Exercice 7.4

**Rembourser.**

Écran de caisse → **Commandes** (en haut à gauche) → filtre sur les commandes payées → sélectionnez la vente 1 →
sélectionnez la ligne *Café Maison moulu*, quantité **1** → **Rembourser**.
Une nouvelle commande apparaît avec **−1 × Café Maison moulu** (−7,50 €) → **Paiement** → **Espèces** → **Valider**.

Le tiroir contient 7,50 € de moins, le produit **revient en stock** à la fermeture de la session, et la commande de
remboursement garde le lien avec la vente d'origine.

---

## Exercice 7.5

**Fermer la caisse.**

Menu **≡** → **Fermer la caisse**. La fenêtre de clôture affiche, par moyen de paiement, les montants attendus.
Pour les espèces : 150 (fond) + 15,20 (vente 1) − 7,50 (remboursement) = **157,70 €** attendus. Saisissez **155,70 €**
comptés → la **différence** de −2,00 € s'affiche → ajoutez une note → **Fermer la session**.

1. **La différence** est enregistrée en comptabilité sur un compte de **profits / pertes de caisse** (défini sur le journal
   des espèces). Elle est visible dans le rapport de session. Odoo ne la « corrige » pas : elle est **tracée**, pour que
   le responsable puisse en discuter avec le vendeur.
2. **Le stock** : **Inventaire → Vue d'ensemble** → carte **Commandes du PdV**, ou l'historique des mouvements du produit :
   des mouvements `WH/Stock → Clients` avec la référence de la session.
3. **La facture** : **Facturation → Clients → Factures** (client *Café du Sablon*), ou **Point de Vente → Commandes →
   Commandes**, ouvrir la commande → bouton intelligent **Facture**.

---

## Enquête

**« Le nouveau café n'est pas dans la caisse ! »**

| Cause | Vérification |
|---|---|
| **Disponible dans le PdV** n'est pas coché | Fiche produit, onglet **Point de Vente** |
| La caisse est **limitée à certaines catégories**, et le produit n'en fait pas partie | Paramètres du PdV → *Restreindre les catégories* |
| La caisse a été ouverte **avant** la création du produit | Les produits sont chargés à l'ouverture. **Rechargez la page** de la caisse (les ventes en cours ne sont pas perdues) |
| **Peut être vendu** n'est pas coché | En haut du formulaire produit |
| Le produit n'a **pas de prix** ou est **archivé** | Fiche produit |

La troisième cause est la plus fréquente en boutique : la caisse charge les données une fois, pour pouvoir fonctionner
hors ligne.

---

## Exercice 7.6

**Une journée complète.**

Après avoir fermé la session :

1. **Chiffre par moyen de paiement** : **Point de Vente → Commandes → Sessions** → ouvrez la session → résumé par mode de
   paiement. Ou **Analyse → Commandes** (pivot) → filtre sur la date ou la session → regrouper par **mode de paiement**
   (via **Commandes → Paiements** regroupé par mode de paiement).
2. **Meilleur produit** : **Analyse → Commandes** (vue pivot ou graphique) → regrouper par **Produit** →
   mesure **Quantité** (ou *Total*). Triez.
3. **Le stock en ligne** : **oui**. La boutique physique et le site web vendent depuis **le même entrepôt** (`WH/Stock`),
   donc la même quantité disponible. Une vente en caisse diminue le stock affiché sur le site, et inversement.

> C'est l'argument principal de l'omnicanal intégré : un seul stock, un seul catalogue, un seul fichier clients,
> quel que soit le canal de vente. Avec deux logiciels séparés (une caisse et un site), on vendrait en ligne des produits
> déjà vendus en boutique.

---

[⬅ Retour au chapitre](../07-point-de-vente.md)
