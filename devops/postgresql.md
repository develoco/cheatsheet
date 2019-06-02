postresql

# Install and setup (macOS, brew)

Install dependencies

    brew install postgresql

Start postgres manually

    pg_ctl -D /usr/local/var/postgres start

To have launchd start redis, postgresql now and restart at login

    brew services start postgresql

Create user and connect

    createuser -s postgres
    psql --user=postgres
    postgres=# \?

In the console create a role for specific apps

    create role distinction with createdb login password 'distinction';
    \q