# Réponses — Chapitre 10

[⬅ Retour au chapitre](../chapitres/10-rapports-pdf.md)

---

## Exercice 10.1

**Un premier rapport.**

### Arborescence

```
openacademy/
└── report/
    └── session_report.xml     ← l'action ET le template dans le même fichier
```

```python
# __manifest__.py
'data': [
    # ...
    'report/session_report.xml',
],
```

### `report/session_report.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<odoo>
    <!--
        1. L'ACTION : "il existe un rapport sur les sessions, voici comment le produire".
    -->
    <record id="action_report_session" model="ir.actions.report">
        <!-- Le libellé dans le menu Imprimer -->
        <field name="name">Feuille de session</field>
        <!-- Le modèle imprimé : docs contiendra des openacademy.session -->
        <field name="model">openacademy.session</field>
        <field name="report_type">qweb-pdf</field>
        <!-- Le template à utiliser : <module>.<id du template> -->
        <field name="report_name">openacademy.report_session</field>
        <field name="report_file">openacademy.report_session</field>
        <!-- Nom du fichier téléchargé. object = la session imprimée. -->
        <field name="print_report_name">'Session - %s' % object.name</field>
        <!-- binding_model_id : ajoute le rapport au menu Imprimer des sessions.
             model_openacademy_session : l'XML ID automatique du modèle. -->
        <field name="binding_model_id" ref="model_openacademy_session"/>
        <field name="binding_type">report</field>
    </record>

    <!--
        2. LE TEMPLATE : "voici à quoi ressemble le document".
    -->
    <template id="report_session">
        <!-- html_container : la structure HTML de base (head, CSS des rapports) -->
        <t t-call="web.html_container">
            <!-- docs : les sessions à imprimer (une ou plusieurs si sélection dans la liste) -->
            <t t-foreach="docs" t-as="doc">
                <!-- external_layout : en-tête (logo, adresse) et pied de page de la société,
                     tels que configurés dans Paramètres → Société → Disposition des documents -->
                <t t-call="web.external_layout">
                    <!-- class="page" : le contenu d'une page. Chaque doc = une nouvelle page. -->
                    <div class="page">
                        <!-- t-field : affiche le champ avec le bon format -->
                        <h2 t-field="doc.name"/>
                        <!-- Pour un Many2one, t-field affiche le nom de l'enregistrement lié -->
                        <p>Cours : <span t-field="doc.course_id"/></p>
                    </div>
                </t>
            </t>
        </t>
    </template>
</odoo>
```

### Si le PDF est vide ou sans style

wkhtmltopdf charge le CSS **en appelant le serveur Odoo** par HTTP. S'il ne peut pas le joindre,
le PDF sort sans mise en forme. Vérifiez le paramètre système `web.base.url`
(Paramètres → Technique → Paramètres système) : il doit être `http://127.0.0.1:8069` en local.

---

## Exercice 10.2

**Feuille de présence.**

```xml
<template id="report_session">
    <t t-call="web.html_container">
        <t t-foreach="docs" t-as="doc">
            <t t-call="web.external_layout">
                <div class="page">

                    <!-- En-tête : nom à gauche, dates à droite (grille Bootstrap) -->
                    <div class="row mb-4">
                        <div class="col-8">
                            <h2 t-field="doc.name"/>
                            <div>
                                <strong>Professeur :</strong>
                                <span t-field="doc.instructor_id"/>
                            </div>
                        </div>
                        <div class="col-4 text-end">
                            <!-- t-field sur une Date : format de la langue (10/10/2026 en français) -->
                            <div><strong>Du</strong> <span t-field="doc.start_date"/></div>
                            <div><strong>Au</strong> <span t-field="doc.end_date"/></div>
                        </div>
                    </div>

                    <!-- table-bordered : des bordures partout, pour signer dans les cases -->
                    <table class="table table-bordered">
                        <thead>
                            <tr>
                                <th style="width: 50%">Participant</th>
                                <th>Signature</th>
                            </tr>
                        </thead>
                        <tbody>
                            <!-- Une ligne <tr> par participant -->
                            <tr t-foreach="doc.attendee_ids" t-as="attendee">
                                <!-- t-out : affiche la valeur brute (échappée, donc sans risque d'injection HTML) -->
                                <td t-out="attendee.name"/>
                                <!-- Cellule vide, avec de la hauteur pour la signature -->
                                <td style="height: 40px;"/>
                            </tr>
                            <!-- Si aucun participant : un message plutôt qu'un tableau vide -->
                            <tr t-if="not doc.attendee_ids">
                                <td colspan="2" class="text-center text-muted">
                                    Aucun participant inscrit.
                                </td>
                            </tr>
                        </tbody>
                    </table>

                </div>
            </t>
        </t>
    </t>
</template>
```

### Pourquoi 3 sessions = 3 pages

`t-foreach="docs"` boucle sur les sessions sélectionnées, et chaque passage appelle `web.external_layout`
qui ouvre une nouvelle `page`. wkhtmltopdf insère un saut de page entre chaque.

---

## Exercice 10.3

**Défi.**

### 1. Totaux en bas du tableau

```xml
<!-- Après le </tbody>, dans la <table> -->
<tfoot>
    <tr>
        <td>
            <strong t-out="len(doc.attendee_ids)"/> participant(s)
            sur <span t-field="doc.max_participants"/> places
        </td>
        <td class="text-end">
            <!-- t-options : formatage. Ici, pas de décimales. -->
            Occupation : <span t-field="doc.seat_ratio" t-options="{'widget': 'float', 'precision': 0}"/> %
        </td>
    </tr>
</tfoot>
```

### 2. Mot de passe pour les responsables

```xml
<!-- user : l'utilisateur qui imprime (variable fournie à tous les rapports) -->
<div t-if="user.has_group('openacademy.group_openacademy_manager')">
    <strong>Mot de passe :</strong> <span t-field="doc.password"/>
</div>
```

> Le champ `password` est protégé par `groups` (chapitre 9) : même sans le `t-if`, un non-responsable
> ne pourrait pas le lire et le rapport échouerait. Le `t-if` évite l'erreur et masque la ligne proprement.

### 3. Le rapport de facture

Dans `addons/account/views/report_invoice.xml`, le titre change selon l'état et le type de document :

```xml
<span t-if="o.move_type == 'out_invoice' and o.state == 'posted'">Facture</span>
<span t-elif="o.move_type == 'out_invoice' and o.state == 'draft'">Facture brouillon</span>
<span t-elif="o.move_type == 'out_refund'">Avoir</span>
...
```

(Le code exact varie selon la version, mais le principe est le même.)

**À retenir :** une facture en brouillon ne doit pas pouvoir être confondue avec une facture officielle.
C'est une exigence **légale**, gérée simplement avec `t-if` dans le template.

---

[⬅ Retour au chapitre](../chapitres/10-rapports-pdf.md)
