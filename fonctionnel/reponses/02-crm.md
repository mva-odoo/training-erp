# Réponses — Chapitre 2 : CRM et ventes

[⬅ Retour au chapitre](../02-crm.md)

---

## Exercice 2.1

**Le pipeline de Torréfaction Sablon.**

### 1. L'équipe commerciale

**CRM → Configuration → Équipes commerciales → Nouveau** : nom `Restaurants`, **Chef d'équipe** : Karim,
**Membres** : Karim (et d'autres vendeurs si besoin) → **Enregistrer**.

> Si le menu *Équipes commerciales* n'apparaît pas : **CRM → Configuration → Paramètres** → cochez **Équipes multiples**.

### 2. Les étapes

**CRM → Configuration → Pipeline → Étapes** :
- renommez ou créez les étapes pour obtenir *Nouveau, Qualifié, Dégustation planifiée, Proposition, Gagné* ;
- réordonnez-les en glissant la poignée à gauche ;
- l'étape *Gagné* doit avoir la case **Est l'étape gagnée ?** cochée.

Variante plus rapide : dans le pipeline, colonne **+ Étape** à droite, et renommer une colonne par son menu (⚙ en haut de colonne).

Une étape peut être **commune à toutes les équipes**, ou réservée à une équipe (champ *Équipes commerciales* de l'étape).

### 3. Les opportunités

**CRM → Ventes → Mon pipeline** → choisissez le pipeline de l'équipe *Restaurants* (filtre ou **CRM → Ventes → Équipes**) →
**+** en haut de la colonne *Nouveau* → nom de l'opportunité, client (créé à la volée), **revenu attendu** → **Ajouter**.

Le total en haut de la colonne : 6 000 + 12 000 + 2 500 = **20 500 €**.

---

## Exercice 2.2

**Un contact au salon.**

1. **CRM → Configuration → Paramètres** → cochez **Pistes** → **Enregistrer**. Un menu **CRM → Pistes** apparaît.
2. **CRM → Pistes → Nouveau** :
   - nom de la piste : `Restaurant La Truffe Noire` ;
   - **Nom de la société** : *Restaurant La Truffe Noire*, **Nom du contact** : *Marc Lemaire*, **E-mail** ;
   - onglet **Notes** : le contexte de la rencontre ;
   - (optionnel) étiquette *Horeca Expo*, pour mesurer plus tard ce que le salon a rapporté.
3. **Convertir en opportunité** :
   - **Action de conversion** : *Convertir en opportunité* ;
   - **Vendeur** : Karim, **Équipe** : Restaurants ;
   - **Client** : *Créer un nouveau client* (Odoo propose de **lier** à un client existant si l'e-mail ou le nom correspondent) ;
   - **Créer une opportunité**.

### Pourquoi une piste n'a pas encore de client

Une piste contient des **informations de contact brutes** (nom de société, e-mail…) **sans** créer de fiche contact.
On évite ainsi de remplir le carnet d'adresses avec des centaines de contacts de salon jamais qualifiés.
Le contact n'est créé qu'à la conversion, quand l'intérêt est réel.

---

## Exercice 2.3

**Le suivi.**

1. Dans l'opportunité → chatter → **Activité** → type **Appel**, échéance demain, assignée à Karim → **Enregistrer**.
2. L'activité apparaît en haut du chatter → **✓ Terminé** → la fenêtre propose d'ajouter un **compte rendu** →
   tapez le compte rendu → **Terminé et planifier la suivante** → type **Réunion**, résumé « Dégustation », dans une semaine.
   Le compte rendu reste dans l'historique de l'opportunité.
3. Dans la barre d'état en haut du formulaire, cliquez sur **Dégustation planifiée** (ou glissez la carte dans le kanban).

> Une activité de type **Réunion** propose d'ouvrir le **Calendrier** pour fixer l'heure et inviter le client.
> L'événement est alors lié à l'opportunité.

---

## Exercice 2.4

**Une opportunité perdue.**

1. **CRM → Configuration → Pipeline → Motifs de perte → Nouveau** : `Concurrent sous contrat`.
   Puis, dans l'opportunité *Café Le Cercle* → bouton **Perdu** → motif *Concurrent sous contrat* → note « contrat de 3 ans,
   à recontacter dans 3 ans » → **Marquer comme perdu**.
2. Elle a disparu du pipeline : elle est **archivée**. Pour la retrouver : recherche → filtre **Perdu** (ou **Archivé**).
   Elle porte un bandeau rouge « Perdu ». Le bouton **Restaurer** la remet dans le pipeline si le client revient.

> Astuce commerciale : avant de la marquer perdue, planifiez une **activité dans 3 ans** (« recontacter à la fin du contrat »).
> Une opportunité perdue aujourd'hui peut être gagnée demain.

---

## Exercice 2.5

**La proposition.**

### 1. Le devis

Dans l'opportunité → **Nouveau devis**. Le devis s'ouvre avec le client déjà rempli.
- **Ajouter un produit** : *Café Éthiopie*, quantité 8 ; *Café Brésil*, quantité 4.
- Remise de 5 % : il faut d'abord activer **Ventes → Configuration → Paramètres → Remises**. Une colonne **Rem. %** apparaît
  sur les lignes : tapez `5` sur la ligne Éthiopie.
- **Expiration** : date du jour + 15 jours.
- **Enregistrer**.

### 2. Envoyer et signer

- **Envoyer** → l'e-mail contient le PDF et un lien → **Envoyer**. Le devis passe à l'état **Devis envoyé**.
- **Aperçu** → la page client s'ouvre (bandeau « Ceci est un aperçu du portail client »).
  **Signer & Payer** (ou **Accepter & Signer**) → nom, signature dessinée → **Accepter & Signer**.
  (Si le paiement en ligne est activé, le client est invité à payer ; sinon la signature suffit.)

> La signature en ligne doit être activée dans **Ventes → Configuration → Paramètres → Signature en ligne**.

### 3. Ce qui se passe

- Le **devis** signé est **confirmé** automatiquement : il devient une **commande client** (`S000xx`).
  La signature est jointe au document et notée dans le chatter.
- L'**opportunité** garde un bouton intelligent vers ses devis et commandes. Vérifiez son état : si elle n'est pas
  passée automatiquement à *Gagné*, cliquez sur **Gagné**. Le revenu attendu peut être mis à jour avec le montant réel.

À partir de la commande, la suite du flux est celle des chapitres suivants : **livraison** (chapitre 3) et **facture** (chapitre 4).

---

## Enquête

**« L'opportunité a disparu ! »**

Personne ne l'a supprimée (et un vendeur n'en a généralement pas le droit). Les explications les plus probables :

| Cause | Comment vérifier |
|---|---|
| Elle a été marquée **perdue** (par erreur, ou par un collègue) : elle est **archivée** | Filtre **Perdu** / **Archivé**. Le chatter dit qui l'a fait et quand |
| Elle a été marquée **gagnée** et Karim ne regarde que les étapes ouvertes | Colonne *Gagné*, ou filtre **Gagné** |
| Elle a été **réassignée** à un autre vendeur ou une autre équipe, et Karim filtre sur *Mon pipeline* | Retirer le filtre *Mon pipeline* |
| Elle a été **fusionnée** avec une autre opportunité du même client | Chercher l'opportunité du même client : son chatter mentionne la fusion |

**Moralité :** dans Odoo, presque rien n'est supprimé. Les documents sont **archivés**, et le **chatter** garde l'historique
de qui a fait quoi. C'est la première chose à regarder avant d'accuser quelqu'un.

---

## Exercice 2.6

**Le tableau de bord commercial.**

1. **Revenu attendu pondéré** : **CRM → Analyse → Pipeline** → vue **pivot** → mesure **Revenu au prorata**
   (*revenu attendu × probabilité*) → regrouper par vendeur. Le revenu attendu « brut » surestime : une opportunité
   à 10 000 € avec 10 % de chance « vaut » 1 000 €.
2. **Gagnées et perdues** : même analyse, filtres **Gagné** puis **Perdu** (ajoutez **Archivé** si les perdues n'apparaissent pas),
   regroupées par vendeur, mesure **Nombre**.
   Taux de transformation = gagnées ÷ (gagnées + perdues). Exemple : 12 gagnées, 18 perdues → 12 ÷ 30 = **40 %**.
3. **Motifs de perte** : filtre **Perdu** → **Regrouper par → Motif de perte** → vue graphique (secteurs) ou liste regroupée.

Enregistrez chaque analyse en **favori** (chapitre 9) pour la retrouver au trimestre suivant.

---

[⬅ Retour au chapitre](../02-crm.md)
