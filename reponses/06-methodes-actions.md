# Réponses — Chapitre 6

[⬅ Retour au chapitre](../chapitres/06-methodes-actions.md)

---

## Exercice 6.1

**État et boutons sur les sessions.**

### `models/session.py` (ajouts)

```python
from odoo import fields, models


class Session(models.Model):
    _name = 'openacademy.session'
    _description = 'Session de formation'

    # ... champs existants ...

    state = fields.Selection(
        selection=[
            ('draft', 'Brouillon'),
            ('confirm', 'Confirmé'),
            ('done', 'Terminé'),
            ('cancel', 'Annulé'),
        ],
        string='État',
        default='draft',     # toute nouvelle session commence en brouillon
        required=True,
        readonly=True,       # impossible à modifier à la main : seuls les boutons changent l'état
        copy=False,          # une session dupliquée repart en brouillon
    )

    # Convention Odoo : les méthodes appelées par des boutons commencent par action_.
    # self est un RECORDSET : depuis la liste, l'utilisateur peut sélectionner
    # plusieurs sessions et appeler l'action sur toutes en même temps.

    def action_confirm(self):
        # write() sur le recordset entier : une seule requête SQL pour tous les enregistrements.
        # Équivalent à "for rec in self: rec.state = 'confirm'", en plus efficace.
        self.write({'state': 'confirm'})

    def action_done(self):
        self.write({'state': 'done'})

    def action_cancel(self):
        self.write({'state': 'cancel'})

    def action_set_draft(self):
        self.write({'state': 'draft'})
```

> `readonly=True` sur le champ ne bloque que **l'interface**. Le code Python, lui, peut toujours écrire
> dans le champ : c'est ce que font les méthodes `action_*`.

### `views/session_views.xml` — formulaire (extrait)

```xml
<form string="Session">
    <!-- header : la barre au-dessus de la feuille, pour les boutons et l'état -->
    <header>
        <!--
            type="object" : appelle la méthode Python du même nom.
            invisible     : expression évaluée sur l'enregistrement affiché.
                            Le bouton est CACHÉ quand l'expression est vraie.
            class="btn-primary" : le bouton principal, mis en évidence (violet)
        -->
        <button name="action_confirm" type="object" string="Confirmer"
                class="btn-primary" invisible="state != 'draft'"/>

        <button name="action_done" type="object" string="Terminer"
                class="btn-primary" invisible="state != 'confirm'"/>

        <!-- "not in" : visible en brouillon ET en confirmé -->
        <button name="action_cancel" type="object" string="Annuler"
                invisible="state not in ('draft', 'confirm')"/>

        <button name="action_set_draft" type="object" string="Remettre en brouillon"
                invisible="state != 'cancel'"/>

        <!--
            widget="statusbar" : affiche l'état comme une suite d'étapes, à droite.
            statusbar_visible  : les étapes toujours affichées. "cancel" n'y est pas :
                                 il n'apparaît que si la session est annulée.
            Le champ étant readonly, l'utilisateur ne peut pas cliquer sur les étapes.
        -->
        <field name="state" widget="statusbar" statusbar_visible="draft,confirm,done"/>
    </header>
    <sheet>
        <!-- ... contenu du chapitre 5 ... -->
    </sheet>
</form>
```

**Bonus** dans la liste : afficher l'état sous forme de badge coloré.

```xml
<field name="state" widget="badge"
       decoration-info="state == 'draft'"
       decoration-success="state == 'done'"
       decoration-danger="state == 'cancel'"/>
```

---

## Exercice 6.2

**Explorer avec le shell.**

```python
# 1. Les noms des cours de niveau basique.
#    search() renvoie un recordset ; mapped() extrait un champ de chaque enregistrement.
>>> env['openacademy.course'].search([('level', '=', 'basic')]).mapped('name')
['Python', 'Excel']

# 2. Une session pour le premier cours trouvé, avec trois participants.
#    limit=1 : on ne récupère qu'un enregistrement.
>>> course = env['openacademy.course'].search([], limit=1)
>>> contacts = env['openacademy.contact'].search([], limit=3)
>>> session = env['openacademy.session'].create({
...     'name': 'Session test',
...     'course_id': course.id,                 # un Many2one attend un ID (entier)
...     'attendee_ids': [(6, 0, contacts.ids)], # un Many2many attend une liste de COMMANDES
... })
# (6, 0, ids) = "remplace la liste par ces ids". Version lisible :
#   from odoo import Command
#   'attendee_ids': [Command.set(contacts.ids)]

# 3. Toutes les sessions en brouillon, d'un seul appel.
#    write() s'applique à tout le recordset : une seule requête SQL.
>>> env['openacademy.session'].search([]).write({'state': 'draft'})
True

# 4. Les sessions qui commencent ce mois-ci.
>>> from datetime import date
>>> today = date.today()
>>> env['openacademy.session'].search_count([
...     ('start_date', '>=', today.replace(day=1)),   # ET implicite entre les deux conditions
...     ('start_date', '<=', today),
... ])
4
# (Pour tout le mois, utilisez dateutil.relativedelta pour le dernier jour.)

# 5. Sans commit, la transaction est annulée à la fermeture du shell.
>>> env.cr.commit()
```

