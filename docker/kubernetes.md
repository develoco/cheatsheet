kubernetes, docker, swarm, orchestration, cluster, stack, deployment, gitlab

https://docs.docker.com/docker-for-mac/kubernetes/
https://docs.gitlab.com/omnibus/docker/#install-gitlab-using-docker-compose
https://medium.com/@thms.hmm/docker-for-mac-with-kubernetes-enable-k8s-dashboard-62fe036b7480

You can deploy a stack on Kubernetes with docker stack deploy, the docker-compose.yml file, and the name of the stack.

    docker stack deploy --compose-file /path/to/docker-compose.yml mystack
    docker stack services mystack

By default, the default namespace is used. You can specify a namespace with the --namespace flag.

    docker stack deploy --namespace my-app --compose-file docker-compose.yml mystack

You can see the service deployed with the kubectl get services command.

    kubectl get services -n my-app
    
# Demo app

Docker compose file

    version: '3.3'

    services:
      web:
        build: web
        image: dockerdemos/lab-web
        volumes:
         - "./web/static:/static"
        ports:
         - "80:80"

      words:
        build: words
        image: dockerdemos/lab-words
        deploy:
          replicas: 5
          endpoint_mode: dnsrr
          resources:
            limits:
              memory: 16M
            reservations:
              memory: 16M

      db:
        build: db
        image: dockerdemos/lab-db


# Git lab

Create a docker-compose.yml file:

    version: "3.6"
    services:
     gitlab:
       image: gitlab/gitlab-ce:latest
       ports:
         - "22:22"
         - "80:80"
         - "443:443"
       volumes:
         - /srv/gitlab/data:/var/opt/gitlab
         - /srv/gitlab/logs:/var/log/gitlab
         - /srv/gitlab/config:/etc/gitlab
       environment:
         GITLAB_OMNIBUS_CONFIG: "from_file('/omnibus_config.rb')"
       configs:
         - source: gitlab
           target: /omnibus_config.rb
       secrets:
         - gitlab_root_password
     gitlab-runner:
       image: gitlab/gitlab-runner:alpine
       deploy:
         mode: replicated
         replicas: 4
    configs:
     gitlab:
       file: ./gitlab.rb
    secrets:
     gitlab_root_password:
       file: ./root_password.txt

For simplicity reasons, the network configuration was omitted. More information can be found in the official Compose file reference.

Create a gitlab.rb file:

    external_url 'https://my.domain.com/'
    gitlab_rails['initial_root_password'] = File.read('/run/secrets/gitlab_root_password')

Create a root_password.txt file:

    MySuperSecretAndSecurePass0rd!

Make sure you are in the same directory as docker-compose.yml and run:

    docker stack deploy --compose-file docker-compose.yml mystack
