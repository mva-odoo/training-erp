# Réponses — Chapitre 4 : Facturation

[⬅ Retour au chapitre](../04-facturation.md)

---

## Exercice 4.1

**Configurer Torréfaction Sablon.**

### 1. Les taxes des produits

Ouvrez chaque café (**Facturation → Clients → Produits**, ou depuis l'Inventaire) → onglet **Informations générales** →
**Taxes de vente** : retirez la taxe proposée par défaut (souvent 21 %) et choisissez **6 %**.
Pour l'atelier dégustation, gardez **21 %**.

> Astuce : depuis la **liste** des produits, sélectionnez les trois cafés, puis modifiez la colonne *Taxes de vente*
> d'une ligne : Odoo propose d'appliquer la modification à toute la sélection.

### 2. La condition de paiement

**Configuration → Facturation → Conditions de paiement → Nouveau** :
- Nom : `30 jours fin de mois` ;
- ligne d'échéance : **100 %**, **30 jours** après la date de facture, **fin du mois** (option *Fin du mois* / *à la fin du mois*).

L'aperçu en bas du formulaire montre la date d'échéance calculée pour un exemple : vérifiez-la.

### 3. Par défaut pour le client

Fiche **Café du Sablon** → onglet **Ventes & Achats** → **Conditions de paiement** (section Ventes) : `30 jours fin de mois`.

Toute nouvelle commande et toute nouvelle facture pour ce client reprendra cette condition. On ne la saisit plus à la main,
donc on ne l'oublie plus.

---

## Exercice 4.2

**Facturer le Café du Sablon.**

### 1. Ce que propose Odoo

Cliquez sur **Créer une facture** → choisissez **Facture normale** → **Créer une facture**.

Si la politique des cafés est **Quantités livrées** (valeur par défaut pour les produits stockables), Odoo ne facture
que ce qui a été **livré**. Si vous avez livré 10 Brésil et 10 Éthiopie (le reste en reliquat), la facture contient
10 + 10, pas 10 + 15.

Les 5 Éthiopie restants seront facturés quand ils seront livrés : la commande reste **À facturer**.

### 2. Confirmer et envoyer

Dans la facture brouillon → **Confirmer** : elle reçoit son numéro `FAC/2026/000xx` et passe à l'état **Comptabilisé**.
Puis **Envoyer** → l'e-mail est pré-rempli avec le PDF de la facture en pièce jointe → **Envoyer**.

L'envoi est noté dans le chatter de la facture. C'est une preuve utile en cas de litige (« je n'ai jamais reçu la facture »).

---

## Exercice 4.3

**L'atelier dégustation.**

**Clients → Factures → Nouveau** :
- **Client** : *Le Petit Sablon* (créez-le) ;
- **Ajouter une ligne** : produit *Atelier dégustation*, quantité **8** → le prix (35 €) et la TVA 21 % sont repris de la fiche produit ;
- **Confirmer**.

Total : 8 × 35 € = 280 € HTVA, + 21 % = **338,80 € TVAC**.

> Quand utiliser une facture **directe** plutôt qu'une commande ? Quand il n'y a ni stock ni livraison à suivre,
> et pas besoin d'un devis à faire accepter. Pour tout le reste, passer par une commande garde la traçabilité.

---

## Exercice 4.4

**Paiement en deux fois.**

1. Sur la facture → **Payer** → journal **Banque**, **Montant : 100** → **Créer un paiement**.
   L'état de paiement passe à **Partiellement payé**. En bas de la facture, le paiement apparaît et le **Montant dû** diminue.
2. Deux semaines plus tard → **Payer** à nouveau : le montant proposé est le **solde restant** → **Créer un paiement**.
   L'état passe à **Payé**.

### « Payé » ou « En paiement » ?

Selon la configuration, un paiement peut d'abord être enregistré sur un compte d'attente (« paiements en cours »).
La facture est alors **En paiement** jusqu'à ce que le paiement soit **rapproché** avec la ligne du relevé bancaire.
C'est une sécurité : on considère la facture réellement payée seulement quand l'argent est sur le compte.

---

## Exercice 4.5

**Avoir partiel.**

Sur la facture du Café du Sablon → **Avoir** → indiquez une **raison** (« 2 sacs abîmés ») → **Inverser**
(le libellé exact peut varier). Odoo crée un **avoir en brouillon**, copie de toute la facture.

Dans l'avoir brouillon :
- supprimez la ligne Café Éthiopie ;
- sur la ligne Café Brésil, mettez la quantité à **2** ;
- **Confirmer**.

L'avoir est automatiquement **lettré** (rapproché) avec la facture d'origine si celle-ci est encore impayée :
le montant dû par le client diminue. Si la facture était déjà payée, l'avoir reste un **crédit** pour le client,
à rembourser ou à déduire d'une prochaine facture.

> Et le stock ? L'avoir est un document **comptable**. Pour que les 2 sacs reviennent en stock (ou partent au rebut),
> il faut aussi un **retour** sur la livraison (bouton **Retour** sur le bon de livraison validé).

---

## Exercice 4.6

**Facture fournisseur.**

**Achats** → commande à Café Import Antwerpen → bouton **Créer la facture**.

### 1. Combien de Café Éthiopie ?

Odoo propose **20**, pas 30 : la politique de contrôle des achats est par défaut **« Quantités reçues »**.
On ne paie que ce qui a été réceptionné (exercice 3.2 : 20 reçus, 10 en reliquat).

### 2. La facture papier mentionne 30

**Ne la confirmez pas telle quelle.** Deux possibilités :
- le reliquat est arrivé entre-temps mais n'a pas été validé dans Odoo → demandez au magasinier de valider la réception,
  puis facturez ;
- le fournisseur facture 10 sacs **non livrés** → on contacte le fournisseur, qui doit corriger sa facture.

Une fois les quantités justes : **Confirmer**, puis **Payer** (journal Banque).

**C'est exactement le rôle du rapprochement à trois voies :** l'écart entre commande, réception et facture saute aux yeux.

---

## Enquête

**« Je ne peux plus modifier la facture ! »**

Une facture **comptabilisée** est verrouillée : c'est un document légal, numéroté, qui a généré des écritures.

Deux solutions, selon la situation :

| Situation | Solution |
|---|---|
| La facture n'a **pas encore été envoyée** au client et la période n'est pas clôturée | Bouton **Remettre en brouillon**, corriger, **Confirmer** à nouveau. Elle garde son numéro. |
| La facture a **déjà été envoyée** | **Avoir** complet, puis nouvelle facture correcte |

**Mais attention :** l'adresse vient de la **fiche du client**. Il faut d'abord corriger la fiche contact, sinon
toutes les prochaines factures auront la même erreur.

Le chatter de la facture garde la trace de la remise en brouillon et de qui l'a faite.

---

## Exercice 4.7

**La facturation mensuelle.**

1. Trois commandes pour *Le Petit Sablon*, confirmées, livraisons validées.
2. **Ventes → À facturer → Commandes à facturer** → filtrez le client → **sélectionnez les trois commandes** (cases à gauche) →
   bouton **Créer des factures** (ou menu **Actions**) → **Facture normale**.
   Par défaut, Odoo regroupe les commandes **d'un même client** dans **une seule** facture.
   Vérifiez que la facture contient bien les lignes des trois commandes, chacune avec sa référence de commande.
3. Le réglage : **conditions de paiement** par défaut sur la fiche du client (onglet **Ventes & Achats**),
   comme à l'exercice 4.1.

> En pratique, on fait cette opération chaque fin de mois pour tous les clients à facturation mensuelle :
> filtre « À facturer », regroupement par client, sélection de tout, **Créer des factures**.

---

[⬅ Retour au chapitre](../04-facturation.md)
