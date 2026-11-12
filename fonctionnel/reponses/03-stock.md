# Réponses — Chapitre 3 : Stock

[⬅ Retour au chapitre](../03-stock.md)

---

## Exercice 3.1

**Le catalogue.**

**Inventaire → Produits → Produits → Nouveau**, pour chaque produit :

- **Nom**, **Prix de vente**, **Coût** (onglet Informations générales).
- **Type de produit** : *Biens* pour les cafés et le sac, *Service* pour l'atelier.
- **Suivre l'inventaire** : coché pour les trois cafés, **décoché** pour le sac kraft.

Pour la catégorie : dans le champ **Catégorie**, tapez `Café` → **Créer « Café »**.
(Ou **Inventaire → Configuration → Produits → Catégories** pour la créer d'abord.)

### Pourquoi le sac n'est pas suivi

On achète les sacs par milliers, ils coûtent 0,15 €, et on ne veut pas les compter à chaque vente. Suivre leur stock
demanderait un inventaire régulier pour une valeur négligeable. Un produit non suivi peut quand même être acheté et vendu,
mais Odoo ne calcule pas de quantité en stock.

### Pourquoi une catégorie

La catégorie sert à **regrouper** (analyses, listes de prix, règles de stock) et porte des réglages comptables
(comptes de revenus et de charges). C'est plus facile de configurer une catégorie que 50 produits.

---

## Exercice 3.2

**Première livraison du fournisseur.**

1. **Achats → Commandes → Fournisseurs → Nouveau** (ou directement dans le champ Fournisseur de la commande, *Créer*).
2. **Achats → Nouveau** : fournisseur *Café Import Antwerpen*, trois lignes avec les quantités. **Confirmer la commande**.
   Un bouton intelligent **Réception** apparaît.
3. Cliquez sur **Réception** : la réception `WH/IN/0000x` est à l'état **Prêt**.
   Dans la colonne **Quantité** de la ligne Éthiopie, mettez **20**. Cliquez sur **Valider**.
   Odoo demande : **Créer un reliquat ?** → **Créer un reliquat**.

### Ce qui s'est passé

| Document | État |
|---|---|
| Réception d'origine | Terminé : 50 + 20 + 100 entrés en stock |
| Reliquat `WH/IN/0000y` | Prêt : 10 Café Éthiopie attendus |
| Commande fournisseur | Ligne Éthiopie : Reçu 20 / 30 |

**Pas de reliquat** (« Aucun reliquat ») aurait signifié : « le fournisseur ne livrera jamais le reste ».
La commande serait alors considérée comme entièrement traitée pour cette ligne.

---

## Exercice 3.3

**Le Café du Sablon commande.**

1. **Ventes → Nouveau** : client *Café du Sablon* (créez-le), lignes 10 × Brésil, 15 × Éthiopie. **Confirmer**.
2. Le bouton intelligent **Livraison** ouvre `WH/OUT/0000x`. Son état est **Prêt** si tout est disponible…
   mais il n'y a que **20** Éthiopie (exercice 3.2) : 15 sont disponibles, donc la livraison est **Prête**.

   > Si vous avez reçu moins, ou si une autre commande a réservé du stock avant, l'état peut être **En attente**.
   > La colonne de disponibilité sur chaque ligne indique ce qui est réservé.

3. Pour livrer en deux fois : dans la livraison, mettez les quantités **réellement expédiées**, **Valider** →
   **Créer un reliquat**. Le reliquat sera livré plus tard, avec sa propre validation.

**Lien avec la facturation (chapitre 4) :** si la politique de facturation du produit est « Quantités livrées »,
la commande ne peut être facturée que pour ce qui a été **livré**.

---

## Exercice 3.4

**La réserve et la boutique.**

1. **Inventaire → Configuration → Paramètres** → section *Entrepôt* → cochez **Emplacements de stockage** → **Enregistrer**.
2. **Configuration → Gestion de l'entrepôt → Emplacements → Nouveau** :
   - Nom : `Réserve`, Emplacement parent : `WH/Stock`, Type : *Interne* ;
   - même chose pour `Boutique`.
   Les emplacements complets sont `WH/Stock/Réserve` et `WH/Stock/Boutique`.
3. **Vue d'ensemble** → carte **Transferts internes** → **Nouveau** (ou **Opérations → Transferts → Interne → Nouveau**) :
   de `WH/Stock/Réserve` vers `WH/Stock/Boutique`, 10 × Café Maison moulu → **Valider**.

> Si le stock est entièrement dans `WH/Stock` (et pas encore dans `Réserve`), le transfert depuis *Réserve* sera
> en attente. Faites d'abord un transfert `WH/Stock` → `WH/Stock/Réserve`, ou recevez directement dans la réserve.

### Pourquoi le total ne change pas

Un transfert interne déplace le stock **entre deux emplacements internes** : il sort de l'un et entre dans l'autre.
Le stock de l'entrepôt reste le même. Seuls les mouvements vers ou depuis un emplacement **virtuel** (fournisseur, client,
rebut, ajustement) modifient le total.

