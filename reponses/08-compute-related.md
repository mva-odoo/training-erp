# Réponses — Chapitre 8

[⬅ Retour au chapitre](../chapitres/08-compute-related.md)

---

## Exercice 8.1

**Nombre de participants.**

```python
from odoo import api, fields, models


class Session(models.Model):
    _name = 'openacademy.session'
    # ... champs existants ...

    # compute : le nom de la méthode qui calcule la valeur.
    # Pas de store : la valeur est recalculée à chaque affichage, rien en base.
    attendee_count = fields.Integer(
        string='Participants',
        compute='_compute_attendee_count',
    )

    # @api.depends('attendee_ids') : dès que la liste des participants change
    # (même dans le formulaire, avant d'enregistrer), Odoo rappelle la méthode.
    @api.depends('attendee_ids')
    def _compute_attendee_count(self):
        # self peut contenir plusieurs sessions (une liste de 80 sessions = un seul appel)
        for session in self:
            # len() sur un recordset = nombre d'enregistrements
            session.attendee_count = len(session.attendee_ids)
```

Dans les vues : `<field name="attendee_count"/>` dans la liste et dans le formulaire.

**Pourquoi ça se met à jour avant d'enregistrer ?** Le formulaire envoie chaque modification au serveur
(appel `onchange`), qui recalcule les champs dont les dépendances ont changé. C'est `@api.depends`
qui rend ce mécanisme automatique.

---

## Exercice 8.2

**Âge des contacts.**

```python
from dateutil.relativedelta import relativedelta

from odoo import api, fields, models


class ResPartner(models.Model):
    _inherit = 'res.partner'

    birth_date = fields.Date(string='Date de naissance')

    # store=True : la valeur est enregistrée en base → on peut trier et grouper dessus.
    age = fields.Integer(string='Âge', compute='_compute_age', store=True)

    @api.depends('birth_date')
    def _compute_age(self):
        today = fields.Date.today()
        for partner in self:
            if partner.birth_date:
                # relativedelta calcule la différence en années, mois, jours
                # en tenant compte des années bissextiles. .years = années entières.
                partner.age = relativedelta(today, partner.birth_date).years
            else:
                # OBLIGATOIRE : chaque enregistrement doit recevoir une valeur.
                # Sans ce else, Odoo lève "Compute method failed to assign".
                partner.age = 0
```

Dans la vue recherche des contacts (vue héritée), le regroupement :

```xml
<record id="view_res_partner_filter_inherit_openacademy" model="ir.ui.view">
    <field name="name">res.partner.search.inherit.openacademy</field>
    <field name="model">res.partner</field>
    <field name="inherit_id" ref="base.view_res_partner_filter"/>
    <field name="arch" type="xml">
        <xpath expr="//group" position="inside">
            <filter name="group_age" string="Âge" context="{'group_by': 'age'}"/>
        </xpath>
    </field>
</record>
```

### Le piège

Avec `store=True`, l'âge n'est recalculé **que quand `birth_date` change**. Quelqu'un né le 15 mars
aura toujours 20 ans en base le 16 mars de l'année suivante. Solutions possibles :

| Solution | Avantage | Inconvénient |
|---|---|---|
| Ne pas stocker | Toujours juste | Pas de tri ni de regroupement |
| Stocker + **action planifiée** quotidienne qui recalcule | Juste et groupable | Une tâche de plus à maintenir |
| Grouper par **année de naissance** (`birth_date:year` dans `group_by`) | Rien à coder, toujours juste | Ce n'est pas exactement « l'âge » |

C'est une excellente question d'oral : il n'y a pas de bonne réponse unique, il faut savoir **expliquer le compromis**.

---

## Exercice 8.3

**Date de fin ↔ durée.**

