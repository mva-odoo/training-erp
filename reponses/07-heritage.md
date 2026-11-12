# Réponses — Chapitre 7

[⬅ Retour au chapitre](../chapitres/07-heritage.md)

---

## Exercice 7.1

**Utiliser les contacts d'Odoo.**

### Pourquoi ce changement ?

C'est le fil rouge du cours : **ne pas recréer ce qui existe**. `res.partner` a déjà un nom, une photo,
une adresse, un e-mail, un téléphone, une société, des étiquettes… Il est utilisé par les ventes,
la facturation, le CRM. Un participant enregistré comme `res.partner` peut recevoir une facture
sans aucun développement supplémentaire. Notre `openacademy.contact` ne le pourrait jamais.

### Ce qu'on supprime

- `models/contact.py` et sa ligne dans `models/__init__.py`
- `views/contact_views.xml` et sa ligne dans le manifest
- la ligne `access_openacademy_contact` du CSV

### `models/__init__.py`

```python
from . import course
from . import session
from . import res_partner     # nouveau : l'extension de res.partner
```

### `models/res_partner.py`

```python
from odoo import fields, models


class ResPartner(models.Model):
    # Pas de _name : on ne crée pas de modèle, on MODIFIE res.partner.
    # Odoo ajoute nos champs à la classe existante et nos colonnes à la table res_partner.
    _inherit = 'res.partner'

    is_instructor = fields.Boolean(string='Professeur', default=False)

    # Le même Many2many que attendee_ids sur la session, vu depuis le contact.
    # On réutilise la table de jonction déclarée dans session.py.
    session_ids = fields.Many2many(
        'openacademy.session',
        relation='openacademy_session_partner_rel',
        column1='partner_id',
        column2='session_id',
        string='Sessions suivies',
    )
```

> Pas besoin de ligne dans `ir.model.access.csv` : `res.partner` a déjà ses droits, définis par le module `base`.

### `models/session.py` (champs modifiés)

```python
    # Le professeur est maintenant un res.partner.
    # domain : seuls les contacts cochés "professeur" sont proposés dans la liste déroulante.
    # C'est un filtre d'INTERFACE : il ne valide pas les données écrites par du code.
    instructor_id = fields.Many2one(
        'res.partner',
        string='Professeur',
        domain=[('is_instructor', '=', True)],
    )

    attendee_ids = fields.Many2many(
        'res.partner',
        relation='openacademy_session_partner_rel',   # nouveau nom de table : nouveau modèle cible
        column1='session_id',
        column2='partner_id',
        string='Participants',
    )
```

### `views/menus.xml`

```xml
<!--
    On réutilise l'action standard des contacts, définie dans le module "contacts".
    Le préfixe "contacts." indique le module qui la définit.
    → il faut ajouter 'contacts' dans les depends du manifest.
-->
<menuitem id="menu_contact" name="Contacts" parent="menu_root"
          action="contacts.action_contacts" sequence="30"/>
```

### `__manifest__.py`

```python
'depends': ['base', 'contacts'],
```

> Variante sans dépendre de `contacts` : créer votre propre action sur `res.partner`, avec un domaine
> `[('is_instructor', '=', True)]` pour un menu « Professeurs ». C'est même plus utile pour l'utilisateur.

---

## Exercice 7.2

**Afficher `is_instructor` dans le formulaire contact.**

### 1. Trouver l'ID de la vue

Contacts → ouvrir un contact → 🐞 → **Modifier la vue : Formulaire** → champ *ID externe* :
**`base.view_partner_form`**. Dans l'onglet *Architecture*, on repère le champ `function`
et le `notebook`.

### 2 et 3. `views/res_partner_views.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<odoo>
    <record id="view_partner_form_inherit_openacademy" model="ir.ui.view">
        <!-- Convention de nommage : <modèle>.<type>.inherit.<votre module> -->
        <field name="name">res.partner.form.inherit.openacademy</field>
        <field name="model">res.partner</field>

        <!-- La vue parente. Le préfixe "base." : elle est définie dans le module base. -->
        <field name="inherit_id" ref="base.view_partner_form"/>

        <!-- arch ne contient PAS une vue complète, seulement la liste des modifications -->
        <field name="arch" type="xml">

            <!-- Modification 1 : ajouter is_instructor juste après le champ "Fonction" -->
            <xpath expr="//field[@name='function']" position="after">
                <field name="is_instructor"/>
            </xpath>

            <!-- Modification 2 : ajouter un onglet à la fin du notebook.
                 inside = "à l'intérieur, à la fin". -->
            <xpath expr="//notebook" position="inside">
                <!-- invisible : l'onglet n'apparaît que si le contact suit des sessions -->
                <page string="Sessions" name="openacademy_sessions"
                      invisible="not session_ids">
                    <field name="session_ids">
                        <list>
                            <field name="name"/>
                            <field name="course_id"/>
                            <field name="start_date"/>
                            <field name="state"/>
                        </list>
                    </field>
                </page>
            </xpath>

        </field>
    </record>
