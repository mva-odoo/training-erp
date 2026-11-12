# Réponses — Chapitre 5

[⬅ Retour au chapitre](../chapitres/05-vues.md)

Organisation conseillée : **un fichier de vues par modèle**, plus un fichier pour les menus.

```
openacademy/
├── __manifest__.py
└── views/
    ├── course_views.xml     ← vues + action des cours
    ├── session_views.xml    ← vues + action des sessions
    ├── contact_views.xml    ← vues + action des contacts
    └── menus.xml            ← tous les menus, chargés en dernier
```

```python
# __manifest__.py
'data': [
    'security/ir.model.access.csv',   # 1. droits : sinon les menus sont invisibles
    'views/course_views.xml',         # 2. vues et actions
    'views/session_views.xml',
    'views/contact_views.xml',
    'views/menus.xml',                # 3. menus : ils référencent les actions → en dernier
],
```

---

## Exercice 5.1

**Créer les menus.**

Pour l'instant, on déclare seulement les **actions** (sans vues : Odoo génère des vues par défaut).

### `views/course_views.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<odoo>
    <!--
        Une action "fenêtre" : ouvrir un modèle dans des vues.
        id         : l'identifiant XML, utilisé par le menu (action="course_action")
        name       : le titre affiché en haut de l'écran (fil d'Ariane)
        res_model  : le modèle à afficher
        view_mode  : les types de vues disponibles, dans l'ordre (la 1re s'ouvre par défaut)
    -->
    <record id="course_action" model="ir.actions.act_window">
        <field name="name">Cours</field>
        <field name="res_model">openacademy.course</field>
        <field name="view_mode">list,form</field>
    </record>
</odoo>
```

Même chose dans `session_views.xml` (`session_action`, `openacademy.session`) et `contact_views.xml`
(`contact_action`, `openacademy.contact`).

### `views/menus.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<odoo>
    <!-- Menu racine : pas de parent → il apparaît dans la liste des applications -->
    <menuitem id="menu_root" name="Open Academy"/>

    <!--
        Sous-menus : parent = le menu racine, action = l'action à ouvrir.
        sequence : l'ordre d'affichage (plus petit = plus à gauche)
    -->
    <menuitem id="menu_course"  name="Cours"    parent="menu_root" action="course_action"  sequence="10"/>
    <menuitem id="menu_session" name="Sessions" parent="menu_root" action="session_action" sequence="20"/>
    <menuitem id="menu_contact" name="Contacts" parent="menu_root" action="contact_action" sequence="30"/>
</odoo>
```

> L'icône de l'application est le fichier `static/description/icon.png` de votre module
> (s'il existe), déclaré avec l'attribut `web_icon="openacademy,static/description/icon.png"`.

Pourquoi `menus.xml` est chargé **en dernier** : `action="course_action"` cherche un enregistrement
dont l'XML ID est `openacademy.course_action`. S'il n'a pas encore été chargé, Odoo s'arrête avec
*« External ID not found »*.

---

## Exercice 5.2

**Liste des sessions.**

### `views/session_views.xml` (extrait)

```xml
<record id="session_view_list" model="ir.ui.view">
    <!-- name : libre, mais la convention est <modèle>.<type de vue> -->
    <field name="name">openacademy.session.list</field>
    <!-- model : le modèle auquel cette vue s'applique -->
    <field name="model">openacademy.session</field>
    <field name="arch" type="xml">
        <list>
            <!-- Une colonne par champ, dans l'ordre d'affichage -->
            <field name="name"/>
            <field name="course_id"/>        <!-- Many2one : affiche le nom du cours -->
            <field name="start_date"/>
            <field name="end_date"/>
            <field name="duration"/>
            <field name="instructor_id"/>
        </list>
    </field>