```python
from datetime import timedelta

from odoo import api, fields, models


class Session(models.Model):
    # ...

    start_date = fields.Date(string='Date de début', default=fields.Date.today)
    duration = fields.Float(string='Durée (jours)', digits=(6, 1), default=1)

    # compute + inverse + store :
    #  - compute  : calculée à partir du début et de la durée
    #  - inverse  : si l'utilisateur la modifie, on recalcule la durée
    #  - store    : stockée, pour le calendrier, les filtres et le tri
    end_date = fields.Date(
        string='Date de fin',
        compute='_compute_end_date',
        inverse='_inverse_end_date',
        store=True,
    )

    @api.depends('start_date', 'duration')
    def _compute_end_date(self):
        for session in self:
            if session.start_date and session.duration:
                # Une session d'1 jour commence et finit le même jour : on retire 1.
                # On arrondit vers le haut : 1.5 jour = 2 jours calendrier.
                days = max(int(session.duration + 0.99) - 1, 0)
                session.end_date = session.start_date + timedelta(days=days)
            else:
                session.end_date = session.start_date

    def _inverse_end_date(self):
        for session in self:
            if session.start_date and session.end_date:
                # Le calcul inverse : du nombre de jours entre les deux dates, on déduit la durée.
                session.duration = (session.end_date - session.start_date).days + 1
```

### Ce qu'il faut comprendre

- Sans `inverse`, `end_date` serait en **lecture seule** dans le formulaire.
- L'`inverse` est appelé **à l'enregistrement**, quand l'utilisateur a modifié `end_date`.
- On a supprimé une **source d'incohérence** : avant, on pouvait saisir « du 1er au 3 mars, durée 10 jours ».

> Si vous aviez la contrainte « fin après début » du chapitre 6, elle reste utile : l'utilisateur peut encore
> saisir une date de fin avant le début, ce qui donnerait une durée négative.

---

## Exercice 8.4

**Responsable du cours sur la session.**

```python
    # related : suit course_id puis responsible_id.
    # store=True : nécessaire pour GROUPER par responsable (on ne groupe que sur des colonnes).
    # Odoo met la valeur à jour automatiquement si le cours change de responsable.
    responsible_id = fields.Many2one(
        related='course_id.responsible_id',
        string='Responsable',
        store=True,
    )
```

On n'a pas besoin de répéter `'res.users'` : le type est déduit du champ cible.

Dans la vue recherche des sessions :

```xml
<filter name="group_responsible" string="Responsable"
        context="{'group_by': 'responsible_id'}"/>
```

**Pourquoi pas un simple `Many2one` rempli à la création ?** Parce que si le responsable du cours change,
toutes les sessions afficheraient l'ancien. Le `related` garantit une **seule source de vérité** : le cours.

---

## Exercice 8.5

**Taux d'occupation.**

```python
    max_participants = fields.Integer(string='Places', default=10)

    seat_ratio = fields.Float(string="Taux d'occupation", compute='_compute_seat_ratio')

    # Dépend des places ET des participants
    @api.depends('max_participants', 'attendee_ids')
    def _compute_seat_ratio(self):
        for session in self:
            if session.max_participants:
                session.seat_ratio = 100.0 * len(session.attendee_ids) / session.max_participants
            else:
                # Pas de places définies : on évite la division par zéro
                session.seat_ratio = 0.0

    # L'avertissement ne peut venir que d'un onchange : un compute ne peut rien "afficher".
    @api.onchange('max_participants', 'attendee_ids')
    def _onchange_check_seats(self):
        # Dans un onchange, self est TOUJOURS un seul enregistrement (celui du formulaire)
        if self.max_participants < 0:
            return {
                'warning': {
                    'title': "Nombre de places incorrect",
                    'message': "Le nombre de places ne peut pas être négatif.",
                }
            }
        if self.max_participants and len(self.attendee_ids) > self.max_participants:
            return {
                'warning': {
                    'title': "Session complète",
                    'message': (
                        f"{len(self.attendee_ids)} participants pour "
                        f"{self.max_participants} places."
                    ),
                }
            }
```

Dans le formulaire :

```xml
<field name="max_participants"/>
<field name="seat_ratio" widget="progressbar"/>
```

