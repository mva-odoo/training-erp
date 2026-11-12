# Commandes utiles

[⬅ Sommaire](../README.md)

## Lancer Odoo

```bash
# Lancement simple
./odoo-bin -d my_database --addons-path=addons,/home/odoo/src/custo

# Installer un module
./odoo-bin -d my_database --addons-path=... -i openacademy

# Mettre à jour un module (nouveau champ, XML, CSV)
./odoo-bin -d my_database --addons-path=... -u openacademy

# Recharger automatiquement Python et XML à chaque sauvegarde (développement)
./odoo-bin -d my_database --addons-path=... --dev=reload,xml

# Une seule base visible (utile pour les routes publiques)
./odoo-bin -d my_database --addons-path=... --db-filter=my_database

# Base sans données de démo
./odoo-bin -d my_database --addons-path=... -i base --without-demo=all
```

| Vous avez modifié… | Action |
|---|---|
| Une méthode Python | Redémarrer |
| Un champ ou un modèle | Redémarrer avec `-u` |
| Un fichier XML ou CSV | Redémarrer avec `-u` (ou `--dev=xml` pour les vues) |
| Le `__manifest__.py` | Redémarrer avec `-u` |
| Ajouté un nouveau module | Mettre à jour la liste des applications, puis installer |

## Outils

```bash
# Créer le squelette d'un module
./odoo-bin scaffold mon_module /home/odoo/src/custo

# Console Python connectée à la base (variable env disponible)
./odoo-bin shell -d my_database --addons-path=...

# Lancer les tests d'un module
./odoo-bin -d test_db --addons-path=... -i openacademy --test-enable --stop-after-init
```

## Mode développeur

- URL : ajoutez `?debug=1` (ou `?debug=assets` pour le JS/CSS non minifié)
- Menu 🐞 : voir les champs, modifier la vue, voir les métadonnées (XML ID) d'un enregistrement
- **Paramètres → Technique** : modèles, vues, actions, menus, droits d'accès, règles…

## PostgreSQL

```bash
psql --list                    # lister les bases
psql my_database               # se connecter
dropdb my_database             # supprimer une base (⚠️ définitif)
```

```sql
\dt                            -- tables
\dt openacademy*               -- tables du module
\d openacademy_session         -- structure d'une table
SELECT id, name FROM openacademy_course;
\q                             -- quitter
```

## Docker

```bash
docker ps -a                           # conteneurs (y compris arrêtés)
docker start helb_odoo                 # démarrer
docker stop helb_odoo                  # arrêter
docker exec -it helb_odoo /bin/bash    # terminal dans le conteneur
docker logs -f helb_odoo               # logs
```

## Git

```bash
git status                             # qu'est-ce qui a changé ?
git add .
git commit -m "openacademy: ajout du champ state sur les sessions"
git push
git log --oneline                      # historique
```
