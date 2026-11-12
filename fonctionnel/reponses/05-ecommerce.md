# Réponses — Chapitre 5 : eCommerce

[⬅ Retour au chapitre](../05-ecommerce.md)

---

## Exercice 5.1

**Mettre en ligne les cafés.**

1. **Site Web → eCommerce → Produits → Catégories d'eCommerce → Nouveau** : `Cafés` → **Enregistrer**.
2. Pour chaque café : **Site Web → eCommerce → Produits → Produits** → ouvrez le produit :
   - image : cliquez sur la vignette en haut à droite du formulaire → téléversez une photo ;
   - onglet **Ventes** → section **Boutique eCommerce** → **Catégories** : `Cafés` ; **Est publié** : activé ;
   - la description visible sur le site se modifie le plus facilement **sur le site** : bouton **Aller au site Web** →
     **Modifier** → cliquez sous le prix et tapez le texte → **Sauvegarder**.
3. Le sac kraft et l'atelier : laissez **Est publié** désactivé.

### Catégorie de produit ou catégorie eCommerce ?

Ce sont deux choses différentes :

| | Catégorie de **produit** | Catégorie **eCommerce** |
|---|---|---|
| Sert à | Comptabilité, stock, analyses internes | Ranger les produits dans la boutique |
| Visible par le client | Non | Oui (filtres, menu) |
| Nombre par produit | Une seule | Plusieurs possibles |

Un même café peut être dans les catégories eCommerce *Cafés* **et** *Idées cadeaux*.

---

## Exercice 5.2

**Variantes de mouture.**

1. **Ventes → Configuration → Paramètres** → section *Produits* → cochez **Variantes** → **Enregistrer**.
2. **Ventes → Configuration → Produits → Attributs → Nouveau** :
   - nom `Mouture`, type d'affichage **Pilules** (ou *Radio*) ;
   - **Création des variantes : Instantanément** ;
   - valeurs : `Grains`, `Espresso`, `Filtre`.
3. Fiche **Café Brésil 1 kg** → onglet **Attributs & Variantes** → **Ajouter une ligne** : attribut *Mouture*,
   valeurs *Grains, Espresso, Filtre* → **Enregistrer**. Un bouton intelligent **3 Variantes** apparaît.
4. Supplément Espresso : bouton **Configurer** sur la ligne d'attribut (ou **Ventes → Configuration → Attributs →
   Mouture → valeur Espresso**) → **Supplément de prix** : `1,00`.

### Ce qui a changé

- Le **modèle** de produit (*Café Brésil 1 kg*) porte ce qui est commun : nom, description, catégorie.
- Chaque **variante** (*Café Brésil 1 kg (Espresso)*) a son **stock**, sa **référence interne**, son **code-barres**.

> ⚠️ Le stock existant de Café Brésil a été réparti sur une variante (souvent la première). Après avoir ajouté des variantes
> à un produit qui a déjà du stock, faites un **inventaire physique** pour répartir correctement les quantités.

---

## Exercice 5.3

**Livraison et paiement.**

### Livraison bpost

**Site Web → Configuration → eCommerce → Modes de livraison → Nouveau** :
- nom `Livraison bpost`, fournisseur **Prix fixe**, prix `5,95` ;
- cochez **Gratuit si le montant de la commande est supérieur à** `50` ;
- **Publier** sur le site (bouton/interrupteur *Publié*).

### Retrait en boutique

**Nouveau** : nom `Retrait en boutique`, **Prix fixe**, `0` → **Publier**.

> Avec un retrait en boutique, il n'y a pas d'expédition, mais Odoo crée quand même un **bon de livraison**,
> que l'on valide quand le client vient chercher sa commande.

### Virement bancaire

**Site Web → Configuration → eCommerce → Fournisseurs de paiement** → **Virement bancaire** → **État : Activé** →
vérifiez le message affiché au client (il reprend le **compte bancaire** de la société : ajoutez-le dans
**Facturation → Configuration → Comptabilité → Journaux → Banque** si nécessaire) → **Enregistrer**.

Le bandeau « Activez un fournisseur de paiement » disparaît des paramètres du site.

---

## Exercice 5.4

**Première commande en ligne.**

### 1. Côté client (navigation privée)

`/shop` → Café Brésil → mouture *Espresso*, quantité 2 → **Ajouter au panier** → idem Café Maison moulu →
icône panier → **Payer** → adresse (créez un compte) → mode **Livraison bpost** : *Gratuit* car le total dépasse 50 € →
**Virement bancaire** → **Payer maintenant**. La page de confirmation affiche les coordonnées bancaires et la communication.

### 2. Côté Odoo

**Site Web → eCommerce → Commandes non payées** : la commande est là. Le paiement par virement est **en attente** :
Odoo ne peut pas savoir si l'argent est arrivé. La commande n'est pas encore considérée comme payée.

### 3. Le virement est arrivé

- Ouvrez la commande → onglet/bouton des **transactions** de paiement → confirmez la transaction (ou, selon la configuration,
  **Confirmer** la commande puis enregistrez le paiement sur la facture).
- **Livraison** : bouton intelligent **Livraison** → **Valider**.
- **Facture** : **Créer une facture** → **Confirmer** → **Payer** (journal Banque), si le paiement n'a pas déjà été lettré.

### À retenir

Le visiteur n'a rien saisi dans « Ventes » : il a utilisé le site. Pourtant on retrouve exactement les mêmes documents
qu'aux chapitres 2 et 3 : commande, livraison, facture, paiement. C'est l'intérêt de l'eCommerce **intégré**.

---

## Enquête

**« Mon café n'est pas dans la boutique ! »**

| Cause | Où vérifier |
|---|---|
| Le produit n'est **pas publié** | Onglet **Ventes** → *Est publié* (ou interrupteur sur la page produit) |
| La case **Peut être vendu** est décochée | En haut du formulaire produit, case *Ventes* |
| Le produit est limité à un **autre site web** (multi-site) | Onglet **Ventes** → champ *Site Web* |
| Le produit n'est dans **aucune catégorie** et le client navigue par catégorie | Onglet **Ventes** → *Catégories* |
| Le produit est **archivé** | Filtre *Archivé* dans la liste des produits |

Et toujours : **testez en navigation privée**. Un administrateur connecté voit aussi les produits non publiés.

---

## Exercice 5.5

**Défi : le stock en ligne.**

1. Fiche **Café Éthiopie** → onglet **Ventes** → section *Boutique eCommerce* :
   - **Vendre en cas de rupture de stock** : décoché ;
   - **Afficher la quantité disponible** : coché, seuil **5** ;
   - (optionnel) **Message de rupture de stock** : « Victime de son succès, de retour bientôt ! ».
2. Même section → **Ruban** : `Nouveau` (créez-le si besoin, avec une couleur).
3. **Inventaire → Opérations → Ajustements → Inventaire physique** → Café Éthiopie, compté **0** → **Appliquer**.
   Sur le site : le produit est toujours visible, mais marqué **en rupture** et le bouton **Ajouter au panier** est désactivé.
4. Fiche des cafés en grains → onglet **Ventes** → **Produits optionnels** : *Café Maison moulu*. En ajoutant un café en grains
   au panier, une fenêtre propose le café moulu en option (comme la fenêtre « Configurer » de la capture du chapitre).

> Le stock affiché sur le site est le stock **disponible** de l'entrepôt du site web (**Configuration → Paramètres** du site),
> en temps réel. Une vente en boutique (chapitre 7) diminue aussi le stock en ligne : un seul stock pour tous les canaux.

---

[⬅ Retour au chapitre](../05-ecommerce.md)