> L'avertissement **n'empêche pas** d'enregistrer : il prévient. Pour **interdire** le dépassement,
> il faudrait une contrainte `@api.constrains` (chapitre 6). C'est un choix fonctionnel à discuter avec le client.

---

## Exercice 8.6

**Boutons intelligents.**

### 1. Sur le cours — `models/course.py`

```python
from odoo import api, fields, models


class Course(models.Model):
    _name = 'openacademy.course'

    # Le compteur affiché dans le bouton. Non stocké : recalculé à l'affichage.
    session_count = fields.Integer(string='Nombre de sessions', compute='_compute_session_count')

    @api.depends('session_ids')
    def _compute_session_count(self):
        for course in self:
            course.session_count = len(course.session_ids)

    def action_view_sessions(self):
        # Appelée par un bouton type="object" : self = le cours affiché (un seul).
        self.ensure_one()
        # On RENVOIE une action (un dictionnaire) : le client web l'exécute,
        # exactement comme une action définie en XML.
        return {
            'type': 'ir.actions.act_window',
            'name': 'Sessions',
            'res_model': 'openacademy.session',
            'view_mode': 'list,form',
            # domain : seulement les sessions de CE cours
            'domain': [('course_id', '=', self.id)],
            # context : une session créée depuis cette liste aura ce cours pré-rempli
            'context': {'default_course_id': self.id},
        }
```

### Formulaire du cours

```xml
<sheet>
    <!--
        oe_button_box : la zone des boutons intelligents, en haut à droite.
        name="button_box" : permet à d'autres modules d'y ajouter leurs boutons par héritage.
        Elle doit être le PREMIER élément de la sheet.
    -->
    <div class="oe_button_box" name="button_box">
        <!-- type="object" : appelle action_view_sessions
             oe_stat_button : le style "compteur"
             icon : une icône Font Awesome 4 -->
        <button name="action_view_sessions" type="object"
                class="oe_stat_button" icon="fa-calendar">
            <!-- statinfo : affiche la valeur en grand et le libellé en dessous -->
            <field name="session_count" widget="statinfo" string="Sessions"/>
        </button>
    </div>

    <div class="oe_title">
        <h1><field name="name"/></h1>
    </div>
    ...
</sheet>
```

### 2. Sur la session

Le compteur existe déjà : c'est `attendee_count` (exercice 8.1).

```python
def action_view_attendees(self):
    self.ensure_one()
    return {
        'type': 'ir.actions.act_window',
        'name': 'Participants',
        'res_model': 'res.partner',
        'view_mode': 'list,form',
        # Les contacts dont l'id fait partie des participants de la session
        'domain': [('id', 'in', self.attendee_ids.ids)],
    }
```

```xml
<div class="oe_button_box" name="button_box">
    <button name="action_view_attendees" type="object"
            class="oe_stat_button" icon="fa-users">
        <field name="attendee_count" widget="statinfo" string="Participants"/>
    </button>
</div>
```

