haproxy

Reverse proxy with virtual hosts, ping monitoring (different thatn health check)

    docker network create --driver=overlay proxy_default

    docker service create \
      --name proxy_lb \
      --detach=true \
      --network proxy_default \
      --mount type=bind,target=/var/run/docker.sock,source=/var/run/docker.sock,readonly \
      -e MONITOR_URI=/ping -e MONITOR_PORT=80 \
      -p 80:80 \
      --constraint "node.role == manager" \
      dockercloud/haproxy
