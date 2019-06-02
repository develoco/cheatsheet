node, nodejs, npm, package, packagemanager, dependency, docker, circleci, ci, publish

# Integrate into your project

Note: Because the core library could live in a private registry it would be advisable to generate an OAuth token within npm.

Add a dependency in your package.json:

    "dependencies": {
        "@gaspard-bruno/etudes-ecommerce-core": "^1.0.0",
        ...

## Develop side by side with your client project

Use npm link to have a working development copy of the library within your project:

    npm uninstall ecommerce-core
    cd ../core
    npm link
    cd ../app
    npm link ecommerce-core

To revert:

    npm unlink ecommerce-core
    cd ../core
    npm unlink
    npm install ecommerce-core

# Publishing

Bump version number, tag on git

    npm version patch -m "Upgrade to %s for reasons"

Reference env variable in `.npmrc`:

    # To run as root within container
    # unsafe-perm = true
    # Authenticate
    //registry.npmjs.org/:_authToken=${NPM_TOKEN}

Publish

    NPM_TOKEN=<OAuth> npm publish --access=public

# Docker

Use this `Dockerfile` to build and publish

    FROM mhart/alpine-node
    ARG NPM_TOKEN
    COPY package.json package-lock.json .npmrc /usr/src/app/
    WORKDIR /usr/src/app
    RUN \
      cp .npmrc .npmrc.tmp && \
      npm install && \
      npm cache --force clean && \
      mv .npmrc.tmp .npmrc
    VOLUME /usr/src/app
    VOLUME /usr/src/app/node_modules
    ENTRYPOINT ["npm"]
    CMD ["start"]
    
# CircleCI

Use this `.circleci/config.yml` to automate publishing:

    version: 2
    jobs:
     publish:
       machine: true
       steps:
         - checkout
         - run: docker build -t ecommerce-core --build-arg NPM_TOKEN=${NPM_TOKEN} .
         - run: docker run --rm -e NPM_TOKEN -v $(pwd):/usr/src/app ecommerce-core test
         - run: docker run --rm -e NPM_TOKEN -v $(pwd):/usr/src/app ecommerce-core publish --access=public
    workflows:
      version: 2
      build_test_and_publish:
        jobs:
          - publish:
              filters:
                branches:
                  only: master
