docker, homebrew, brew, dockerformac

# docker for mac install via brew

Using cask

    brew cask install docker

# Docker tricks

Get bound port:

    docker inspect --format '{{ (index (index .NetworkSettings.Ports "80/tcp") 0).HostPort }}' my_container

Check if container exists (stopped or running):

    if [ ! "$(docker ps -a -q -f name=${mariadb})" ]; then
      echo "
    Non-Existing docker container for current branch '${branch}'.
    "
    fi

## nginx (alpine)

    docker run --rm -v $PWD:/usr/share/nginx/html:ro -p 8080:80 -i -t nginx:alpine
