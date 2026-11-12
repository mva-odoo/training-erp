# Réponses — Chapitre 12

[⬅ Retour au chapitre](../chapitres/12-wizards.md)

---

## Exercice 12.1

**Inscrire plusieurs participants d'un coup.**

### `__init__.py` du module

```python
from . import controllers
from . import models
from . import wizard          # nouveau dossier
```

### `wizard/__init__.py`

```python
from . import add_attendees
```

### `wizard/add_attendees.py`

```python
from odoo import fields, models


class AddAttendeesWizard(models.TransientModel):
    # TransientModel et non Model : les enregistrements de ce modèle sont TEMPORAIRES.
    # Odoo les supprime automatiquement (tâche planifiée "vacuum").
    # On n'y stocke rien d'important : c'est juste le "formulaire" de la fenêtre.
    _name = 'openacademy.add.attendees.wizard'
    _description = "Ajout de participants à une session"

    def _default_session(self):
        # Quand l'action est lancée depuis une session, Odoo met dans le contexte :
        #   active_model = 'openacademy.session'
        #   active_id    = l'id de la session ouverte
        # active_id est un ENTIER : browse() le transforme en recordset.
        # Si active_id est absent (wizard ouvert d'ailleurs), browse(None) donne un recordset vide.
        return self.env['openacademy.session'].browse(self.env.context.get('active_id'))

    # default=_default_session : on passe la MÉTHODE (sans parenthèses).
    # Odoo l'appelle à l'ouverture du wizard pour pré-remplir le champ.
    session_id = fields.Many2one(
        'openacademy.session',
        string='Session',
        required=True,
        default=_default_session,
    )

    # Les contacts à inscrire. Sans relation=..., Odoo crée une table de jonction
    # dédiée au wizard (elle sera vidée avec les enregistrements temporaires).
    attendee_ids = fields.Many2many('res.partner', string='Participants')

    def action_add(self):
        # self = l'enregistrement du wizard (un seul : celui de la fenêtre).
        # ensure_one() : lève une erreur si ce n'est pas le cas. Bonne habitude.
        self.ensure_one()

        # |= : UNION de recordsets. On AJOUTE les nouveaux participants à ceux
        # déjà inscrits. Avec "=", on REMPLACERAIT la liste (et on perdrait les anciens).
        self.session_id.attendee_ids |= self.attendee_ids

        # Pas de return : la fenêtre se ferme toute seule après un bouton type="object"
        # dans un wizard. On pourrait aussi renvoyer {'type': 'ir.actions.act_window_close'}.
```

### `wizard/add_attendees.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<odoo>
    <!-- La vue : un formulaire simple, sans sheet ni header -->
    <record id="add_attendees_wizard_form" model="ir.ui.view">
        <field name="name">openacademy.add.attendees.wizard.form</field>
        <field name="model">openacademy.add.attendees.wizard</field>
        <field name="arch" type="xml">
            <form string="Ajout de participants">
                <group>
                    <!-- readonly : la session vient du contexte, on ne la change pas ici -->
                    <field name="session_id" readonly="1"/>
                    <field name="attendee_ids" widget="many2many_tags"/>
                </group>
                <!-- footer : la barre de boutons en bas de la fenêtre modale.
                     Dans un wizard, il REMPLACE les boutons Enregistrer / Annuler par défaut. -->
                <footer>
                    <button name="action_add" type="object" string="Ajouter" class="btn-primary"/>
                    <!-- special="cancel" : ferme la fenêtre sans appeler de méthode -->
                    <button special="cancel" string="Annuler"/>
                </footer>
            </form>
        </field>
    </record>

    <!-- L'action : ouvre le wizard -->
    <record id="add_attendees_wizard_action" model="ir.actions.act_window">
        <field name="name">Ajouter des participants</field>
        <field name="res_model">openacademy.add.attendees.wizard</field>
        <field name="view_mode">form</field>
        <!-- new : dans une fenêtre modale par-dessus l'écran courant -->
        <field name="target">new</field>
        <!-- binding_model_id : l'action apparaît dans le menu Action (⚙️) des SESSIONS.
             C'est le modèle D'ORIGINE, pas le wizard : c'est depuis la session qu'on clique. -->
        <field name="binding_model_id" ref="openacademy.model_openacademy_session"/>
        <!-- form seulement : depuis la liste, active_id serait ambigu (exercice 12.2) -->
        <field name="binding_view_types">form</field>
    </record>
</odoo>
```

### `security/ir.model.access.csv` (ajout)

```csv
access_add_attendees_wizard,add attendees wizard,model_openacademy_add_attendees_wizard,group_openacademy_user,1,1,1,0
```

Un `TransientModel` est un vrai modèle : sans droit d'accès, seul l'administrateur peut l'utiliser.
Pas besoin du droit de suppression (`perm_unlink = 0`) : c'est Odoo qui nettoie.

### `__manifest__.py`

```python
'data': [
    'security/security.xml',
    'security/ir.model.access.csv',
    # ...
    'wizard/add_attendees.xml',
],
```

---

## Exercice 12.2

**Inscrire à plusieurs sessions.**

### `wizard/add_attendees.py`

```python
from odoo import fields, models


