# Réponses — Chapitre 4

[⬅ Retour au chapitre](../chapitres/04-modeles.md)

---

## Exercice 4.1

**Créer les trois modèles.**

### Arborescence

```
openacademy/
├── __init__.py
├── __manifest__.py
├── models/
│   ├── __init__.py
│   ├── course.py
│   ├── session.py
│   └── contact.py
└── security/
    └── ir.model.access.csv
```

### `__init__.py` (racine du module)

```python
# On importe le dossier models/. Python exécute alors models/__init__.py.
from . import models
```

> Si vous avez gardé le dossier `controllers/` du scaffold, gardez aussi `from . import controllers`.

### `models/__init__.py`

```python
# Un import par fichier. L'ordre n'a pas d'importance ici :
# Odoo résout les relations entre modèles après avoir tout chargé.
from . import course
from . import session
from . import contact
```

### `models/course.py`

```python
# fields : les types de champs · models : la classe de base des modèles
from odoo import fields, models


class Course(models.Model):
    # Le nom technique : c'est lui qu'on utilise partout (relations, CSV, XML, self.env[...])
    _name = 'openacademy.course'
    # Le libellé lisible : affiché dans les messages d'erreur, les journaux, l'interface technique
    _description = 'Cours'

    # required=True : obligatoire dans l'interface ET contrainte NOT NULL en base
    name = fields.Char(string='Titre', required=True)
```

### `models/session.py`

```python
from odoo import fields, models


class Session(models.Model):
    _name = 'openacademy.session'
    _description = 'Session de formation'

    name = fields.Char(required=True)
```

### `models/contact.py`

```python
from odoo import fields, models


class Contact(models.Model):
    _name = 'openacademy.contact'
    _description = 'Contact (participant ou professeur)'

    name = fields.Char(required=True)
```

### `security/ir.model.access.csv`

```csv
id,name,model_id:id,group_id:id,perm_read,perm_write,perm_create,perm_unlink
access_openacademy_course,openacademy.course,model_openacademy_course,base.group_user,1,1,1,1
access_openacademy_session,openacademy.session,model_openacademy_session,base.group_user,1,1,1,1
access_openacademy_contact,openacademy.contact,model_openacademy_contact,base.group_user,1,1,1,1
```

Chaque ligne se lit : « la règle `access_openacademy_course` donne au groupe **utilisateur interne**
(`base.group_user`) le droit de **lire, modifier, créer et supprimer** (`1,1,1,1`) des enregistrements
du modèle `openacademy.course` ».

`model_openacademy_course` est l'**identifiant XML** qu'Odoo crée automatiquement pour chaque modèle :
`model_` + le `_name` avec les points remplacés par des `_`.

### `__manifest__.py` (extrait)

```python
'data': [
    'security/ir.model.access.csv',   # sans cette ligne, le CSV n'est jamais lu
],
```

### Mettre à jour et vérifier

```bash
./odoo-bin -d my_database --addons-path=addons,/home/odoo/src/custo -u openacademy
```

```sql
my_database=> \dt openacademy*
                List of relations
 Schema |        Name         | Type  | Owner
--------+---------------------+-------+-------
 public | openacademy_contact | table | odoo
 public | openacademy_course  | table | odoo
 public | openacademy_session | table | odoo
```

---

## Exercice 4.2

**Ajouter les champs simples.**

### `models/course.py`

```python
from odoo import fields, models


class Course(models.Model):
    _name = 'openacademy.course'
    _description = 'Cours'

    name = fields.Char(string='Titre', required=True)

    # Text et non Char : une description peut faire plusieurs paragraphes.
    # Char est fait pour une ligne (nom, référence) ; Text n'a pas de limite pratique.
    description = fields.Text()

    # Selection : une liste fermée de valeurs.
    # (clé stockée en base, libellé affiché). On stocke 'basic', pas 'Basique' :
    # si on traduit ou renomme le libellé, les données restent valides.
    level = fields.Selection(
        selection=[
            ('basic', 'Basique'),
            ('medium', 'Moyen'),
            ('advanced', 'Avancé'),
        ],
        string='Niveau',
        default='basic',
    )
```

### `models/session.py`

```python
from odoo import fields, models


class Session(models.Model):
    _name = 'openacademy.session'
    _description = 'Session de formation'

    name = fields.Char(required=True)

    # Date et non Datetime : une session commence un jour, l'heure n'a pas d'importance ici.
    # default=fields.Date.today : on passe la FONCTION (sans parenthèses),
    # Odoo l'appelle à chaque création. Avec des parenthèses, la date serait
    # figée au jour du démarrage du serveur.
    start_date = fields.Date(string='Date de début', default=fields.Date.today)
    end_date = fields.Date(string='Date de fin')

    # Float : une durée peut être d'une demi-journée (0.5).
    # digits=(6, 1) : 6 chiffres au total, dont 1 après la virgule.
    duration = fields.Float(string='Durée (jours)', digits=(6, 1))
```

### `models/contact.py`

