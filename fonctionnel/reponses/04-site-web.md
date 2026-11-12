# Réponses — Chapitre 4 : Site web

[⬅ Retour au chapitre](../04-site-web.md)

---

## Exercice 4.1

**La page d'accueil.**

**Site Web** (la page d'accueil s'affiche) → **Modifier**.

1. **Bannière** : onglet **Blocs** → glissez un bloc de type *Couverture* ou *Bannière* tout en haut de la page.
   - Cliquez sur le titre et tapez « Torréfaction Sablon », puis le sous-titre.
   - Cliquez sur l'image de fond → **Remplacer** → choisissez une image (la bibliothèque propose des photos libres de droits :
     cherchez « coffee »).
   - Cliquez sur le bouton → modifiez le texte « Découvrir nos cafés » et le lien (`/shop` si la boutique existe, sinon `/nos-ateliers`).
2. **Texte + image** : glissez le bloc *Texte - Image* sous la bannière, écrivez la présentation, remplacez l'image.
3. **Chiffres clés** : bloc *Chiffres* (ou *Compteurs*) → modifiez les trois nombres et leurs légendes.
4. Supprimez le texte par défaut « Bienvenue sur votre Accueil ! » : sélectionnez son bloc → icône 🗑.
5. **Sauvegarder**.

Vérification mobile : bouton 📱 dans la barre du haut. Les colonnes doivent s'empiler les unes sous les autres.

> Une erreur fréquente : modifier puis fermer l'onglet sans **Sauvegarder**. Tout est perdu.

---

## Exercice 4.2

**Le thème.**

**Modifier** → onglet **Thème** :
1. **Logo** : section de l'en-tête (ou *Logo*) → téléversez l'image. Le logo apparaît dans l'en-tête de **toutes** les pages.
2. **Couleurs** : section *Couleurs* → choisissez une palette proposée, ou définissez les couleurs principale et secondaire
   (bruns, crème). Les boutons, liens et fonds s'adaptent partout.
3. **Polices** : section *Polices* / *Texte* → police des **titres**.

**Sauvegarder**.

### Pourquoi passer par le Thème

Si on change la couleur de chaque bouton dans l'onglet **Style**, il faudra le refaire sur chaque nouvelle page.
Le **Thème** définit une seule fois l'identité visuelle, et tous les blocs, présents et futurs, la reprennent.

---

## Exercice 4.3

**Deux nouvelles pages.**

1. **Nouveau → Page** → choisissez un modèle (par exemple *À propos*) → nom de la page « Notre histoire » →
   laissez coché **Ajouter au menu** → **Créer**. L'éditeur s'ouvre : remplacez les textes et l'image → **Sauvegarder**.
   L'URL est générée à partir du nom : `/notre-histoire`. Pour la changer : **Site → Cette page → Propriétés**.
2. Même chose pour « Nos ateliers ».
3. **Site → Éditeur de menu** : glissez les entrées dans l'ordre *Accueil, Notre histoire, Nos ateliers, Contactez-nous*.
   Retirez les entrées inutiles (comme *Boutique* si l'eCommerce n'est pas encore configuré) → **Enregistrer**.

### Une page créée n'est pas forcément publiée

Selon la version et le modèle, une nouvelle page peut être créée **non publiée**. Vérifiez l'interrupteur **Publié** en haut
de la page, ou la colonne *Est publié* dans **Site → Contenu → Pages**. Une page non publiée est visible par les utilisateurs
connectés, mais donne une erreur 404 aux visiteurs.

---

## Exercice 4.4

**Retrouver le message.**

La base a le **CRM** installé : le formulaire crée une **piste**.
**CRM → Pipeline** (ou **CRM → Pistes** si les pistes sont activées) → une nouvelle opportunité « Contactez-nous » ou portant
le sujet du message, avec le nom et l'e-mail du visiteur. Le texte du message est dans la description, et le chatter
contient le détail.

Sans CRM, le message serait parti par **e-mail** à l'adresse de la société (et visible dans
**Paramètres → Technique → E-mails** en mode développeur).

**À retenir :** un formulaire web n'est pas « juste un formulaire ». Il alimente directement l'ERP, et un commercial peut
répondre au prospect sans aucune ressaisie.

---

## Exercice 4.5

**SEO.**

Sur la page d'accueil (en mode affichage, pas en édition) → **Site → Cette page → Optimisation SEO** :

| Champ | Accueil | Notre histoire |
|---|---|---|
| Titre | Torréfaction Sablon — Café torréfié à Bruxelles | Notre histoire — Torréfaction Sablon |
| Description | Torréfacteur artisanal au Sablon depuis 1987. Cafés de 12 origines, en grains ou moulus, pour particuliers et restaurants. | Depuis 1987, trois générations de torréfacteurs au cœur de Bruxelles… |
| Mots-clés | café Bruxelles, torréfacteur, café en grains | torréfaction artisanale, histoire, Sablon |

L'outil indique pour chaque mot-clé s'il est présent dans le titre et le contenu de la page, et montre un aperçu du résultat Google.

**Une description par page**, pas la même partout : Google n'aime pas les doublons.

---

## Enquête

**« Ma page n'existe pas ! »**

| Cause possible | Comment vérifier | Correction |
|---|---|---|
| La page **n'est pas publiée** | Interrupteur **Publié / Non publié** en haut de la page, ou **Site → Contenu → Pages**, colonne *Est publié* | Publier la page |
| La page a une **date de publication** future | **Propriétés** de la page | Corriger la date |
| La page est réservée à certains utilisateurs (**visibilité** : connectés, groupe, mot de passe) | **Propriétés → Visibilité** | Mettre la visibilité sur *Tous* |
| La page n'est **pas dans le menu** | Le client ne la « trouve pas » | L'ajouter au menu |
| Sophie a envoyé une **mauvaise URL** | Comparer avec la colonne URL de la liste des pages | Envoyer la bonne |

**Pourquoi Sophie la voit-elle ?** Parce qu'elle est **connectée** avec des droits d'éditeur : Odoo lui montre aussi
les pages non publiées (avec un bandeau). **Testez toujours en navigation privée.**

---

## Exercice 4.6

**Réserver un atelier.**

Page *Nos ateliers* → **Modifier** → onglet **Blocs** → glissez le bloc **Formulaire**.

Cliquez sur le formulaire → à droite :
- **Action** : *Créer une opportunité* (CRM) ;
- ajoutez les champs : **Nom** (champ *Nom du contact*), **E-mail**, **Téléphone**, et deux champs **personnalisés**
  *Date souhaitée* (type date) et *Nombre de personnes* (type nombre) ;
- message de confirmation : « Merci ! Nous vous recontactons dans les 24 heures. »

**Sauvegarder**, puis tester en navigation privée.

Dans le CRM, l'opportunité contient le nom, l'e-mail et le téléphone dans leurs champs. Les champs **personnalisés**
(date, nombre de personnes) n'existent pas dans le modèle des opportunités : Odoo les ajoute dans la **description** de
l'opportunité. Ils sont donc visibles, mais pas filtrables.

> Pour pouvoir filtrer sur le nombre de personnes, il faudrait un vrai champ sur l'opportunité, donc
> un petit module (cours technique, chapitre 7). C'est exactement le genre d'arbitrage « configuration ou
> développement » qu'on fait dans un projet.

---

[⬅ Retour au chapitre](../04-site-web.md)
