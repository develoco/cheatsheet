circleci, docker, ansible, vault

# Links

    https://github.com/madcs/deploy-helper

# Auto deploy to s3 with docker

CircleCI config

    version: 2
    jobs:
      build_and_deploy:
        machine:
          docker_layer_caching: true
        steps:
          - checkout
          - run: |
              docker build -f Dockerfile -t gaspardbruno/eiyu-frontend:build --build-arg NPM_TOKEN=${NPM_TOKEN} .
              docker login -u $DOCKER_USER -p $DOCKER_PASS
              docker push gaspardbruno/eiyu-frontend:build
              docker run --rm --volume $(pwd):/usr/src/app --volume /usr/src/app/node_modules --env NODE_ENV=production --env DEPLOY_ENV=staging --env VAULT_PASS=$VAULT_PASS gaspardbruno/eiyu-frontend:build deploy
      deploy_production:
        machine:
          docker_layer_caching: true
        environment:
          IMAGE_TAG: "gaspardbruno/eiyu-frontend:build-production"
          NODE_ENV: "production"
        steps:
          - checkout
          - run: |
              docker build -t "${IMAGE_TAG}" .
              docker build -f Dockerfile -t "${IMAGE_TAG}" --build-arg NPM_TOKEN=${NPM_TOKEN} .
              docker login -u "${DOCKER_USER}" -p "${DOCKER_PASS}"
              docker push "${IMAGE_TAG}"
              docker run --rm -e NODE_ENV="${NODE_ENV}" -e DEPLOY_ENV="${NODE_ENV}" -e VAULT_PASS="${VAULT_PASS}" -v $(pwd):/usr/src/app -v /usr/src/app/node_modules "${IMAGE_TAG}" deploy
    workflows:
      version: 2
      build_and_deploy:
        jobs:
          - build_and_deploy:
              filters:
                branches:
                  only: master
      deploy_production:
        jobs:
          - deploy_production:
              filters:
                # ignore any commit on any branch by default
                branches:
                  ignore: /.*/
                # only act on version tags
                tags:
                  only: /^v[0-9]+(\.[0-9]+)*$/

Dockerfile

    FROM mhart/alpine-node
    ARG NPM_TOKEN
    ENV NPM_TOKEN ${NPM_TOKEN}
    COPY package.json yarn.lock /usr/src/app/
    COPY .npmrc.docker /usr/src/app/.npmrc
    WORKDIR /usr/src/app
    RUN \
        apk update && \
        apk upgrade && \
        apk add --no-cache git build-base python py-pip ansible && \
        pip --no-cache-dir install awscli && \
        npm install -g react-create-app yarn && \
        yarn && \
        yarn cache clean
    VOLUME /usr/src/app
    VOLUME /usr/src/app/node_modules
    EXPOSE 8080
    EXPOSE 35729
    ENTRYPOINT ["yarn"]
    CMD ["start"]

(alernative) leaner dockerfile

    FROM mhart/alpine-node
    COPY package.json package-lock.json /usr/src/app/
    WORKDIR /usr/src/app
    # Aditional packages: gcc libgcc libstdc++ linux-headers
    RUN \
        apk update && \
        apk upgrade && \
        apk add --no-cache git python py-pip g++ make ansible && \
        pip --no-cache-dir install awscli && \
        npm install && \
        npm cache --force clean
    VOLUME /usr/src/app
    VOLUME /usr/src/app/node_modules
    EXPOSE 8080
    EXPOSE 35729
    ENTRYPOINT ["npm", "run"]
    CMD ["dev"]


Store the token: `.npmrc.docker`
    
    //registry.npmjs.org/:_authToken=${NPM_TOKEN}

deploy script

    #!/bin/sh
    # To encrypt use:
    #   ansible-vault encrypt --output=- < env.tmp > conf/staging/env.vault
    echo $VAULT_PASS > pwd.tmp
    
    `(ansible-vault decrypt --vault-password-file=pwd.tmp --output=- < conf/${NODE_ENV}/env.vault | \
    while read LINE; do echo export ${LINE}; done)`
    rm pwd.tmp
    aws s3 sync build/ s3://${AWS_S3_BUCKET}
    aws cloudfront create-invalidation --distribution-id ${AWS_CLOUDFRONT_DISTRIBUTION_ID} --paths /\*

# Auto deploy backend with docker

