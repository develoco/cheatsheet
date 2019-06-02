rails, ruby

# Install (macOs, homebrew)

Install dependencies (will also install `ruby-build`)

    brew install readline rbenv
    # Alternatively: brew install readline ruby-build rbenv

Add rbenv to bash so that it loads every time you open a terminal

    echo 'if which rbenv > /dev/null; then eval "$(rbenv init -)"; fi' >> ~/.bash_profile
    source ~/.bash_profile

Verification

    rbenv install -l # Shows available versions for download/install throught ruby-build
    rbenv versions # Shows installed versions
    rbenv verion #  Show current active versiom

Install Ruby

    rbenv install 2.6.0

Set global/local

    rbenv local 2.6.1 # Or, rbenv global
    rbenv version
    ruby -v
    cat .ruby-version
    2.6.1

To upgrade to the latest rbenv and update ruby-build with newly released Ruby versions, upgrade the Homebrew packages:

    $ brew upgrade rbenv ruby-build

Install rails for local

    gem install rails # sudo for global

Install dependencies

    brew install redis postgresql

Start redis manually

    redis-server /usr/local/etc/redis.conf

Start postgres manually

    pg_ctl -D /usr/local/var/postgres start

To have launchd start redis, postgresql now and restart at login

    brew services start redis
    brew services start postgresql

# Run a ruby-on-rails project

Project dependencies, from the project root:

    bundle install

DB credentials

    cat <<EOF > config/database.yml
    development:
      adapter: postgresql
      encoding: unicode
      database: distinction_development
      pool: 50
      username: distinction
      password: distinction
    test: ...
    EOF

If it's a postgres db, create role:

    psql --user=postgres
    # or psql --dbname=postgres
    postgres=# create role distinction with createdb login password 'distinction';

Run db migrations (first time)

    rails db:create

First time and after git pull
    rails db:migrate
    rails db:seed

To reset the DB (create, migrate, seed)

    rails db:reset

Prepare environment

    cat <<EOF > .env
    SECRET_KEY_BASE=
    WEB_HOST=http://localhost:8080
    REDIS_HOST=localhost
    REDIS_PORT=6379
    REDIS_DB=1
    SENDGRID_API_KEY=
    EOF
    
Make sure postgres and redis are up then launch rails
    
    rails s # or server

Check `http://localhost:3000/` with your browser

# Console

Launch

    rails c # or console
    pry(main)> exit
 
 Inspect `User`
 
    User.all
    []
    
    User.create
    #<User id: nil, email: "", created_at: nil, updated_at: nil, username: nil, first_name: nil, last_name: nil, terms_agreement: true, receive_newsletter: false, slug: nil>

Create an admin user:
    
    user=User.create!(:email=>"test@test.local",:username=>"test",:password=>"password",:slug=>"test")
