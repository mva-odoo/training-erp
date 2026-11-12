# Réponses — Chapitre 11

[⬅ Retour au chapitre](../chapitres/11-controleurs-site-web.md)

---

## Exercice 11.1

**Hello world.**

### Arborescence

```
openacademy/
├── __init__.py              → from . import controllers, models
└── controllers/
    ├── __init__.py          → from . import main
    └── main.py
```

### `controllers/main.py`

```python
from odoo import http


# Un contrôleur = une classe qui hérite de http.Controller.
# Odoo enregistre automatiquement toutes ses routes au démarrage.
class Academy(http.Controller):

    # @http.route : associe une URL à cette méthode.
    # auth='public' : accessible sans être connecté (le visiteur est l'utilisateur "Public").
    @http.route('/academy/hello', auth='public')
    def hello(self, **kw):
        # **kw : reçoit les paramètres de l'URL (?nom=valeur) ; ici on n'en a pas besoin,
        # mais sans **kw, un paramètre inattendu ferait planter la route.
        # On renvoie une chaîne : Odoo l'envoie telle quelle au navigateur.
        return "Hello, world!"
```

### `__init__.py` du module

```python
from . import controllers     # sans cette ligne, la route n'existe pas (404)
from . import models
```

**Redémarrer** suffit (c'est du Python, pas une donnée XML). Pas besoin de `-u`.

---

## Exercice 11.2

**Liste statique.**

### `controllers/main.py` (ajout)

```python
    @http.route('/academy/teachers', auth='public')
    def teachers(self, **kw):
        # request.render(template, valeurs) :
        #  - template : l'XML ID du template (<module>.<id>)
        #  - valeurs  : un dictionnaire ; chaque clé devient une variable dans le template
        return http.request.render('openacademy.teachers', {
            'teachers': ["Diana Padilla", "Jody Caroll", "Lester Vaughn"],
        })
```

### `views/templates.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<odoo>
    <!-- <template> : un raccourci pour créer une vue QWeb (ir.ui.view de type qweb) -->
    <template id="teachers">
        <title>Academy</title>
        <!-- t-foreach : boucle sur la liste "teachers" passée par le contrôleur.
             t-as      : le nom de la variable pour l'élément courant. -->
        <t t-foreach="teachers" t-as="teacher">
            <!-- t-out : insère la valeur, en échappant le HTML (sécurité) -->
            <p t-out="teacher"/>
        </t>
    </template>
</odoo>
```

```python
# __manifest__.py
'data': [
    # ...
    'views/templates.xml',
],
```

Un template est une **donnée XML** : il faut `-u openacademy` pour le charger.

---

## Exercice 11.3

**Les sessions sur le site web.**

### `__manifest__.py`

```python
# website.layout et website=True viennent du module website
'depends': ['base', 'contacts', 'mail', 'website'],
```

### `controllers/main.py` (ajout)

```python
    # website=True : la page est intégrée au site (langue, thème, menu, éditeur)
    @http.route('/academy/sessions', auth='public', website=True)
    def sessions(self, **kw):
        # request.env : l'environnement de la requête. Pour un visiteur, user = "Public".
        # sudo() : on lit avec les droits superutilisateur, car Public n'a aucun droit
        #          sur openacademy.session. C'est NOUS qui filtrons ce qui est montré :
        #          le domaine ci-dessous devient la vraie règle de sécurité.
        sessions = http.request.env['openacademy.session'].sudo().search(
            [('state', '=', 'confirm')],
            order='start_date',
        )
        return http.request.render('openacademy.sessions', {
            'sessions': sessions,
        })
```

### `views/templates.xml` (ajout)

```xml
<template id="sessions">
    <!-- website.layout : enveloppe la page avec l'en-tête, le menu et le pied de page du site -->
    <t t-call="website.layout">
        <!-- t-set title : le titre de l'onglet du navigateur -->
        <t t-set="title">Nos sessions</t>

        <!-- oe_structure : zone où l'éditeur du site permet de glisser des blocs -->
        <div class="oe_structure"/>

        <div class="container my-5">
            <h1>Sessions à venir</h1>

            <!-- Cas "aucune session" -->
            <p t-if="not sessions" class="text-muted">Aucune session prévue pour le moment.</p>

            <!-- Bootstrap : une liste de cartes -->
            <div class="list-group">
                <div t-foreach="sessions" t-as="session" class="list-group-item">
                    <h5 t-out="session.course_id.name"/>
                    <!-- t-field : formate la date selon la langue du visiteur -->
                    <span t-field="session.start_date"/> → <span t-field="session.end_date"/>
                </div>
            </div>
        </div>
    </t>
</template>
```

---

## Exercice 11.4

**Page de détail.**

### `controllers/main.py` (ajout)

```python
from werkzeug.exceptions import NotFound

    # <model("openacademy.session"):session> : Odoo lit l'id dans l'URL et fournit
    # directement le RECORDSET. Pas besoin de browse().
    # ⚠️ Le convertisseur fait un simple browse() : aucun contrôle sur l'état de la session,
    #    et le visiteur Public n'a pas le droit de lire ses champs.
    #    On passe donc en sudo() et on vérifie nous-mêmes ce qui peut être montré.
    @http.route('/academy/session/<model("openacademy.session"):session>',
                auth='public', website=True)
    def session_page(self, session, **kw):
        session = session.sudo()
        # Une session non confirmée ne doit pas être visible, même avec l'URL exacte.
        if session.state != 'confirm':
            raise NotFound()      # renvoie la page 404 du site
        return http.request.render('openacademy.session_page', {
            'session': session,
        })
```

### `views/templates.xml` — le lien dans la liste

```xml
<!-- t-attf-href : attribut "formaté" ; #{...} insère une expression -->
<a t-attf-href="/academy/session/#{session.id}"
   t-foreach="sessions" t-as="session"
   class="list-group-item list-group-item-action">
    <h5 t-out="session.course_id.name"/>
    <span t-field="session.start_date"/> → <span t-field="session.end_date"/>
</a>
```

### `views/templates.xml` — la page de détail

```xml
<template id="session_page">
    <t t-call="website.layout">
        <t t-set="title" t-value="session.course_id.name"/>
        <div class="container my-5">
            <a href="/academy/sessions">← Toutes les sessions</a>
            <h1 class="mt-3" t-out="session.course_id.name"/>

            <!-- On n'affiche QUE des champs choisis.
                 Jamais de boucle générique sur tous les champs : le mot de passe y passerait. -->
            <dl class="row">
                <dt class="col-sm-3">Dates</dt>
                <dd class="col-sm-9">
                    <span t-field="session.start_date"/> → <span t-field="session.end_date"/>
                </dd>

                <dt class="col-sm-3">Professeur</dt>
                <dd class="col-sm-9" t-out="session.instructor_id.name or 'À définir'"/>

                <dt class="col-sm-3">Places restantes</dt>
                <!-- t-set : une variable locale pour éviter de répéter le calcul -->
                <t t-set="remaining" t-value="session.max_participants - len(session.attendee_ids)"/>
                <dd class="col-sm-9">
                    <span t-if="remaining > 0" t-out="remaining"/>
                    <span t-else="" class="badge text-bg-danger">Complet</span>
                </dd>
            </dl>

            <div t-field="session.course_id.description"/>
        </div>
    </t>
</template>
```

### Les deux vérifications de sécurité

| Risque | Protection |
|---|---|
| Afficher le mot de passe | On ne cite que les champs voulus dans le template |
| Accéder à une session en brouillon via l'URL `/academy/session/7` | Le contrôleur vérifie `state` et renvoie 404 |

Avec `sudo()`, **c'est votre code qui fait office de sécurité**. Chaque route publique doit se poser
la question : « que se passe-t-il si quelqu'un modifie l'URL ? »

---

[⬅ Retour au chapitre](../chapitres/11-controleurs-site-web.md)