</record>
```

### Comment Odoo sait quelle vue utiliser

L'action ne cite aucune vue : Odoo cherche **la vue de type `list` du modèle `openacademy.session`
avec la plus petite priorité**. Comme il n'y en a qu'une, c'est la vôtre.
Si plusieurs vues existent, on peut forcer le choix dans l'action avec `view_id`.

---

## Exercice 5.3

**Formulaires.**

### Session

```xml
<record id="session_view_form" model="ir.ui.view">
    <field name="name">openacademy.session.form</field>
    <field name="model">openacademy.session</field>
    <field name="arch" type="xml">
        <form string="Session">
            <!-- sheet : la "feuille" blanche centrée, style document -->
            <sheet>
                <!-- oe_title : le grand titre en haut du formulaire -->
                <div class="oe_title">
                    <h1><field name="name" placeholder="Nom de la session"/></h1>
                </div>

                <!-- Un group contenant deux groups = deux colonnes côte à côte.
                     Dans un group, chaque champ est affiché avec son libellé à gauche. -->
                <group>
                    <group>
                        <field name="course_id"/>
                        <field name="start_date"/>
                        <field name="end_date"/>
                    </group>
                    <group>
                        <field name="duration"/>
                        <field name="instructor_id"/>
                        <!-- many2many_tags : des étiquettes plutôt qu'un tableau -->
                        <field name="attendee_ids" widget="many2many_tags"/>
                    </group>
                </group>
            </sheet>
        </form>
    </field>
