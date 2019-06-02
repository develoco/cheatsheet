javascript, react, createreactapp, cra

# Environment

Adding Custom Environment Variables https://facebook.github.io/create-react-app/docs/adding-custom-environment-variables

    .env: Default.
    .env.local: Local overrides. This file is loaded for all environments except test.
    .env.development, .env.test, .env.production: Environment-specific settings.
    .env.development.local, .env.test.local, .env.production.local: Local overrides of environment-specific settings.

Use like this within javascript (variable should begin with `REACT_APP_`)

    process.env.REACT_APP_SECRET_CODE

Note on `NODE_ENV`. This variable is overriden by `run-script` used in your `package.json`. Files on the left have more priority than files on the right:

    npm start: .env.development.local, .env.development, .env.local, .env
    npm run build: .env.production.local, .env.production, .env.local, .env
    npm test: .env.test.local, .env.test, .env (note .env.local is missing)

On your `.gitignore`

    /.env.local
    /.env.*.local

You can also access the environment variables starting with `REACT_APP_` in the `public/index.html`. For example:

    <title>%REACT_APP_WEBSITE_NAME%</title>

# Import alias

Put this in your `.env`

    NODE_PATH=src

Then in js you can do

    import 'something/within/src'
