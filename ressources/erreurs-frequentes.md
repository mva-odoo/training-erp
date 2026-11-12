# Erreurs fréquentes

[⬅ Sommaire](../README.md)

Avant de demander de l'aide : **lisez la dernière ligne de l'erreur** dans le terminal.
Elle donne presque toujours le fichier et la ligne en cause.

---

### Mon module n'apparaît pas dans Apps

1. Le chemin dans `--addons-path` est le dossier **parent** du module.
2. **Mettre à jour la liste des applications** en mode développeur.
3. Retirez le filtre « Applications » dans la recherche.
4. Vérifiez qu'il n'y a pas d'erreur de syntaxe dans `__manifest__.py` (logs au démarrage).

### Mon nouveau champ n'existe pas / « column does not exist »

Vous avez redémarré sans `-u`. Relancez avec `-u <votre_module>`.

### Mon fichier Python est ignoré

Il n'est pas importé : vérifiez `models/__init__.py` **et** le `__init__.py` du module.

### Mon menu n'apparaît pas

1. Le fichier XML est-il dans `data` du manifest ?
2. L'utilisateur a-t-il le droit de lecture sur le modèle de l'action ?
3. Rafraîchissez la page (`F5`).

### « The model … has no access rules »

Ajoutez une ligne pour ce modèle dans `security/ir.model.access.csv`, et vérifiez que le CSV est dans le manifest.

### « External ID not found in the system: … »

Vous référencez un XML ID qui n'existe pas (encore) :
- faute de frappe dans le `ref` ;
- le fichier qui le définit est chargé **après** celui qui l'utilise (ordre dans `data`) ;
- il vient d'un autre module : préfixez-le (`base.view_partner_form`) et ajoutez ce module dans `depends`.

### « Field … does not exist » à la mise à jour d'une vue

Le champ utilisé dans le XML n'existe pas dans le modèle : faute de frappe, ou le code Python n'a pas été chargé
(fichier non importé, serveur non redémarré).

### « Element … cannot be located in parent view »

L'expression XPath ne trouve rien dans la vue parente. Ouvrez la vue parente (mode développeur → Modifier la vue)
et vérifiez le nom exact de l'élément ciblé.

### « Compute method failed to assign … »

Votre méthode de calcul n'assigne pas de valeur à **tous** les enregistrements. Ajoutez un cas par défaut
(`rec.total = 0`) en dehors de vos `if`.

### « Expected singleton »

Vous utilisez `self.champ` alors que `self` contient plusieurs enregistrements. Bouclez : `for rec in self:`.

### « Access Denied » / « You are not allowed to access … »

Vérifiez les droits d'accès (CSV) et les règles d'enregistrement du groupe de l'utilisateur.
Testez avec l'administrateur pour savoir si le problème vient de la sécurité.

### Mes modifications XML ne s'affichent pas

Relancez avec `-u`, ou utilisez `--dev=xml` pendant le développement. Videz le cache du navigateur si besoin (`Ctrl + Shift + R`).

### « Address already in use » au démarrage

Un autre Odoo tourne déjà sur le port 8069. Arrêtez-le (`Ctrl + C` dans son terminal, ou `docker stop`).