CircleCI config

    cat .circleci/config.yml

    version: 2
    jobs:
      build_and_deploy:
        machine:
          docker_layer_caching: true
        environment:
          TARGET_ENV: "staging"
          PROJECT_PREFIX: "auto-deploy-test"
          DEPLOY_TO: "ubuntu@ec2-34-245-150-91.eu-west-1.compute.amazonaws.com"
        steps:
          - checkout
          - run: |
              docker login -u $DOCKER_USER -p $DOCKER_PASS
              docker build -f Dockerfile.web -t madcs/$PROJECT_PREFIX:web .
              docker build -f deploy/Dockerfile --build-arg DEPLOY_ENV=$TARGET_ENV -t madcs/$PROJECT_PREFIX:deploy-$TARGET_ENV ./deploy
              docker push madcs/$PROJECT_PREFIX:web
              docker push madcs/$PROJECT_PREFIX:deploy-$TARGET_ENV
              ./deploy/deploy-ci.sh
      deploy_production:
        machine:
          docker_layer_caching: true
        environment:
          TARGET_ENV: "production"
          PROJECT_PREFIX: "auto-deploy-test"
          DEPLOY_TO: "ubuntu@ec2-34-245-150-91.eu-west-1.compute.amazonaws.com"
        steps:
          - checkout
          - run: |
              docker login -u $DOCKER_USER -p $DOCKER_PASS
              docker build -f deploy/Dockerfile --build-arg DEPLOY_ENV=$TARGET_ENV -t madcs/$PROJECT_PREFIX:deploy-$TARGET_ENV ./deploy
              docker push madcs/$PROJECT_PREFIX:deploy-$TARGET_ENV
              ./deploy/deploy-ci.sh
    workflows:
      version: 2
      build_and_deploy:
        jobs:
          - build_and_deploy:
              filters:
                branches:
                  only: master
      deploy_production:
        jobs:
          - deploy_production:
              filters:
                # ignore any commit on any branch by default
                branches:
                  ignore: /.*/
                # only act on version tags
                tags:
                  only: /^v[0-9]+(\.[0-9]+)*$/

deploy-ci.sh

    #!/bin/sh
    # This script should be run on CircleCI
    ssh $DEPLOY_TO "\
      export PROJECT_PREFIX=$PROJECT_PREFIX && \
      export TARGET_ENV=$TARGET_ENV && \
      export VAULT_PASS=$VAULT_PASS && \
      export DOCKER_USER=$DOCKER_USER && \
      export DOCKER_PASS=$DOCKER_PASS && \
      bash -s" < deploy/deploy-host.sh

deploy/Dockerfile

    FROM madcs/deploy-helper
    ARG DEPLOY_ENV=production
    COPY docker-cloud.yml /usr/src/app/
    COPY ${DEPLOY_ENV}.vault /usr/src/app/env.vault

deploy-host.sh

    #!/bin/sh
    # This script should be run on the host
    docker login -u $DOCKER_USER -p $DOCKER_PASS
    docker pull madcs/$PROJECT_PREFIX:deploy-$TARGET_ENV
    # Get the environment variables
    `(docker run --rm -e VAULT_PASS \
      madcs/$PROJECT_PREFIX:deploy-$TARGET_ENV env-vars)`
    # Get the Docker stack configuration
    docker run --rm \
      madcs/$PROJECT_PREFIX:deploy-$TARGET_ENV stack-conf > $PROJECT_PREFIX.tmp.yml
    docker stack deploy --compose-file $PROJECT_PREFIX.tmp.yml \
      --with-registry-auth $PROJECT_PREFIX
    # Cleanup
    rm $PROJECT_PREFIX.tmp.yml
    docker system prune -f

From deploy helper, Dockerfile

    FROM alpine
    COPY entrypoint.sh /usr/src/app/
    RUN apk update && apk upgrade && \
      apk add --no-cache python py-pip ansible
    WORKDIR /usr/src/app
    ENTRYPOINT ["./entrypoint.sh"]

Entrypoint entrypoint.sh

    #!/bin/sh
    # This script should be run from within the Docker container
    if [ "$1" = "env-vars" ]
    then
      echo $VAULT_PASS > pwd.tmp
      # Decrypt vault
      ansible-vault decrypt --vault-password-file=pwd.tmp --output=- < env.vault | \
        while read LINE; do \
          # Will run on host
          echo export ${LINE}; \
        done
      #
      #   To generate encrypted file type (use password foobar):
      #       ansible-vault encrypt --output=- < staging.env > staging.vault
      rm pwd.tmp
    fi
    if [ "$1" = "stack-conf" ]
    then
        cat docker-cloud.yml
    fi

_Alternative__ docker-in-docker deploy Dockerfile

    FROM docker:stable-dind
    ARG DEPLOY_ENV=production
    COPY .env.${DEPLOY_ENV} /.env
    COPY docker-cloud.yml docker-deploy-entrypoint.sh /
    RUN  apk update && apk upgrade && \
      apk add --no-cache git python py-pip ansible