</odoo>
```

Et dans le manifest : `'views/res_partner_views.xml'` dans `data`.

### Vérifier le résultat

🐞 → **Modifier la vue : Formulaire** → onglet **Vues héritées** : votre vue apparaît dans la liste.
Vous pouvez désactiver une vue héritée pour voir le formulaire sans vos modifications.

---

## Exercice 7.3

**Le chatter sur les sessions.**

### `models/session.py`

```python
class Session(models.Model):
    _name = 'openacademy.session'
    _description = 'Session de formation'

    # _name ET _inherit : on garde NOTRE modèle (openacademy.session),
    # et on y ajoute le comportement des deux mixins.
    # mail.thread         : messages, abonnés, suivi des modifications
    # mail.activity.mixin : activités planifiées (appel, réunion, à faire…)
    _inherit = ['mail.thread', 'mail.activity.mixin']

    # tracking=True : chaque modification de ce champ est notée dans le chatter
    # ("État : Brouillon → Confirmé"), avec l'auteur et la date.
    state = fields.Selection([...], tracking=True)
    instructor_id = fields.Many2one('res.partner', ..., tracking=True)
```

### `__manifest__.py`

```python
# mail.thread et mail.activity.mixin sont définis dans le module mail
'depends': ['base', 'contacts', 'mail'],
```

### Formulaire de la session

```xml
<form string="Session">
    <header>...</header>
    <sheet>...</sheet>
    <!-- APRÈS la sheet, au même niveau : Odoo l'affiche à droite (grand écran) ou en dessous -->
    <chatter/>
</form>
```

> Les mixins ajoutent des **modèles** à votre module (`mail.followers`, `mail.message`…). Ils ont déjà leurs
> droits d'accès : rien à ajouter dans le CSV.

---

## Exercice 7.4

**Étendre un module métier.**

### De quel module dépendre ?

`sale.order` est défini dans le module **`sale`**. Sans `'sale'` dans `depends` :
- Odoo pourrait charger votre module **avant** `sale`, et `_inherit = 'sale.order'` échouerait
  (« model sale.order does not exist ») ;
- votre module pourrait être installé sur une base **sans** l'application Ventes.

```python
'depends': ['base', 'contacts', 'mail', 'sale'],
```

> Remarquez que c'est une décision **fonctionnelle** : votre module impose désormais l'application Ventes
> à tous ses utilisateurs. Dans un vrai projet, on mettrait cette partie dans un petit module séparé
> (`openacademy_sale`) qui dépend des deux. C'est une question classique d'oral.

### `models/sale_order.py`

```python
from odoo import fields, models


class SaleOrder(models.Model):
    _inherit = 'sale.order'

    session_id = fields.Many2one('openacademy.session', string='Session de formation')
```

### `models/session.py` (ajout)

```python
    # Les commandes dont le session_id pointe vers cette session
    sale_order_ids = fields.One2many('sale.order', 'session_id', string='Commandes')
```

### `views/sale_order_views.xml`

```xml
<odoo>
    <record id="view_order_form_inherit_openacademy" model="ir.ui.view">
        <field name="name">sale.order.form.inherit.openacademy</field>
        <field name="model">sale.order</field>
        <field name="inherit_id" ref="sale.view_order_form"/>
        <field name="arch" type="xml">
            <field name="partner_id" position="after">
                <field name="session_id"/>
            </field>
        </field>
    </record>
</odoo>
```

### Formulaire de la session (ajout dans le `notebook`)

```xml
<page string="Commandes" name="sale_orders">
    <field name="sale_order_ids" readonly="1">
        <list>
            <field name="name"/>
            <field name="partner_id"/>
            <field name="amount_total"/>
            <field name="state"/>
        </list>
    </field>
</page>
```

`readonly="1"` : on consulte les commandes depuis la session, on ne les crée pas ici.
Les commandes se créent dans l'application Ventes, avec tout son processus (devis, confirmation, facturation).

---

[⬅ Retour au chapitre](../chapitres/07-heritage.md)