---

## Exercice 3.5

**Corriger le stock.**

1. **Opérations → Ajustements → Inventaire physique** → ligne Café Brésil (emplacement `WH/Stock`) →
   colonne **Compté** : saisissez la quantité comptée → la colonne **Différence** se calcule → bouton **Appliquer**
   (sur la ligne, ou après sélection) → confirmez.
   Odoo crée un mouvement de `WH/Stock` vers **Ajustement d'inventaire** pour la différence.
2. **Opérations → Ajustements → Rebut → Nouveau** : produit *Café Maison moulu*, quantité 1 → **Valider**.
   Le paquet part vers l'emplacement virtuel **Rebut**.

### Pourquoi deux outils différents

| | Inventaire physique | Rebut |
|---|---|---|
| On connaît la cause ? | Non : on constate un écart | Oui : produit abîmé |
| Trace | « Ajustement d'inventaire » | « Rebut », avec le motif |

En analyse, on peut ainsi distinguer les **pertes connues** (casse) des **écarts inexpliqués** (erreurs, vol),
ce qui intéresse beaucoup le gérant.

---

## Exercice 3.6

**Réapprovisionnement automatique.**

1. Fiche **Café Brésil** :
   - onglet **Achats** → **Ajouter une ligne** : fournisseur *Café Import Antwerpen*, prix 11 € ;
   - onglet **Inventaire** → **Routes** : cochez **Acheter** (si la case est proposée).
2. Bouton intelligent **Règles de réapprovisionnement** (ou **Opérations → Approvisionnement → Réassort → Nouveau**) :
   emplacement `WH/Stock`, **Min** 20, **Max** 80.
3. Vendez du Café Brésil (une commande client confirmée) jusqu'à ce que le stock **prévu** passe sous 20.

### Ce qui se passe

Le **planificateur** (une tâche planifiée, exécutée chaque nuit) détecte que le stock prévu est sous le minimum et crée une
**demande de prix** chez le fournisseur pour remonter au maximum. Pour ne pas attendre la nuit :
**Opérations → Approvisionnement → Réassort** → bouton **Commander** sur la ligne (ou
**Approvisionnement : lancer le planificateur**).

**Quantité commandée** = Max − stock prévu. Si le stock prévu est 15 : 80 − 15 = **65 kg**.

> La demande de prix est en **brouillon** : quelqu'un doit la vérifier et la confirmer. Odoo propose, l'humain décide.

---

## Enquête

**« Le stock ne bouge pas ! »**

Ce n'est pas un bug : le **Sac kraft** a été créé **sans suivi de l'inventaire** (exercice 3.1).
Odoo crée quand même une **livraison** (pour savoir ce qui a été expédié au client), mais il ne tient **aucune quantité
en stock** pour ce produit : pas de quantité disponible, pas de ligne dans **Analyse → Stock**, pas d'inventaire.

| | Biens **sans** suivi | Biens **avec** suivi |
|---|---|---|
| Livraison créée | ✅ | ✅ |
| Quantité en stock connue | ❌ | ✅ |
| Réapprovisionnement automatique | ❌ | ✅ |
| Inventaire physique | ❌ | ✅ |

(Un **service**, lui, ne crée même pas de livraison.)

Si l'entreprise veut finalement suivre les sacs, il faut cocher **Suivre l'inventaire** sur le produit, puis faire un
**inventaire physique** pour enregistrer la quantité réelle de départ.

**Moralité :** le type de produit se décide **au début**, avec le client. Il change tout le comportement du produit.

---

## Exercice 3.7

**Défi : vente plus grande que le stock.**

1. **Commande client** : 30 × Café Éthiopie → **Confirmer**. La livraison est **En attente** (ou partiellement disponible :
   10 réservés).
2. **Livrer les 10 disponibles** : dans la livraison, quantité 10 → **Valider** → **Créer un reliquat** (20 restants).
3. **Recommander** : si la règle de l'exercice 3.6 existe pour l'Éthiopie (sinon, créez-la), le **Réassort** propose une
   commande. Sinon, créez une commande fournisseur manuelle. Confirmez-la.
4. **Recevoir** : validez la réception du fournisseur. Le reliquat de livraison passe automatiquement à **Prêt**
   (Odoo réserve le stock arrivé pour les livraisons en attente).
5. **Livrer le reliquat** : **Valider**.
6. **Historique** : **Analyse → Historique des mouvements**, filtre sur le produit *Café Éthiopie*. On voit toutes les
   entrées (fournisseur → stock) et sorties (stock → client), avec les références des documents.

Variante pour la gérante : depuis la fiche produit, bouton intelligent **Entrée / Sortie** : même historique, déjà filtré.

---

[⬅ Retour au chapitre](../03-stock.md)
