wordpress, php, mysql, mamp, mariadb, docker, wp

# wordpress, mariadb, docker


    docker run --name 8or-mariadb -d -e MYSQL_ROOT_PASSWORD=root mariadb
    docker run --name 8or-wordpress -d --link 8or-mariadb:mysql -p 80:80 -v "$(pwd)":/eight-octo-recipes:ro wordpress

    docker run --name 8or-wordpress -d --link 8or-mariadb:mysql -p 80:80 \
      -v "$(pwd)/assets":/var/www/html/assets:ro \
      -v "$(pwd)/uploads":/var/www/html/wp-content/uploads \
      -v "$(pwd)/themes/deepbluesea":/var/www/html/wp-content/themes/deepbluesea:ro \
      -v "$(pwd)/plugins/octofacts.php":/var/www/html/wp-content/plugins/octofacts.php:ro \
      wordpress

    #docker run --name 8or-wordpress -u www-data:www-data -d --link 8or-mariadb:mysql -p 80:80 -v "$(pwd)":/eight-octo-recipes:ro wordpress
    docker run --name 8or-phpmyadmin -d --link 8or-mariadb:db -p 8081:80 phpmyadmin/phpmyadmin

    docker exec -it 8or-wordpress bash

    docker rm -f 8or-wordpress 8or-phpmyadmin 8or-mariadb

https://hub.docker.com/_/mariadb/
https://hub.docker.com/_/wordpress
https://hub.docker.com/r/phpmyadmin/phpmyadmin


    docker run -it -v "$(pwd)"/sql:/shared:ro --link 8or-mariadb:mysql --rm mariadb sh -c 'exec mysql -h"$MYSQL_PORT_3306_TCP_ADDR" -P"$MYSQL_PORT_3306_TCP_PORT" -uroot -p"$MYSQL_ENV_MYSQL_ROOT_PASSWORD"'
    docker run -it -v "$(pwd)"/sql:/shared:ro --link 8or-mariadb:mysql --rm mariadb sh -c 'exec mysql -h"$MYSQL_PORT_3306_TCP_ADDR" -P"$MYSQL_PORT_3306_TCP_PORT" -uroot -p"$MYSQL_ENV_MYSQL_ROOT_PASSWORD" < /shared/000.sql'
    docker run -it -v "$(pwd)"/sql:/shared:ro --link 8or-mariadb:mysql --rm mariadb sh -c 'exec mysql -h"$MYSQL_PORT_3306_TCP_ADDR" -P"$MYSQL_PORT_3306_TCP_PORT" -uroot -p"$MYSQL_ENV_MYSQL_ROOT_PASSWORD" < /shared/000-structure.sql'
    docker run -it -v "$(pwd)"/sql:/shared:ro --link 8or-mariadb:mysql --rm mariadb sh -c 'exec mysql -h"$MYSQL_PORT_3306_TCP_ADDR" -P"$MYSQL_PORT_3306_TCP_PORT" -uroot -p"$MYSQL_ENV_MYSQL_ROOT_PASSWORD" < /shared/001-settings.sql'
    docker run -it -v "$(pwd)"/sql:/shared:ro --link 8or-mariadb:mysql --rm mariadb sh -c 'exec mysql -h"$MYSQL_PORT_3306_TCP_ADDR" -P"$MYSQL_PORT_3306_TCP_PORT" -uroot -p"$MYSQL_ENV_MYSQL_ROOT_PASSWORD" < /shared/002-posts.sql'

    docker run --name 8or-wordpress -d --link 8or-mariadb:mysql -p 80:80 -v "$(pwd)":/root/eight-octo-recipes:ro wordpress

Dump  db with docker

    docker run -it -v "$(pwd)"/sql:/shared:ro --link 8or-mariadb:mysql --rm mariadb sh -c 'exec mysql -h"$MYSQL_PORT_3306_TCP_ADDR" -P"$MYSQL_PORT_3306_TCP_PORT" -uroot -p"$MYSQL_ENV_MYSQL_ROOT_PASSWORD" < /shared/002-posts.sql'

