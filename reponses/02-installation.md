# Réponses — Chapitre 2

[⬅ Retour au chapitre](../chapitres/02-installation.md)

---

## Exercice 2.1

**Environnement opérationnel.**

### La commande complète, ligne par ligne

```bash
docker run -it -d \
  --name helb_odoo \
  -v ~/Documents/custom_addons:/home/odoo/src/custo \
  -p 8069:8069 \
  -p 8080:8080 \
  mvaodoo/odoo_training
```

| Option | Signification |
|---|---|
| `-it` | Terminal interactif (utile pour voir les logs si besoin) |
| `-d` | Lance le conteneur en arrière-plan |
| `--name helb_odoo` | Un nom lisible, sinon Docker en invente un |
| `-v ~/Documents/custom_addons:/home/odoo/src/custo` | `VOTRE_DOSSIER:DOSSIER_DANS_LE_CONTENEUR` |
| `-p 8069:8069` | `PORT_DE_VOTRE_PC:PORT_DU_CONTENEUR` pour Odoo |
| `-p 8080:8080` | Idem pour code-server |
| `mvaodoo/odoo_training` | L'image à lancer |

> ⚠️ Sous Windows, le chemin s'écrit `C:\Users\vous\Documents\custom_addons`.

### Vérifier que le volume fonctionne

Sur votre ordinateur :

```bash
echo "test" > ~/Documents/custom_addons/test.txt
```

Dans le terminal de code-server (ou `docker exec -it helb_odoo /bin/bash`) :

```bash
ls /home/odoo/src/custo/          # test.txt doit apparaître
```

Si le fichier n'apparaît pas, le chemin dans `-v` est faux. Supprimez le conteneur (`docker rm -f helb_odoo`)
et relancez la commande avec le bon chemin. Vous ne perdez rien : vos fichiers sont sur votre ordinateur.

### Pourquoi c'est important

Tout ce qui est **dans** le conteneur et **hors** d'un volume est perdu quand le conteneur est supprimé.
Si vous codez directement dans le conteneur sans volume, une mauvaise manipulation efface des heures de travail.
Avec le volume, le code vit dans votre dépôt Git, sur votre machine.

---

## Exercice 2.2

**Installer une application.**

En installant **Ventes**, vous verrez apparaître au minimum **Facturation** (ou Comptabilité) et **Discussion**.

**Pourquoi ?** Le module `sale_management` (Ventes) déclare des **dépendances** dans son fichier `__manifest__.py` :

```python
'depends': ['sale', 'digest'],   # sale dépend lui-même de sales_team, account (facturation), ...
```

Odoo installe automatiquement toute la chaîne des dépendances : on ne peut pas vendre sans pouvoir facturer.

À retenir pour la suite : **votre** module devra lui aussi déclarer ses dépendances. Si vous étendez les commandes
de vente, il devra dépendre de `sale`. Vous le verrez au chapitre 3.

---

[⬅ Retour au chapitre](../chapitres/02-installation.md)
