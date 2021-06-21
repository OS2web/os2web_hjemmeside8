# Ballerup D7 Migration module

## Module purpose

The aim of this module is to provide migration of the content from Ballerup.dk D7 version to OS2Web D8.

## Install

1. Module is part of the repository, and can be installed:
    ```
    drush en ballerup_d7_migration
    ```

2. Create separate Database and import D7 version of the site there. Database must be defined in **settings.php** file (next to the default one):
```
$databases['default']['default'] = array (
  ...
);

// Describe migration database.
$databases['migrate']['default'] = array (
  'database'  => '[db_name]',
  'username'  => '[db_user]',
  'password'  => '[db_password]',
  'prefix' => '',
  'host'      => '[db_host]',
  'port' => '',
  'namespace' => 'Drupal\\Core\\Database\\Driver\\mysql',
  'driver'    => 'mysql',
);
```

## Migrate quick run
Run the entire migration process with single command:
```
cd web
sh ./modules/custom/ballerup_d7_migration/scripts/migrate.sh
```

## Migrate process details for manual run

The recommended usage migrate process is via Drush:

Read more about the Drush commands for Migrate API on [Migrate tools](https://www.drupal.org/project/migrate_tool).

 * Migrate users:
    ```
    drush migrate:import ballerup_d7_user
    ```

 * Migrate section terms:
    ```
    drush migrate:import ballerup_d7_taxonomy_section
    ```

 * Migrate tags terms:
    ```
    drush migrate:import ballerup_d7_taxonomy_tags
    ```

 * Migrate Borger.dk articles:
    ```
    drush migrate:import os2web_borgerdk_articles_import
    ```

 * Migrate Contact boxs:
    ```
    drush migrate:import ballerup_d7_contact_box
    ```

 * Migrate Media (gallery_slide) paragraphs:
    ```
    drush migrate:import ballerup_d7_paragraph_iframe
    drush migrate:import ballerup_d7_node_gallery_slide
    ```

 * Migrate Institution pages:
   ```
   drush migrate:import ballerup_d7_node_institution_page
   ```

 * Migrate News:
   ```
   drush migrate:import ballerup_d7_node_news
   ```

 * Migrate Harmonika paragraphs __NB*__:
    ```
    drush cim --partial --source=modules/contrib/os2web_pagebuilder/modules/os2web_paragraphs/modules/os2web_accordion_paragraph/config/optional

    drush migrate:import ballerup_d7_paragraph_accordion
    ```

 * Migrate Indholdside __NB*__:
    ```
    drush migrate:import ballerup_d7_node_indholdside
    ```

## Migration fixes
Some parts were too complication or not possible to be solved by only running the migrations.
They require extra scripts to run after the migration is done. See *scripts* directory
* drush scr scripts/migrate_fix_publish_status.php

  Fixing the publish status of the node.

* drush scr scripts/remove_inline_pictures.php

  Body field might come with inline pictures/files. This script removes that.

* URL ```/admin/config/system/delete-orphans```

  Delete the orphaned paragraphs.

__NB*__ This migration involves entities manual creation, and when run multiple times database is left with orphaned entities. It is _not recommended_ to run this migratons multiple times on a __production database__.

## Useful hints

When changing migration definition in **.yml** files, import the changes with:
```
drush cim --partial --source=modules/contrib/ballerup_d7_migration/config/install -y
```

If migration stopped with an error, it might need to be reset, e.g.:
```
drush migrate:reset ballerup_d7_node_indholdside
```

Instead of migrating/importing all entities, it is faster to do development with importing only few entities. Use **limit** flag:
```
drush migrate:import ballerup_d7_node_indholdside --limit=5
```

By default each migration run will import only new entities, but sometimes you want to overwrite what you already have instead of importing new ones. That can be done with **update** flag:
```
drush migrate:import ballerup_d7_node_indholdside --update
```

Use it with **limit** flag to force update previously imported entities:
```
drush migrate:import ballerup_d7_node_indholdside --limit=5 --update
```

## Contribution

Project is opened for new features and os course bugfixes.
If you have any suggestion or you found a bug in project, you are very welcome
to create an issue in github repository issue tracker.
For issue description there is expected that you will provide clear and
sufficient information about your feature request or bug report.

### Code review policy
See [OS2Web code review policy](https://github.com/OS2Web/docs#code-review)

### Git name convention
See [OS2Web git name convention](https://github.com/OS2Web/docs#git-guideline)