</record>
```

### Cours

```xml
<record id="course_view_form" model="ir.ui.view">
    <field name="name">openacademy.course.form</field>
    <field name="model">openacademy.course</field>
    <field name="arch" type="xml">
        <form string="Cours">
            <sheet>
                <div class="oe_title">
                    <h1><field name="name" placeholder="Titre du cours"/></h1>
                </div>
                <group>
                    <group>
                        <field name="level"/>
                    </group>
                    <group>
                        <field name="responsible_id"/>
                    </group>
                </group>

                <!-- notebook = la barre d'onglets, page = un onglet -->
                <notebook>
                    <page string="Description" name="description">
                        <!-- Hors d'un group, le champ prend toute la largeur, sans libellé -->
                        <field name="description" placeholder="Décrivez le cours…"/>
                    </page>
                    <page string="Sessions" name="sessions">
                        <!-- Un One2many dans un formulaire : une sous-liste éditable.
                             On définit ici les colonnes à afficher, sinon Odoo prend
                             la vue liste des sessions (qui contient la colonne "Cours",
                             inutile puisqu'on est déjà dans le cours). -->
                        <field name="session_ids">
                            <list editable="bottom">   <!-- édition directe dans la ligne -->
                                <field name="name"/>
                                <field name="start_date"/>
                                <field name="end_date"/>
                                <field name="instructor_id"/>
                            </list>
                        </field>
                    </page>
                </notebook>
            </sheet>
        </form>
    </field>
</record>
```

`editable="bottom"` : on ajoute les sessions directement dans le tableau (nouvelle ligne en bas)
au lieu d'ouvrir une fenêtre. Le `course_id` de la nouvelle session est rempli **automatiquement** :
c'est tout l'intérêt du `One2many`.

### Contact

```xml
<record id="contact_view_form" model="ir.ui.view">
    <field name="name">openacademy.contact.form</field>
    <field name="model">openacademy.contact</field>
    <field name="arch" type="xml">
        <form string="Contact">
            <sheet>
                <!-- oe_avatar : la photo flottante en haut à droite, comme dans Contacts -->
                <field name="image" widget="image" class="oe_avatar"/>
                <div class="oe_title">
                    <h1><field name="name" placeholder="Nom"/></h1>
                </div>
                <group>
                    <field name="birth_date"/>
                </group>
            </sheet>
        </form>
    </field>
</record>
```

---

## Exercice 5.4

**Recherche, calendrier, graphique.**

### Recherche

```xml
<record id="session_view_search" model="ir.ui.view">
    <field name="name">openacademy.session.search</field>
    <field name="model">openacademy.session</field>
    <field name="arch" type="xml">
        <search>
            <!-- Chaque <field> devient une proposition quand on tape dans la barre :
                 "Rechercher Nom pour : …", "Rechercher Cours pour : …" -->
            <field name="name"/>
            <field name="course_id"/>

            <!-- Un filtre prédéfini : un domaine appliqué en un clic -->
            <filter name="filter_basic" string="Niveau basique"
                    domain="[('level', '=', 'basic')]"/>

            <!-- Filtre dynamique : uid = l'id de l'utilisateur connecté -->
            <filter name="filter_my_courses" string="Mes cours"
                    domain="[('course_id.responsible_id', '=', uid)]"/>

            <!-- Regroupement : un filtre avec un context group_by -->
            <group>
                <filter name="group_course" string="Cours"
                        context="{'group_by': 'course_id'}"/>
            </group>
        </search>
    </field>
</record>
```

> ⚠️ `level` est un `related` **non stocké** : le filtre fonctionne (Odoo traduit en `course_id.level`),
> mais on ne pourrait pas **grouper** par `level`. Il faudrait `store=True` (chapitre 8).

### Calendrier

```xml
<record id="session_view_calendar" model="ir.ui.view">
    <field name="name">openacademy.session.calendar</field>
    <field name="model">openacademy.session</field>
    <field name="arch" type="xml">
        <!-- date_start / date_stop : les champs qui donnent le début et la fin de l'événement
             color : une couleur par cours, pour s'y retrouver
             mode  : la vue affichée par défaut (day, week, month) -->
        <calendar date_start="start_date" date_stop="end_date"
                  color="course_id" mode="month">
            <field name="name"/>
            <field name="instructor_id"/>
        </calendar>
    </field>
</record>
```

### Graphique

```xml
<record id="session_view_graph" model="ir.ui.view">
    <field name="name">openacademy.session.graph</field>
    <field name="model">openacademy.session</field>
    <field name="arch" type="xml">
        <!-- type : bar, line ou pie
             Un champ sans type="measure" = un axe de regroupement.
             Sans mesure, Odoo compte les enregistrements ("Count"). -->
        <graph type="bar">
            <field name="course_id"/>
        </graph>
    </field>
</record>
```

### L'action mise à jour

```xml
<record id="session_action" model="ir.actions.act_window">
    <field name="name">Sessions</field>
    <field name="res_model">openacademy.session</field>
    <!-- Les nouvelles vues ne sont accessibles que si elles sont dans view_mode -->
    <field name="view_mode">list,form,calendar,graph</field>
</record>
```

---

## Exercice 5.5

**Kanban des sessions.**

```xml
<record id="session_view_kanban" model="ir.ui.view">
    <field name="name">openacademy.session.kanban</field>
    <field name="model">openacademy.session</field>
    <field name="arch" type="xml">
        <!--
            default_group_by : les colonnes. Ici une colonne par cours.
            Glisser une carte dans une autre colonne fait un write({'course_id': ...}).
        -->
        <kanban default_group_by="course_id">
            <templates>
                <!-- "card" : le modèle de chaque carte (Odoo 18) -->
                <t t-name="card">
                    <!-- Classes Bootstrap pour la mise en forme -->
                    <field name="name" class="fw-bold fs-5"/>
                    <div class="text-muted">
                        <field name="start_date"/> → <field name="end_date"/>
                    </div>
                    <field name="instructor_id"/>
                    <!-- Les mêmes widgets que dans un formulaire -->
                    <field name="attendee_ids" widget="many2many_tags"/>
                </t>
            </templates>
        </kanban>
    </field>
</record>
```

Dans l'action, `kanban` passe en premier : c'est la vue ouverte par défaut.

```xml
<field name="view_mode">kanban,list,form,calendar,graph</field>
```

**Pourquoi grouper par cours et pas par état ?** Glisser une carte d'une colonne à l'autre modifie
directement le champ. Pour l'état, ça court-circuiterait les boutons et leurs vérifications
(chapitre 6) : on ne veut pas qu'une session passe à « Terminé » par un simple glisser-déposer.

---

## Exercice 5.6

**Des données pour la démo.**

### `__manifest__.py`

```python
'demo': [
    'demo/demo.xml',
],
```

### `demo/demo.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<odoo>
    <!-- ===== Cours ===== -->
    <!-- L'id XML doit être unique dans le module. Préfixe "demo_" : on sait d'où ça vient. -->
    <record id="demo_course_python" model="openacademy.course">
        <field name="name">Python pour débutants</field>
        <field name="level">basic</field>
        <field name="description">Les bases de la programmation en Python.</field>
    </record>
    <record id="demo_course_sql" model="openacademy.course">
        <field name="name">SQL</field>
        <field name="level">medium</field>
    </record>
    <record id="demo_course_odoo" model="openacademy.course">
        <field name="name">Développement Odoo</field>
        <field name="level">advanced</field>
    </record>

    <!-- ===== Contacts ===== -->
    <!-- Au chapitre 5, les contacts sont encore des openacademy.contact.
         Au chapitre 7, remplacez le modèle par res.partner et ajoutez
         <field name="is_instructor" eval="True"/> aux professeurs. -->
    <record id="demo_teacher_1" model="openacademy.contact"><field name="name">Diana Padilla</field></record>
    <record id="demo_teacher_2" model="openacademy.contact"><field name="name">Lester Vaughn</field></record>

    <record id="demo_student_1" model="openacademy.contact"><field name="name">Alice Martin</field></record>
    <record id="demo_student_2" model="openacademy.contact"><field name="name">Bruno Lambert</field></record>
    <record id="demo_student_3" model="openacademy.contact"><field name="name">Chloé Dubois</field></record>
    <record id="demo_student_4" model="openacademy.contact"><field name="name">David Peeters</field></record>
    <record id="demo_student_5" model="openacademy.contact"><field name="name">Emma Janssens</field></record>

    <!-- ===== Sessions ===== -->
    <record id="demo_session_python_1" model="openacademy.session">
        <field name="name">Python — session 1</field>
        <!-- ref : la valeur d'un Many2one, par l'XML ID de l'enregistrement visé -->
        <field name="course_id" ref="demo_course_python"/>
        <field name="instructor_id" ref="demo_teacher_1"/>
        <!-- eval : une expression Python évaluée au chargement.
             DateTime et relativedelta sont disponibles : la date reste toujours "récente". -->
        <field name="start_date" eval="DateTime.today()"/>
        <field name="duration">3</field>
        <!-- Command.set : "la liste vaut exactement ces enregistrements"
             ref('...') : convertit un XML ID en id numérique -->
        <field name="attendee_ids" eval="[Command.set([
            ref('demo_student_1'), ref('demo_student_2'), ref('demo_student_3')])]"/>
    </record>

    <record id="demo_session_python_2" model="openacademy.session">
        <field name="name">Python — session 2</field>
        <field name="course_id" ref="demo_course_python"/>
        <field name="instructor_id" ref="demo_teacher_1"/>
        <field name="start_date" eval="DateTime.today() + relativedelta(months=1)"/>
        <field name="duration">3</field>
        <field name="attendee_ids" eval="[Command.set([ref('demo_student_4')])]"/>
    </record>

    <record id="demo_session_sql" model="openacademy.session">
        <field name="name">SQL — session 1</field>
        <field name="course_id" ref="demo_course_sql"/>
        <field name="instructor_id" ref="demo_teacher_2"/>
        <field name="start_date" eval="DateTime.today() + relativedelta(weeks=2)"/>
        <field name="duration">2</field>
        <field name="attendee_ids" eval="[Command.set([
            ref('demo_student_2'), ref('demo_student_5')])]"/>
    </record>

    <record id="demo_session_odoo" model="openacademy.session">
        <field name="name">Odoo — session 1</field>
        <field name="course_id" ref="demo_course_odoo"/>
        <field name="instructor_id" ref="demo_teacher_2"/>
        <field name="start_date" eval="DateTime.today() - relativedelta(months=1)"/>
        <field name="duration">5</field>
        <field name="attendee_ids" eval="[Command.set([
            ref('demo_student_1'), ref('demo_student_3'), ref('demo_student_5')])]"/>
    </record>
