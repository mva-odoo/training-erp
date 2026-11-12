# Aide-mémoire ORM

[⬅ Sommaire](../README.md)

## Modèle

```python
from odoo import api, fields, models
from odoo.exceptions import UserError, ValidationError


class Session(models.Model):
    _name = 'openacademy.session'            # nouveau modèle
    _inherit = ['mail.thread']               # mixins (optionnel)
    _description = 'Session'
    _order = 'start_date desc, name'         # tri par défaut
    _rec_name = 'name'                       # champ affiché (par défaut : name)
```

| Héritage | Code | Nouvelle table ? |
|---|---|---|
| Extension | `_inherit = 'res.partner'` | Non |
| Prototype | `_name = 'x'` + `_inherit = 'y'` | Oui |
| Délégation | `_inherits = {'res.partner': 'partner_id'}` | Oui |

## Champs

```python
name = fields.Char(string='Nom', required=True, index=True, copy=False, default='/')
notes = fields.Text()
active = fields.Boolean(default=True)
seats = fields.Integer()
price = fields.Float(digits=(10, 2))
start_date = fields.Date(default=fields.Date.today)
state = fields.Selection([('draft', 'Brouillon'), ('done', 'Terminé')], default='draft')

course_id = fields.Many2one('openacademy.course', ondelete='cascade')
session_ids = fields.One2many('openacademy.session', 'course_id')
tag_ids = fields.Many2many('res.partner.category')

level = fields.Selection(related='course_id.level', store=True)
total = fields.Float(compute='_compute_total', store=True)
```

## Décorateurs

```python
@api.depends('price', 'seats')               # champ calculé
def _compute_total(self):
    for rec in self:
        rec.total = rec.price * rec.seats

@api.constrains('start_date', 'end_date')     # contrainte Python
def _check_dates(self):
    for rec in self:
        if rec.end_date and rec.start_date and rec.end_date < rec.start_date:
            raise ValidationError("La fin doit être après le début.")

@api.onchange('course_id')                   # formulaire uniquement
def _onchange_course_id(self):
    self.name = self.course_id.name

@api.model_create_multi                      # surcharge de create
def create(self, vals_list):
    records = super().create(vals_list)
    return records
```

## CRUD

```python
Course = self.env['openacademy.course']

Course.create({'name': 'SQL'})                       # créer → recordset
Course.search([('level', '=', 'basic')])             # chercher → recordset
Course.search([], order='name', limit=10)
Course.search_count([('level', '=', 'basic')])       # compter → int
Course.browse(5)                                     # par id → recordset
courses.write({'level': 'advanced'})                 # modifier tous
courses.unlink()                                     # supprimer
course.copy({'name': 'Copie'})                       # dupliquer
```

## Domaines

```python
[('name', '=', 'SQL')]
[('seats', '>', 10), ('level', 'in', ['basic', 'medium'])]   # ET
['|', ('level', '=', 'basic'), ('seats', '>', 10)]           # OU
['!', ('active', '=', True)]                                 # NON
[('course_id.name', 'ilike', 'python')]                      # suivre une relation
```

Opérateurs : `=` `!=` `>` `>=` `<` `<=` `in` `not in` `like` `ilike` `=like` `child_of`

## Recordsets

```python
for rec in records: ...                      # itérer
len(records)                                 # taille
if records: ...                              # vide ?
records.ensure_one()                         # erreur si != 1 enregistrement
records.mapped('name')                       # ['SQL', 'Python']
records.mapped('course_id')                  # recordset des cours (sans doublons)
records.filtered(lambda r: r.seats > 10)     # filtrer en Python
records.filtered_domain([('seats', '>', 10)])
records.sorted('name', reverse=True)         # trier
a | b    a & b    a - b                      # union, intersection, différence
```

## Écrire dans un One2many / Many2many

```python
from odoo import Command

rec.write({'tag_ids': [
    Command.create({'name': 'Nouveau'}),     # créer et lier
    Command.link(5),                         # lier un existant
    Command.unlink(5),                       # délier (sans supprimer)
    Command.delete(5),                       # délier et supprimer
    Command.set([1, 2, 3]),                  # remplacer toute la liste
    Command.clear(),                         # tout délier
]})
```

## Environnement

```python
self.env.user                 # utilisateur connecté
self.env.company              # société active
self.env.context              # contexte (dict)
self.env.ref('base.main_company')            # enregistrement par XML ID
self.env.user.has_group('base.group_system') # test de groupe
records.sudo()                # ignorer les droits (prudence !)
records.with_user(user)       # agir en tant qu'un autre utilisateur
records.with_context(key=value)
```

## Exceptions

```python
from odoo.exceptions import UserError, ValidationError, AccessError

raise UserError("Message affiché à l'utilisateur.")
raise ValidationError("Donnée invalide.")         # dans les contraintes
raise AccessError("Accès refusé.")
```