**Question 5 :** Odoo travaille dans une **transaction** PostgreSQL. Dans l'interface, chaque clic est
une transaction validée automatiquement. Dans le shell, c'est vous qui décidez : sans `env.cr.commit()`,
tout est annulé (`rollback`) à la fermeture. C'est pratique pour tester sans risque.

---

## Exercice 6.3

**Dupliquer un cours.**

```python
from odoo import api, fields, models


class Course(models.Model):
    _name = 'openacademy.course'
    _description = 'Cours'

    # ... champs ...

    def copy(self, default=None):
        # default peut être None : on en fait un dictionnaire (copie, pour ne pas
        # modifier le dictionnaire de l'appelant).
        default = dict(default or {})

        # Si l'appelant a déjà imposé un nom, on le respecte.
        if 'name' not in default:
            base_name = f"Copie de {self.name}"

            # --- Version de base : ---
            # default['name'] = base_name

            # --- Bonus : numéroter si le nom existe déjà ---
            # =like : comme LIKE en SQL, sensible à la casse, sans ajouter de % automatiquement.
            # On compte les cours qui s'appellent "Copie de X" ou "Copie de X (n)".
            count = self.search_count([('name', '=like', f"{base_name}%")])
            default['name'] = base_name if count == 0 else f"{base_name} ({count + 1})"

        # super() fait la vraie copie, avec nos valeurs par défaut
        return super().copy(default)
```

> ⚠️ `self.name` suppose que `self` contient **un seul** cours. Dans l'interface, **Dupliquer** agit
> sur un enregistrement à la fois. Depuis Odoo 17, `copy()` peut recevoir plusieurs enregistrements :
> une version robuste surchargerait plutôt `copy_data()`. Pour cet exercice, la version simple suffit.

**Pourquoi ça casserait sans cette surcharge :** avec la contrainte d'unicité de l'exercice 6.4,
dupliquer un cours tenterait de créer un deuxième « Python » → erreur PostgreSQL.

---

## Exercice 6.4

**Contraintes.**

### `models/course.py`

```python
class Course(models.Model):
    _name = 'openacademy.course'

    # Liste de tuples : (nom de la contrainte, définition SQL, message d'erreur)
    # Odoo crée en base : ALTER TABLE openacademy_course ADD CONSTRAINT
    #                     openacademy_course_name_unique UNIQUE(name)
    _sql_constraints = [
        ('name_unique', 'UNIQUE(name)', "Un cours avec ce titre existe déjà."),
    ]
```

### `models/session.py`

```python
from odoo import api, fields, models
from odoo.exceptions import ValidationError


class Session(models.Model):
    _name = 'openacademy.session'

    _sql_constraints = [
        ('name_unique', 'UNIQUE(name)', "Une session avec ce nom existe déjà."),
    ]

    # @api.constrains : la méthode est appelée après chaque create/write
    # qui touche start_date OU end_date.
    @api.constrains('start_date', 'end_date')
    def _check_dates(self):
        for session in self:
            # On ne compare que si les deux dates sont remplies :
            # une date vide (False) ne peut pas être comparée à une date.
            if session.start_date and session.end_date \
                    and session.end_date < session.start_date:
                # ValidationError : annule l'enregistrement et affiche le message
                raise ValidationError(
                    f"La session « {session.name} » se termine avant de commencer."
                )

    # Bonus : pas deux sessions du même cours le même jour
    @api.constrains('course_id', 'start_date')
    def _check_unique_course_date(self):
        for session in self:
            duplicate = self.search_count([
                ('id', '!=', session.id),                     # pas soi-même !
                ('course_id', '=', session.course_id.id),
                ('start_date', '=', session.start_date),
            ])
            if duplicate:
                raise ValidationError(
                    "Une session de ce cours commence déjà à cette date."
                )
```

### Pourquoi le bonus n'est pas une contrainte SQL