class AddAttendeesWizard(models.TransientModel):
    _name = 'openacademy.add.attendees.wizard'
    _description = "Ajout de participants à des sessions"

    def _default_sessions(self):
        # active_ids : la LISTE des ids sélectionnés dans la vue liste.
        # Depuis un formulaire, Odoo met aussi active_ids = [active_id] : ça marche dans les deux cas.
        return self.env['openacademy.session'].browse(self.env.context.get('active_ids'))

    # Many2many au lieu de Many2one : plusieurs sessions possibles
    session_ids = fields.Many2many(
        'openacademy.session',
        string='Sessions',
        required=True,
        default=_default_sessions,
    )
    attendee_ids = fields.Many2many('res.partner', string='Participants')

    def action_add(self):
        self.ensure_one()
        # On boucle sur les sessions : chacune reçoit les nouveaux participants
        for session in self.session_ids:
            session.attendee_ids |= self.attendee_ids
```

> Variante sans boucle, avec une commande ORM qui ajoute le lien sur toutes les sessions en une fois :
> `self.session_ids.write({'attendee_ids': [Command.link(p.id) for p in self.attendee_ids]})`
> (avec `from odoo import Command`).

### Vue et action

```xml
<field name="session_ids" widget="many2many_tags" readonly="1"/>
```

```xml
<!-- L'action est maintenant disponible depuis la liste ET le formulaire -->
<field name="binding_view_types">list,form</field>
```

---

## Exercice 12.3

**Depuis un cours.**

### `wizard/create_session.py`

```python
from odoo import api, fields, models


class CreateSessionWizard(models.TransientModel):
    _name = 'openacademy.create.session.wizard'
    _description = "Création d'une session depuis un cours"

    # Ici, on pré-remplit le cours avec default_get, une autre façon de définir des valeurs par défaut.
    # Elle reçoit la liste des champs à initialiser et renvoie un dictionnaire.
    @api.model
    def default_get(self, fields_list):
        res = super().default_get(fields_list)
        # On vérifie qu'on vient bien d'un cours avant d'utiliser active_id :
        # sinon, ouvert depuis un autre modèle, on prendrait un id qui n'a rien à voir.
        if self.env.context.get('active_model') == 'openacademy.course':
            res['course_id'] = self.env.context.get('active_id')
        return res

    course_id = fields.Many2one('openacademy.course', string='Cours', required=True)
    name = fields.Char(string='Nom de la session', required=True)
    start_date = fields.Date(string='Date de début', default=fields.Date.today)
    attendee_ids = fields.Many2many('res.partner', string='Participants')

    def action_create(self):
        self.ensure_one()
        # create() sur le modèle PERMANENT : c'est là que la vraie donnée naît.
        session = self.env['openacademy.session'].create({
            'name': self.name,
            'course_id': self.course_id.id,     # Many2one : on passe l'id
            'start_date': self.start_date,
            # Many2many : une liste de commandes. (6, 0, ids) = "la liste vaut exactement ces ids"
            'attendee_ids': [(6, 0, self.attendee_ids.ids)],
        })
        # Au lieu de fermer la fenêtre, on ouvre la session créée :
        # on renvoie une ACTION, exactement comme celles définies en XML.
        return {
            'type': 'ir.actions.act_window',
            'res_model': 'openacademy.session',
            'res_id': session.id,           # l'enregistrement à afficher
            'view_mode': 'form',
            'target': 'current',            # dans l'écran principal, pas en modale
        }
```

### `wizard/create_session.xml`

```xml
<odoo>
    <record id="create_session_wizard_form" model="ir.ui.view">
        <field name="name">openacademy.create.session.wizard.form</field>
        <field name="model">openacademy.create.session.wizard</field>
        <field name="arch" type="xml">
            <form string="Ajout de session">
                <group>
                    <field name="course_id" readonly="1"/>
                    <field name="name"/>
                    <field name="start_date"/>
                </group>
                <!-- Sans widget, un Many2many s'affiche en tableau (comme sur la maquette) -->
                <field name="attendee_ids"/>
                <footer>
                    <button name="action_create" type="object" string="Ajouter" class="btn-primary"/>
                    <button special="cancel" string="Annuler"/>
                </footer>
            </form>
        </field>
    </record>

    <record id="create_session_wizard_action" model="ir.actions.act_window">
        <field name="name">Ajout de session</field>
        <field name="res_model">openacademy.create.session.wizard</field>
        <field name="view_mode">form</field>
        <field name="target">new</field>
        <!-- Cette fois, l'action apparaît sur les COURS -->
        <field name="binding_model_id" ref="openacademy.model_openacademy_course"/>
        <field name="binding_view_types">form</field>
    </record>
</odoo>
```

### `security/ir.model.access.csv` (ajout)

```csv
access_create_session_wizard,create session wizard,model_openacademy_create_session_wizard,group_openacademy_user,1,1,1,0
```

### Deux façons de définir une valeur par défaut

| | `default=méthode` sur le champ | `default_get()` |
|---|---|---|
| Portée | Un champ | Plusieurs champs d'un coup |
| Quand l'utiliser | Cas simple (exercice 12.1) | Quand les valeurs dépendent les unes des autres ou du contexte |

---

[⬅ Retour au chapitre](../chapitres/12-wizards.md)