```python
from odoo import fields, models


class Contact(models.Model):
    _name = 'openacademy.contact'
    _description = 'Contact (participant ou professeur)'

    name = fields.Char(required=True)

    # Image : un Binary spécialisé (redimensionnement, aperçu).
    # max_width / max_height : Odoo redimensionne l'image à l'enregistrement.
    image = fields.Image(string='Photo', max_width=512, max_height=512)

    birth_date = fields.Date(string='Date de naissance')
```

### Pourquoi `image` n'est pas une colonne

```sql
my_database=> \d openacademy_contact
   Column    |            Type
-------------+-----------------------------
 id          | integer
 name        | character varying
 birth_date  | date
 create_uid  | integer
 ...
```

Pas de colonne `image` : les champs binaires sont stockés par défaut dans `ir_attachment`
(et le fichier lui-même dans le *filestore*, sur le disque). Ça évite d'alourdir la table
avec des données volumineuses.

---

## Exercice 4.3

**Relier les modèles.**

### `models/course.py`

```python
from odoo import fields, models


class Course(models.Model):
    _name = 'openacademy.course'
    _description = 'Cours'

    name = fields.Char(string='Titre', required=True)
    description = fields.Text()
    level = fields.Selection(
        [('basic', 'Basique'), ('medium', 'Moyen'), ('advanced', 'Avancé')],
        string='Niveau', default='basic',
    )

    # Many2one vers res.users : un cours a UN responsable,
    # un utilisateur peut être responsable de PLUSIEURS cours.
    # → colonne responsible_id (clé étrangère vers res_users.id)
    # ondelete='set null' : si l'utilisateur est supprimé, le champ est vidé
    # (le cours n'est pas supprimé). C'est la valeur par défaut, écrite ici pour être explicite.
    responsible_id = fields.Many2one(
        'res.users',
        string='Responsable',
        ondelete='set null',
        default=lambda self: self.env.user,   # bonus : l'utilisateur connecté par défaut
    )

    # One2many : "les sessions dont le course_id est ce cours".
    # 1er argument : le modèle cible
    # 2e argument : le nom du Many2one DANS le modèle cible qui pointe vers nous
    # → aucune colonne en base : Odoo fait un SELECT ... WHERE course_id = <id>
    session_ids = fields.One2many('openacademy.session', 'course_id', string='Sessions')
```

### `models/session.py`

```python
from odoo import fields, models


class Session(models.Model):
    _name = 'openacademy.session'
    _description = 'Session de formation'

    name = fields.Char(required=True)
    start_date = fields.Date(string='Date de début', default=fields.Date.today)
    end_date = fields.Date(string='Date de fin')
    duration = fields.Float(string='Durée (jours)', digits=(6, 1))

    # Le pendant du One2many du cours. C'est LUI qui crée la colonne course_id.
    # ondelete='cascade' : si on supprime le cours, ses sessions sont supprimées.
    # Une session sans cours n'a pas de sens.
    course_id = fields.Many2one(
        'openacademy.course', string='Cours', required=True, ondelete='cascade',
    )

    # Un professeur par session ; un professeur donne plusieurs sessions.
    instructor_id = fields.Many2one('openacademy.contact', string='Professeur')

    # Plusieurs participants par session, plusieurs sessions par participant.
    # On NOMME la table de jonction pour pouvoir déclarer le même lien
    # de l'autre côté (dans contact.py) avec la même table.
    attendee_ids = fields.Many2many(
        'openacademy.contact',
        relation='openacademy_session_contact_rel',
        column1='session_id',     # la colonne qui contient l'id de LA SESSION
        column2='contact_id',     # la colonne qui contient l'id DU CONTACT
        string='Participants',
    )

    # Le niveau est une propriété du COURS. Le recopier dans la session créerait
    # deux sources de vérité qui finiraient par diverger.
    # related : Odoo lit course_id.level à chaque affichage.
    level = fields.Selection(related='course_id.level', string='Niveau')
```

### `models/contact.py`

```python
from odoo import fields, models


class Contact(models.Model):
    _name = 'openacademy.contact'
    _description = 'Contact (participant ou professeur)'

    name = fields.Char(required=True)
    image = fields.Image(string='Photo', max_width=512, max_height=512)
    birth_date = fields.Date(string='Date de naissance')

    # Le même Many2many que attendee_ids, vu de l'autre côté :
    # même table, colonnes INVERSÉES (column1 = "moi" = le contact).
    session_ids = fields.Many2many(
        'openacademy.session',
        relation='openacademy_session_contact_rel',
        column1='contact_id',
        column2='session_id',
        string='Sessions suivies',
    )
```

### Ce que vous devez voir en base

```sql
my_database=> \d openacademy_session
    Column     |       Type        | ...
---------------+-------------------+
 id            | integer           |
 name          | character varying |
 start_date    | date              |
 end_date      | date              |
 duration      | numeric           |
 course_id     | integer           |   ← Many2one : vraie colonne
 instructor_id | integer           |   ← Many2one : vraie colonne
 ...
Foreign-key constraints:
    "openacademy_session_course_id_fkey" FOREIGN KEY (course_id)
        REFERENCES openacademy_course(id) ON DELETE CASCADE
    ...

my_database=> \d openacademy_session_contact_rel
   Column   |  Type
------------+---------
 session_id | integer
 contact_id | integer
```

