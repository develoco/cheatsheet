mysql, mariadb, docker

# mysql console cheatsheet

List of all MySQL commands:
Note that all text commands must be first on line and end with ';'
    exit      (\q) Exit mysql. Same as quit.
    go        (\g) Send command to mysql server.
    help      (\h) Display this help.
    source    (\.) Execute an SQL script file. Takes a file name as an argument.
    status    (\s) Get status information from the server.
    use       (\u) Use another database. Takes database name as argument.

    SHOW DATABASES;
    SHOW TABLES;

# docker and mariadb

Links:

* https://hub.docker.com/_/mariadb/
* https://hub.docker.com/r/phpmyadmin/phpmyadmin

Run with docker

    docker run --name some-mariadb -d -e MYSQL_ROOT_PASSWORD=root mariadb

Run an instance of web-based admin site phpmyadmin:

    docker run --name some-phpmyadmin -d --link some-mariadb:db -p 8081:80 phpmyadmin/phpmyadmin

Re-use the docker container to run mysql commands:

    docker run -it -v "$(pwd)"/sql:/shared:ro --link some-mariadb:mysql --rm mariadb sh -c 'exec mysql -h"$MYSQL_PORT_3306_TCP_ADDR" -P"$MYSQL_PORT_3306_TCP_PORT" -uroot -p"$MYSQL_ENV_MYSQL_ROOT_PASSWORD"'
    docker run -it -v "$(pwd)"/sql:/shared:ro --link 8or-mariadb:mysql --rm mariadb sh -c 'exec mysql -h"$MYSQL_PORT_3306_TCP_ADDR" -P"$MYSQL_PORT_3306_TCP_PORT" -uroot -p"$MYSQL_ENV_MYSQL_ROOT_PASSWORD" < /shared/dump.sql'

Alternatively exec mysql by executing other processes in daemon container:

    docker run --name some-mariadb -d -e MYSQL_ROOT_PASSWORD=root -p 3306:3306 mariadb
    docker exec -it some-mariadb mysql -uroot -proot wordpress
    SHOW TABLES;

To dump:

    docker exec some-mariadb sh -c 'exec mysqldump --all-databases -uroot -p"$MYSQL_ROOT_PASSWORD"' > /some/path/on/your/host/all-databases.sql

If you are dumping tables t1, t2, and t3 from mydb

    mysqldump -u... -p... mydb t1 t2 t3 > mydb_tables.sql

If you have a ton of tables in mydb and you want to dump everything except t1, t2, and t3, do this:

    mysqldump -u... -p... --ignore-table=Database.Table1 --ignore-table=Database.Table2 [db] table3 table4 > mydb_tables.sql
Or:

    DBTODUMP=mydb
    SQL="SET group_concat_max_len = 10240;"
    SQL="${SQL} SELECT GROUP_CONCAT(table_name separator ' ')"
    SQL="${SQL} FROM information_schema.tables WHERE table_schema='${DBTODUMP}'"
    SQL="${SQL} AND table_name NOT IN ('t1','t2','t3')"
    TBLIST=`mysql -u... -p... -AN -e"${SQL}"`
    mysqldump -u... -p... ${DBTODUMP} ${TBLIST} > mydb_tables.sql
    
# Example, wordpress db

Connect, dump

    docker run --name some-mariadb -d -e MYSQL_ROOT_PASSWORD=root -p 3306:3306 mariadb
    docker exec -it some-mariadb mysql -proot
    SHOW DATABASES;

    docker exec some-mariadb sh -c 'exec mysqldump --all-databases -uroot -p"$MYSQL_ROOT_PASSWORD"' > /some/path/on/your/host/all-databases.sql