Dump specific data from the db, no structures:

    mysqldump -uroot -proot --no-create-info \
      wordpress wp_links wp_options

Dump anything not part of the wordpress install:

    mysqldump -uroot -proot \
      --ignore-table=wordpress.wp_commentmeta \
      --ignore-table=wordpress.wp_comments \
      --ignore-table=wordpress.wp_links \
      --ignore-table=wordpress.wp_options \
      --ignore-table=wordpress.wp_postmeta \
      --ignore-table=wordpress.wp_posts \
      --ignore-table=wordpress.wp_term_relationships \
      --ignore-table=wordpress.wp_term_taxonomy \
      --ignore-table=wordpress.wp_termmeta \
      --ignore-table=wordpress.wp_terms \
      --ignore-table=wordpress.wp_usermeta \
      --ignore-table=wordpress.wp_users \
      wordpress

# wordpress cli

Delete all initial posts (and pages)

    wp post delete  --force $(wp post list --format=ids) $(wp post list --post_type='page' --format=ids)

Create a subtheme from scratch:

    wp scaffold child-theme deepbluesea --parent_theme=twentyseventeen

Delete transient data (before export):

    $wp transient delete --all
    $wp transient delete --all --network

List users with docker wp cli image and local folder wp install:

    docker run -it --rm -v "$(pwd)"/dist:/var/www/html wordpress:cli help
    docker run -it --rm -v "$(pwd)"/dist:/var/www/html --link some-mariadb wordpress:cli help

List users with docker wp cli image and docker wp server image:

    docker run -it --rm -volumes-from 8or-wordpress --network container:8or-wordpress wordpress:cli help

Regenerate (deploy) a site from scratch:

    # download specific wp package
    core download --version=5.0.2 --locale=en_US
    # create wp config file (from now on we will need a db link)
    config create --dbname=wp_octorecipes --dbuser=root --dbhost=some-mariadb --dbpass=root
    config list
    config get table_prefix DB_NAME
    # create database
    db create
    # create tables and populate initial config
    core install --url=localhost --title="Eight Octopus Recipes" --admin_user=octopus --admin_password=recipes --admin_email=info@wp-cli.org --skip-email
    core is-installed
    core version
    post list
    post list --post-type=page
    # cleanup initial sample posts, comments and pages
    post delete --force 1,2,3
    # export initial version of site
    export --max_file_size=-1 --dir=/var/www/html --filename_format=wpexport.xml
    # install import plugin and activate
    plugin install wordpress-importer --activate
    # import backup, no users
    import --authors=skip wpexport.xml

Changing url

    wp option update home 'http://example.com'
    wp option update siteurl 'http://example.com'

Storing url in a variable (bash < 4)

    url_untrimmed=$(wp option get home)
    url=${url_untrimmed::${#url_untrimmed}-1}

Search and replace the db

    wp search-replace 'http://example.com' 'http://example.dev' --recurse-objects --skip-columns=guid --skip-tables=wp_users

Generic example 1, simplified:
 
    core download
    config create --dbhost=somedb --dbpass=secret
    db create
    core install --url=somehost --title="Eight Octopus Recipes" --admin_user=octopus --admin_email=info@wp-cli.org
    import data.xml

Generic example 2:

    wp core download --path=wpclidemo.dev
    # Creating directory '/srv/www/wpclidemo.dev/'.
    $ cd wpclidemo.dev
    $ wp config create --dbname=wpclidemo --dbuser=root
    Success: Generated 'wp-config.php' file.
    $ wp db create
    Success: Database created.
    $ wp core install --url=wpclidemo.dev --title="WP-CLI" --admin_user=wpcli --admin_password=wpcli --admin_email=info@wp-cli.org
    Success: WordPress installed successfully.
    $ wp plugin install http://s3.amazonaws.com/bucketname/my-plugin.zip --activate
    $ wp plugin activate my-plugin
    $ wp export
    $ wp import example.wordpress.2016-06-21.xml --authors=create