> Pas de `default_...` ici : la liste montre des contacts existants. Un contact créé depuis cette liste
> ne serait **pas** inscrit à la session (le domaine filtre, il n'inscrit pas).

---

## Exercice 8.7

**Clôture automatique.**

### `data/cron.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<!-- noupdate : si l'administrateur change la fréquence ou désactive l'action,
     une mise à jour du module ne doit pas écraser son choix -->
<odoo noupdate="1">
    <record id="cron_close_sessions" model="ir.cron">
        <field name="name">Open Academy : clôturer les sessions passées</field>
        <!-- Le modèle sur lequel le code s'exécute -->
        <field name="model_id" ref="model_openacademy_session"/>
        <!-- state=code : l'action exécute du code Python -->
        <field name="state">code</field>
        <!-- model = un recordset vide de openacademy.session.
             On appelle une méthode du modèle plutôt que d'écrire la logique ici :
             le code reste dans le Python, versionné et testable. -->
        <field name="code">model._cron_close_past_sessions()</field>
        <!-- Toutes les 1 journée -->
        <field name="interval_number">1</field>
        <field name="interval_type">days</field>
        <field name="active" eval="True"/>
    </record>
</odoo>
```

```python
# __manifest__.py
'data': [
    # ...
    'data/cron.xml',
],
```

### `models/session.py`

```python
    # @api.model : la méthode est appelée sur le MODÈLE (recordset vide), pas sur des sessions précises.
    # Préfixe _cron_ : convention pour reconnaître les méthodes appelées par une action planifiée.
    # Le "_" initial la rend privée : elle ne peut pas être appelée depuis l'interface web.
    @api.model
    def _cron_close_past_sessions(self):
        sessions = self.search([
            ('state', '=', 'confirm'),                    # seulement les sessions confirmées
            ('end_date', '<', fields.Date.today()),       # dont la fin est passée
        ])
        # write sur tout le recordset : une seule requête, même pour 500 sessions
        sessions.write({'state': 'done'})
```

> On n'appelle pas `action_done()` : au chapitre 9, elle exige d'être responsable. Une action planifiée
> tourne avec l'utilisateur **OdooBot** (superutilisateur), mais écrire directement l'état est plus clair
> sur l'intention : c'est le système qui clôture, pas un utilisateur.

### Bonus : recalculer l'âge

```python
# models/res_partner.py
@api.model
def _cron_recompute_age(self):
    partners = self.search([('birth_date', '!=', False)])
    # On relance le calcul. Comme age est stocké, la nouvelle valeur est écrite en base.
    partners._compute_age()
```

```xml
<record id="cron_recompute_age" model="ir.cron">
    <field name="name">Open Academy : recalculer l'âge des contacts</field>
    <field name="model_id" ref="base.model_res_partner"/>
    <field name="state">code</field>
    <field name="code">model._cron_recompute_age()</field>
    <field name="interval_number">1</field>
    <field name="interval_type">days</field>
</record>
```

C'est la solution « stocker + action planifiée » du tableau de l'exercice 8.2 : l'âge est groupable **et** juste,
au prix d'une tâche de plus à maintenir.

---

## Exercice 8.8

**Défi.**

### 1. Nom automatique

```python
# En haut du fichier
from odoo.tools import format_date

    # display_name existe sur TOUS les modèles : on surcharge sa méthode de calcul.
    # Les dépendances sont indispensables : sans elles, le nom ne se mettrait pas à jour
    # quand on change le cours ou la date.
    @api.depends('course_id.name', 'start_date')
    def _compute_display_name(self):
        for session in self:
            # format_date : la date au format de la langue de l'utilisateur (10/10/2026 en français)
            date_str = format_date(self.env, session.start_date) if session.start_date else ''
            # "or ''" : une session sans cours afficherait "False" sinon
            session.display_name = f"Session {session.course_id.name or ''} – {date_str}"
```

> Avant Odoo 17, on surchargeait `name_get()`. Cette méthode n'existe plus.

### 2. Prix

```python
# Dans course.py
    price_per_participant = fields.Float(string='Prix par participant')

# Dans session.py
    total_price = fields.Float(
        string='Chiffre d\'affaires',
        compute='_compute_total_price',
        store=True,                        # stocké : utilisable comme mesure dans le graphique
    )

    # On dépend d'un champ d'un AUTRE modèle (le cours) : si le prix du cours change,
    # Odoo recalcule automatiquement toutes ses sessions.
    @api.depends('attendee_ids', 'course_id.price_per_participant')
    def _compute_total_price(self):
        for session in self:
            session.total_price = len(session.attendee_ids) * session.course_id.price_per_participant
```

```xml
<graph type="bar">
    <field name="course_id"/>
    <field name="total_price" type="measure"/>   <!-- la mesure : la hauteur des barres -->
</graph>
```

### 3. `estate`

```python
    total_area = fields.Integer(string='Surface totale (m²)', compute='_compute_total_area')

    @api.depends('living_area', 'garden_area')
    def _compute_total_area(self):
        for prop in self:
            prop.total_area = prop.living_area + prop.garden_area
```

---

[⬅ Retour au chapitre](../chapitres/08-compute-related.md)