</odoo>
```

### Tester

```bash
# Une nouvelle base, avec les données de démonstration
./odoo-bin -d demo_test --addons-path=addons,/home/odoo/src/custo -i openacademy
```

> **Faites vivre ce fichier avec le module :** quand vous ajouterez un champ obligatoire ou changerez
> un modèle (chapitre 7 : `res.partner`, chapitre 8 : `max_participants`), mettez la démo à jour.
> Une démo cassée empêche l'installation du module sur une base de démonstration.

> Si votre base a été créée **sans** démo, le fichier `demo/` est ignoré. C'est voulu : un client
> en production ne veut pas de faux cours dans sa base.

**Pourquoi des dates relatives (`DateTime.today()`) ?** Avec des dates fixes, la démo vieillit :
dans un an, toutes les sessions seraient passées et le menu « À venir » serait vide.

---

## Exercice 5.7

**Contexte et domaine.**

### 1. Menu « À venir »

```xml
<record id="session_action_upcoming" model="ir.actions.act_window">
    <field name="name">Sessions à venir</field>
    <field name="res_model">openacademy.session</field>
    <field name="view_mode">list,form,calendar</field>
    <!--
        domain : filtre IMPOSÉ. L'utilisateur ne peut pas le retirer depuis la recherche.
        context_today() : la date du jour (dans le fuseau de l'utilisateur).
        strftime('%Y-%m-%d') : au format attendu pour une comparaison de date.
    -->
    <field name="domain">[('start_date', '>=', context_today().strftime('%Y-%m-%d'))]</field>
    <!--
        search_default_group_course : active à l'ouverture le filtre "group_course"
        défini dans la vue recherche (exercice 5.4). L'utilisateur peut le retirer.
    -->
    <field name="context">{'search_default_group_course': 1}</field>
</record>
```

```xml
<!-- menus.xml : un sous-menu sous Sessions -->
<menuitem id="menu_session_upcoming" name="À venir" parent="menu_root"
          action="session_action_upcoming" sequence="25"/>
```

### 2. Bouton « Nouvelle session » sur le cours

On ajoute une action dédiée qui ouvre **directement** un formulaire vide :

```xml
<record id="session_action_new" model="ir.actions.act_window">
    <field name="name">Nouvelle session</field>
    <field name="res_model">openacademy.session</field>
    <field name="view_mode">form</field>
    <field name="target">current</field>
</record>
```

Dans le formulaire du cours :

```xml
<header>
    <!--
        type="action" : ouvre l'action par son XML ID.
        context : default_course_id pré-remplit le champ course_id de la session.
                  "id" est l'id du cours affiché.
    -->
    <button name="%(openacademy.session_action_new)d" type="action"
            string="Nouvelle session" class="btn-primary"
            context="{'default_course_id': id}"/>
</header>
```

> ⚠️ L'action doit être définie **avant** la vue du cours qui l'utilise dans `%(...)d`.
> Mettez-la dans `course_views.xml` avant le formulaire, ou chargez `session_views.xml` avant `course_views.xml`.

---

## Exercice 5.8

**Défi.**

1. Le formulaire contact (`base.view_partner_form`) utilise exactement `sheet`, `group` et `notebook`,
   avec des `page` nommées (`contact_addresses`, `sales_purchases`, `internal_notes`…).
   Ces `name` serviront au chapitre 7 pour ajouter vos propres onglets.
2. Widgets intéressants à découvrir : `email`, `phone`, `url`, `badge`, `priority` (étoiles),
   `boolean_toggle`, `statinfo`, `handle` (glisser-déposer dans une liste avec un champ `sequence`).
3. Pour `estate`, la structure est la même qu'Open Academy. Exemple de formulaire :

```xml
<form string="Bien">
    <sheet>
        <div class="oe_title">
            <h1><field name="name"/></h1>
            <!-- Les étiquettes juste sous le titre, avec leur couleur -->
            <field name="tag_ids" widget="many2many_tags" options="{'color_field': 'color'}"/>
        </div>
        <group>
            <group>
                <field name="state"/>
                <field name="lead_id"/>
            </group>
            <group>
                <field name="expected_price"/>
                <field name="selling_price"/>
            </group>
        </group>
        <notebook>
            <page string="Description">
                <group>
                    <field name="description"/>
                    <field name="living_area"/>
                    <field name="garden_area"/>
                    <field name="active"/>
                </group>
            </page>
        </notebook>
    </sheet>
</form>
```

Et pour voir les biens archivés : un filtre `<filter name="archived" string="Archivés" domain="[('active', '=', False)]"/>`
dans la vue recherche.

---

[⬅ Retour au chapitre](../chapitres/05-vues.md)