On **pourrait** écrire `UNIQUE(course_id, start_date)` en SQL, et ce serait même plus fiable
(PostgreSQL vérifie aussi en cas d'accès concurrents). La contrainte Python est utile quand la règle
devient plus complexe : « pas deux sessions du même cours qui **se chevauchent** », par exemple,
ce que `UNIQUE` ne sait pas exprimer.

| Règle | Choix |
|---|---|
| Unicité simple, valeur positive, plage de valeurs | **SQL** : vérifié par la base, impossible à contourner |
| Comparaison entre champs, règle qui consulte d'autres enregistrements, message personnalisé | **Python** |

---

## Exercice 6.5

**Une référence pour chaque session.**

### `data/sequence.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--
    noupdate="1" : la séquence est créée à l'installation, puis plus jamais rechargée.
    Sans cela, chaque mise à jour du module réinitialiserait sa configuration
    (et un administrateur qui aurait changé le préfixe perdrait sa modification).
-->
<odoo noupdate="1">
    <record id="seq_openacademy_session" model="ir.sequence">
        <field name="name">Session de formation</field>
        <!-- code : la "clé" utilisée dans next_by_code(). Convention : le nom du modèle. -->
        <field name="code">openacademy.session</field>
        <!-- %(year)s est remplacé par l'année en cours à chaque nouveau numéro -->
        <field name="prefix">SESS/%(year)s/</field>
        <!-- padding : nombre de chiffres, complétés par des zéros (1 → 0001) -->
        <field name="padding">4</field>
        <field name="company_id" eval="False"/>   <!-- partagée par toutes les sociétés -->
    </record>
</odoo>
```

```python
# __manifest__.py
'data': [
    'security/security.xml',
    'security/ir.model.access.csv',
    'data/sequence.xml',          # données : avant ou après les vues, peu importe ici
    # ...
],
```

### `models/session.py`

```python
from odoo import api, fields, models


class Session(models.Model):
    _name = 'openacademy.session'
    # _rec_name n'est pas modifié : le nom affiché reste "name" (ou display_name au chapitre 8)

    reference = fields.Char(
        string='Référence',
        readonly=True,       # attribuée par le système, jamais saisie
        copy=False,          # une copie ne reprend pas la référence de l'original
        default='Nouveau',   # valeur affichée tant que la session n'est pas enregistrée
        index=True,          # on recherche souvent par référence
    )

    @api.model_create_multi
    def create(self, vals_list):
        # vals_list : une liste de dictionnaires (une création peut en faire plusieurs d'un coup)
        for vals in vals_list:
            # On n'écrase pas une référence fournie explicitement (import, autre module)
            if vals.get('reference', 'Nouveau') == 'Nouveau':
                # next_by_code : lit la séquence dont le code est 'openacademy.session',
                # renvoie le prochain numéro formaté ("SESS/2026/0007") et incrémente le compteur.
                vals['reference'] = self.env['ir.sequence'].next_by_code('openacademy.session')
        # super() fait la vraie création, avec les références ajoutées
        return super().create(vals_list)
```

### Vues

```xml
<!-- Formulaire : la référence en petit, au-dessus du nom -->
<div class="oe_title">
    <div class="text-muted"><field name="reference"/></div>
    <h1><field name="name" placeholder="Nom de la session"/></h1>
</div>
```

```xml
<!-- Liste : en première colonne -->
<list>
    <field name="reference"/>
    <field name="name"/>
    ...
</list>
```

Et dans la vue recherche, pour retrouver une session par sa référence : `<field name="reference"/>`.

### Pourquoi à la création et pas en valeur par défaut ?

Si on mettait `default=lambda self: self.env['ir.sequence'].next_by_code(...)`, un numéro serait consommé
**à chaque ouverture** du formulaire de création, même si l'utilisateur annule. On aurait des trous
dans la numérotation. En l'attribuant dans `create`, seul un enregistrement réellement créé reçoit un numéro.

---

## Exercice 6.6

**Défi `estate`.**

```python
from odoo import fields, models
from odoo.exceptions import UserError


class EstateProperty(models.Model):
    _name = 'estate.property'

    # ...

    _sql_constraints = [
        # CHECK : une condition que chaque ligne doit respecter
        ('check_expected_price', 'CHECK(expected_price > 0)',
         "Le prix demandé doit être strictement positif."),
    ]

    def action_sold(self):
        for prop in self:
            if prop.state == 'canceled':
                # UserError : une erreur "métier", affichée proprement à l'utilisateur
                raise UserError("Un bien annulé ne peut pas être vendu.")
        self.write({'state': 'sold'})

    def action_cancel(self):
        for prop in self:
            if prop.state == 'sold':
                raise UserError("Un bien vendu ne peut pas être annulé.")
        self.write({'state': 'canceled'})
```

```python
class EstatePropertyTag(models.Model):
    _name = 'estate.property.tag'

    _sql_constraints = [
        ('name_unique', 'UNIQUE(name)', "Cette étiquette existe déjà."),
    ]
```

> **Pourquoi vérifier en Python alors qu'on cache déjà les boutons ?** Cacher un bouton n'empêche pas
> d'appeler la méthode (depuis la liste avec plusieurs biens sélectionnés, depuis l'API, depuis un autre module).
> La vue sert le **confort**, le Python garantit la **règle**.

---

[⬅ Retour au chapitre](../chapitres/06-methodes-actions.md)
