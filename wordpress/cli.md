wordpress, wp, cli, commandline, phar

https://wp-cli.org
Or https://make.wordpress.org/cli/handbook/installing/

# Installation

Download

    curl -O https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar

Then, check if it works:

    php wp-cli.phar --info

To be able to type just wp, instead of php wp-cli.phar, you need to make the file executable and move it to somewhere in your PATH. For example:

    chmod +x wp-cli.phar
    sudo mv wp-cli.phar /usr/local/bin/wp


# Getting started

Create the db in a _MariaDB_ shell `mysql -uroot`

```
# CREATE DATABASE my_wp_db;
GRANT ALL PRIVILEGES ON my_wp_db.* TO my_wp_user@localhost IDENTIFIED BY "secretos";
# GRANT ALL PRIVILEGES ON my_wp_db.* TO my_wp_user@'%' IDENTIFIED BY "secretos";
FLUSH PRIVILEGES;
EXIT
```

Verify with `mysql -umy_wp_user -p` (Note: `SHOW DATABASES;` will not display the db yet as it has not been created)

Setup project

```
wp core download
wp config create --dbname=my_wp_db --dbuser=my_wp_user --prompt=dbpass
# wp config create --dbname=my_wp_db --dbuser=my_wp_user --prompt=dbpass < password.txt
# wp config create --dbname=my_wp_db --dbuser=my_wp_user --dbpass=secretos
wp db create
wp core install --url=localhost:8080/my-wordpress --title="My Wordpress" --admin_user=admin --admin_email=admin@localhost.local --skip-email --prompt=admin_password
```
