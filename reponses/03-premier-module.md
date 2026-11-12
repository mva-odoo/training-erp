# Réponses — Chapitre 3

[⬅ Retour au chapitre](../chapitres/03-premier-module.md)

---

## Exercice 3.1

**Lire un module standard.**

### 1. Dépendances

```python
'depends': ['sale', 'digest'],
```

`sale_management` n'est qu'une **surcouche** : le cœur des ventes est dans le module `sale`.
`sale_management` ajoute l'application dans le menu, les modèles de devis, les produits optionnels…
Odoo découpe souvent ainsi : un module technique de base, et un module « application » par-dessus.

### 2. Fichiers chargés

Les fichiers de `data` commencent par la **sécurité** (`security/…`), viennent ensuite les **vues**,
puis les **menus**. Ce n'est pas un hasard :
- une vue ou un menu qui pointe vers un modèle sans droits d'accès provoque des erreurs ou reste invisible ;
- un menu pointe vers une action, qui doit donc être chargée avant lui.

### 3. Les `__init__.py`

```python
# sale_management/__init__.py
from . import models        # charge le dossier models/
from . import controllers   # (s'il existe) charge le dossier controllers/
```

```python
# sale_management/models/__init__.py
from . import sale_order            # chaque fichier du dossier est importé un par un
from . import sale_order_line
from . import sale_order_template
# ...
```

C'est exactement la structure que vous allez reproduire.

---

## Exercice 3.2

**Créer le module Open Academy.**

### 1. Générer le module

```bash
cd /home/odoo/src/odoo
./odoo-bin scaffold openacademy /home/odoo/src/custo
```

Résultat :

```
/home/odoo/src/custo/openacademy/
├── __init__.py              ← from . import controllers, models
├── __manifest__.py
├── controllers/
│   ├── __init__.py
│   └── controllers.py       ← exemple de route, tout est commenté
├── demo/
│   └── demo.xml
├── models/
│   ├── __init__.py
│   └── models.py            ← exemple de modèle, tout est commenté
├── security/
│   └── ir.model.access.csv  ← exemple de droit, commenté
└── views/
    ├── templates.xml
    └── views.xml
```

### 2. Le manifest adapté

```python
# -*- coding: utf-8 -*-
{
    # Nom affiché dans Apps (librement choisi)
    'name': "Open Academy",

    # Une ligne de description, affichée sous le nom dans Apps
    'summary': "Gestion des cours et des sessions de formation",

    # Description longue (optionnelle)
    'description': """
        Module de formation du cours ERP de la HELB :
        cours, sessions, professeurs et participants.
    """,

    'author': "Prénom Nom",
    'website': "https://www.helb-prigogine.be",

    # Catégorie : sert à ranger le module dans Apps
    'category': 'Education',

    # Convention : <version Odoo>.<majeure>.<mineure>.<correctif>
    'version': '18.0.1.0.0',

    # Licence : obligatoire, sinon Odoo affiche un warning
    'license': 'LGPL-3',

    # Pour l'instant, on n'a besoin que du module de base
    'depends': ['base'],

    # Fichiers XML / CSV chargés à l'installation, dans cet ordre.
    # Le scaffold y met views.xml et templates.xml, qui ne contiennent
    # que des exemples commentés : on peut les laisser ou les retirer.
    'data': [
        # 'security/ir.model.access.csv',
        'views/views.xml',
        'views/templates.xml',
    ],

    # Affiche le module comme une "application" (filtre par défaut de Apps)
    'application': True,
}
```

### 3. Relancer Odoo avec le bon chemin

```bash
./odoo-bin -d my_database --addons-path=addons,/home/odoo/src/custo
```

> ⚠️ L'erreur classique : `--addons-path=addons,/home/odoo/src/custo/openacademy`.
> On donne le dossier qui **contient** les modules, pas le module.

### 4. Installer

Mode développeur → Apps → **Mettre à jour la liste des applications** → rechercher `openacademy`
(sans le filtre « Applications ») → **Activer**.

Le module n'a encore ni modèle ni menu : après l'installation, il ne se passe rien de visible.
C'est normal. Vous ajouterez les modèles au chapitre 4 et les menus au chapitre 5.

### 5. Committer

```bash
cd /home/odoo/src/custo
git add openacademy
git commit -m "openacademy: création du module"
git push
```

Un bon message de commit dit **quel module** et **ce qui a changé**. Évitez « update » ou « fix ».

---

[⬅ Retour au chapitre](../chapitres/03-premier-module.md)