- Pas de colonne `level` dans `openacademy_session` : un `related` non stocké n'existe pas en base.
- Pas de colonne `session_ids` dans `openacademy_course` : un `One2many` est virtuel.
- Pas de colonne `attendee_ids` : un `Many2many` vit dans sa table de jonction.

### L'erreur classique

Sans `relation=...`, Odoo génère un nom de table à partir des deux modèles **dans l'ordre alphabétique**,
ce qui donne la même table des deux côtés… **sauf** quand il y a plusieurs `Many2many` entre les mêmes modèles.
Nommer la table explicitement évite toute ambiguïté et rend le code lisible.

---

## Exercice 4.4

**Agence immobilière.**

### Arborescence

```
estate/
├── __init__.py                  → from . import models
├── __manifest__.py
├── models/
│   ├── __init__.py              → from . import estate_property, estate_property_tag, estate_lead
│   ├── estate_property.py
│   ├── estate_property_tag.py
│   └── estate_lead.py
└── security/
    └── ir.model.access.csv
```

### `__manifest__.py`

```python
{
    'name': 'Immobilier',
    'version': '18.0.1.0.0',
    'summary': "Gestion des biens d'une agence immobilière",
    'license': 'LGPL-3',
    'depends': ['base'],
    'data': [
        'security/ir.model.access.csv',
    ],
    'application': True,
}
```

### `models/estate_property.py`

```python
from odoo import fields, models


class EstateProperty(models.Model):
    _name = 'estate.property'
    _description = 'Bien immobilier'

    name = fields.Char(string='Titre', required=True)
    description = fields.Text()

    expected_price = fields.Float(string='Prix demandé', required=True)

    # readonly=True : l'utilisateur ne le saisit pas, il sera rempli
    #                 quand une offre sera acceptée (plus tard, par du code).
    # copy=False    : dupliquer une annonce ne doit pas dupliquer son prix de vente.
    selling_price = fields.Float(string='Prix de vente', readonly=True, copy=False)

    living_area = fields.Integer(string='Surface habitable (m²)')
    garden_area = fields.Integer(string='Surface du jardin (m²)')

    # active est un nom de champ SPÉCIAL pour Odoo :
    # les enregistrements avec active=False sont masqués de toutes les recherches
    # (c'est l'« archivage »). On ne supprime pas, on archive.
    active = fields.Boolean(default=True)

    state = fields.Selection(
        selection=[
            ('new', 'Nouveau'),
            ('offer_received', 'Offre reçue'),
            ('offer_accepted', 'Offre acceptée'),
            ('sold', 'Vendu'),
            ('canceled', 'Annulé'),
        ],
        string='Statut',
        required=True,
        default='new',
        copy=False,          # une copie repart à l'état "Nouveau"
    )

    # Sans relation=..., Odoo nomme la table estate_property_estate_property_tag_rel.
    tag_ids = fields.Many2many('estate.property.tag', string='Étiquettes')

    lead_id = fields.Many2one('estate.lead', string='Prospect')
```

### `models/estate_property_tag.py`

```python
from odoo import fields, models


class EstatePropertyTag(models.Model):
    _name = 'estate.property.tag'
    _description = 'Étiquette de bien'

    name = fields.Char(required=True)
    # Integer : c'est l'index de couleur utilisé par le widget many2many_tags (0 à 11)
    color = fields.Integer()
```

### `models/estate_lead.py`

```python
from odoo import fields, models


class EstateLead(models.Model):
    _name = 'estate.lead'
    _description = 'Prospect'

    name = fields.Char(string='Nom', required=True)
    phone = fields.Char(string='Téléphone')   # Char, pas Integer : +32, espaces, 0 initial…
    email = fields.Char()

    # Les biens dont le lead_id pointe vers ce prospect
    property_ids = fields.One2many('estate.property', 'lead_id', string='Biens')
```

### `security/ir.model.access.csv`

```csv
id,name,model_id:id,group_id:id,perm_read,perm_write,perm_create,perm_unlink
access_estate_property,estate.property,model_estate_property,base.group_user,1,1,1,1
access_estate_property_tag,estate.property.tag,model_estate_property_tag,base.group_user,1,1,1,1
access_estate_lead,estate.lead,model_estate_lead,base.group_user,1,1,1,1
```

### Pourquoi `phone` est un `Char`

Un numéro de téléphone n'est pas un nombre : on ne l'additionne pas, il commence souvent par `0`
(qu'un `Integer` supprimerait) et contient des `+` ou des espaces. Règle simple :
**si on ne fait pas de calcul dessus, ce n'est pas un nombre.**

---

[⬅ Retour au chapitre](../chapitres/04-modeles.md)
