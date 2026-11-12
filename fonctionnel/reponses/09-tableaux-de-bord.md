# Réponses — Chapitre 9 : Tableaux de bord et analyses

[⬅ Retour au chapitre](../09-tableaux-de-bord.md)

---

## Exercice 9.1

**Le graphique des ventes.**

**Ventes → Analyse → Ventes** :

1. - **Mesures** → décochez tout sauf **Montant HT** (*Total hors taxes*).
   - Icône **barres**.
   - Recherche → filtre **Date de la commande → année en cours** (retirez les filtres par défaut si besoin).
   - Recherche → **Regrouper par → Date de la commande → Mois**.
2. **Regrouper par → Vendeur** (en plus du mois). Chaque barre est découpée par vendeur ; icône **Empilé** pour les superposer.
3. Survolez la barre du mois dernier : l'infobulle donne le montant de chaque vendeur. Ou cliquez sur la légende
   pour masquer/afficher un vendeur.

### L'ordre des regroupements compte

| Regroupement | Lecture |
|---|---|
| Mois, **puis** vendeur | Pour chaque mois, la part de chaque vendeur |
| Vendeur, **puis** mois | Pour chaque vendeur, son évolution dans le temps |

Ce sont les mêmes données, mais pas la même question.

---

## Exercice 9.2

**Le pivot.**

**Ventes → Analyse → Ventes** → vue **Pivot** (icône tableau) :
1. Cliquez sur l'en-tête de ligne `Total` → **Client**.
2. Cliquez sur l'en-tête de colonne `Total` → **Catégorie de produit**.
3. **Mesures** → cochez **Montant HT** et **Quantité commandée**.
4. Bouton **Télécharger xlsx** (icône de téléchargement) → un fichier Excel avec exactement le même tableau.
5. Cliquez sur la cellule la plus élevée : Odoo ouvre la **liste des lignes de vente** qui composent ce montant.
   Un clic sur une ligne mène à la commande.

C'est le **« drill-down »** : partir d'un chiffre global et descendre jusqu'au document qui l'explique.
C'est ce qui rend le pivot d'Odoo plus utile qu'un export Excel figé.

---

## Exercice 9.3

**Encours clients.**

1. **Facturation → Clients → Factures** → filtre **Non payées** (et **Comptabilisé** si besoin pour exclure les brouillons) →
   **Regrouper par → Client** (ou *Partenaire*). Chaque groupe affiche le total dû.
   **Favoris → Enregistrer la recherche actuelle** → « Encours clients » → **Partager avec tous les utilisateurs** → **Enregistrer**.
2. Cochez la case de l'en-tête de la liste (tout sélectionner ; sur une liste regroupée, dépliez d'abord les groupes ou
   utilisez **Sélectionner tout**) → **Actions → Exporter** → ajoutez les champs *Client*, *Numéro*, *Date d'échéance*,
   *Montant dû* → **Exporter**.

> Dans l'écran d'export, **enregistrez le modèle d'export** (liste de champs) : la prochaine fois, un clic suffit.

---

## Exercice 9.4

**Lire les tableaux de bord.**

1. **Panier moyen** : l'indicateur **Commande moyenne** en haut du tableau Ventes, avec la variation (`↗` ou `↘`, en %)
   par rapport à la période précédente de même durée.
2. **Meilleurs clients** : le tableau **Meilleures commandes clients** / classement par client, trié par revenu.
3. **DMP** = **Délai Moyen de Paiement** (en anglais *DSO*, *Days Sales Outstanding*) : le nombre moyen de jours
   entre la facture et le paiement. Un DMP qui augmente signifie que les clients paient de plus en plus tard :
   l'entreprise a de l'argent « bloqué » chez ses clients, et risque des problèmes de trésorerie même si elle vend bien.

> Dans la base de démo, le DMP est très élevé (les factures de démo ne sont pas payées) : ce n'est pas réaliste,
> mais c'est un bon exemple d'indicateur qui doit déclencher une action (relancer les clients).

---

## Enquête

**« Les chiffres ne correspondent pas ! »**

Ce n'est pas une erreur : les deux analyses ne mesurent pas la même chose.

| | Ventes → Analyse | Facturation → Analyse des factures |
|---|---|---|
| Ce qui est compté | Les **commandes** confirmées | Les **factures** comptabilisées |
| Date utilisée | Date de **commande** | Date de **facture** |
| Commandes pas encore facturées | ✅ Comptées | ❌ Pas encore |
| Avoirs | ❌ Pas déduits | ✅ Déduits |
| Factures sans commande (exercice 4.3) | ❌ Absentes | ✅ Comptées |
| Ventes en boutique (Point de Vente) | Selon l'analyse (souvent séparées) | ✅ Si facturées / comptabilisées |

Exemple : une commande du 28 octobre livrée et facturée le 3 novembre est dans le CA **d'octobre** côté Ventes,
et de **novembre** côté Facturation.

**Pour la gérante :** le chiffre **comptable** (factures) est celui qui fait foi pour les comptes. Le chiffre des **ventes**
est un indicateur commercial, plus réactif. Il faut savoir lequel on regarde, et pourquoi.

---

## Exercice 9.5

**Le tableau de bord du lundi.**

| # | Où | Réglages | À quoi ça sert |
|---|---|---|---|
| 1 | **Ventes → Analyse → Ventes** (graphique) | Filtre *Date de la commande* : semaine dernière · Regrouper par **Équipe commerciale** (ou *Site web* / *Origine*) | Voir quel canal a porté les ventes de la semaine |
| 2 | **Facturation → Clients → Factures** | Filtres *Non payées* + **En retard** (*Échéance dépassée*) · Regrouper par client | Savoir qui relancer cette semaine |
| 3 | **Inventaire → Opérations → Approvisionnement → Réassort** | Filtre par défaut (à réapprovisionner) | Commander à temps |
| 4 | **Facturation → Analyse → Gestion → Analyse des factures** (graphique, lignes) | Année en cours · Regrouper par mois | Suivre la tendance |

Chacun enregistré en **favori partagé**.

### Pour le canal de vente (n° 1)

Il n'y a pas un champ « canal » unique dans Odoo. Selon la configuration :
- les commandes eCommerce ont un **Site web** renseigné et une équipe commerciale *Site web* ;
- les ventes en boutique sont dans le **Point de Vente** (analyse séparée) ;
- les restaurants passent par l'équipe commerciale de Karim.

Une bonne configuration des **équipes commerciales** dès le début du projet rend cette analyse triviale. Sinon, elle est
impossible : c'est pour ça qu'on pense au **reporting** dès l'analyse des besoins, pas à la fin.

---

[⬅ Retour au chapitre](../09-tableaux-de-bord.md)
