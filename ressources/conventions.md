# Conventions de nommage

[⬅ Sommaire](../README.md)

## Les noms à utiliser dans `openacademy`

Les exercices s'enchaînent d'un chapitre à l'autre. **Utilisez exactement ces noms**,
sinon les consignes des chapitres suivants ne correspondront plus à votre code.

### `openacademy.course` — Cours

| Champ | Type | Chapitre |
|---|---|---|
| `name` | Char, obligatoire, unique | 4, 6 |
| `description` | Text | 4 |
| `level` | Selection : `basic`, `medium`, `advanced` | 4 |
| `responsible_id` | Many2one → `res.users` | 4 |
| `session_ids` | One2many → `openacademy.session` | 4 |
| `price_per_participant` | Float | 8 |
| `session_count` | Integer, calculé (bouton intelligent) | 8 |

### `openacademy.session` — Session

| Champ | Type | Chapitre |
|---|---|---|
| `name` | Char, unique | 4, 6 |
| `reference` | Char, séquence `SESS/2026/0001` | 6 |
| `start_date` | Date | 4 |
| `end_date` | Date (calculée depuis le début et la durée au chapitre 8) | 4, 8 |
| `duration` | Float (jours) | 4 |
| `course_id` | Many2one → `openacademy.course` | 4 |
| `level` | Related → `course_id.level` | 4 |
| `instructor_id` | Many2one → `openacademy.contact`, puis `res.partner` | 4, 7 |
| `attendee_ids` | Many2many → `openacademy.contact`, puis `res.partner` | 4, 7 |
| `state` | Selection : `draft`, `confirm`, `review` (chapitre 9), `done`, `cancel` | 6, 9 |
| `attendee_count` | Integer, calculé | 8 |
| `max_participants` | Integer | 8 |
| `seat_ratio` | Float, calculé | 8 |
| `total_price` | Float, calculé, stocké | 8 |
| `responsible_id` | Related → `course_id.responsible_id`, stocké | 8 |
| `sale_order_ids` | One2many → `sale.order` | 7 |
| `password` | Char, réservé aux responsables | 9 |

### `openacademy.contact` — Contact (chapitres 4 à 6, remplacé par `res.partner` au chapitre 7)

| Champ | Type |
|---|---|
| `name` | Char |
| `image` | Image |
| `birth_date` | Date |
| `session_ids` | Many2many → `openacademy.session` |

### Extension de `res.partner` (chapitre 7)

| Champ | Type | Chapitre |
|---|---|---|
| `is_instructor` | Boolean | 7 |
| `session_ids` | Many2many → `openacademy.session` | 7 |
| `birth_date` | Date | 8 |
| `age` | Integer, calculé | 8 |

---

## Conventions générales Odoo

| Élément | Convention | Exemple |
|---|---|---|
| Nom de modèle | `module.objet`, au singulier, points | `openacademy.session` |
| Classe Python | CamelCase | `class Session(models.Model)` |
| Fichier Python | un fichier par modèle, nom du modèle | `models/session.py` |
| Many2one | se termine par `_id` | `course_id` |
| One2many, Many2many | se termine par `_ids` | `session_ids` |
| Méthode de calcul | `_compute_<champ>` | `_compute_seat_ratio` |
| Méthode inverse | `_inverse_<champ>` | `_inverse_total` |
| Onchange | `_onchange_<champ>` | `_onchange_partner_id` |
| Contrainte | `_check_<règle>` | `_check_end_date` |
| Méthode appelée par un bouton | `action_<verbe>` | `action_confirm` |
| Méthode appelée par une action planifiée | `_cron_<verbe>` | `_cron_close_past_sessions` |
| ID de séquence | `seq_<modèle>` | `seq_openacademy_session` |
| ID d'action planifiée | `cron_<verbe>` | `cron_close_sessions` |
| ID de donnée de démo | `demo_<objet>_<n>` | `demo_course_python` |
| ID de vue | `<modèle>_view_<type>` | `session_view_form` |
| ID d'action | `<modèle>_action` | `session_action` |
| ID de menu | `menu_<objet>` | `menu_session` |
| Fichier de vues | `views/<modèle>_views.xml` | `views/session_views.xml` |

Référence complète : [Directives de codage Odoo](https://www.odoo.com/documentation/18.0/fr/contributing/development/coding_guidelines.html)
