Docker Swarm Note
====

## 0. Setup VirtualBox

Download & Install: https://www.virtualbox.org/wiki/Downloads

## 1. Install Docker Machine

```bash
if [[ ! -d "$HOME/bin" ]]; then mkdir -p "$HOME/bin"; fi && \
curl -L https://github.com/docker/machine/releases/download/v0.16.2/docker-machine-Windows-x86_64.exe > "$HOME/bin/docker-machine.exe" && \
chmod +x "$HOME/bin/docker-machine.exe"
```

## 2. Setting up a Swarm cluster

```bash
for i in 1 2 3; do
    docker-machine create -d virtualbox node-$i
done
```

## 3. 

```bash
eval $(docker-machine env node-1)

docker swarm init --advertise-addr $(docker-machine ip node-1)
```

## 4.

```bash
eval $(docker-machine env node-1)
TOKEN=$(docker swarm join-token -q worker)

for i in 2 3; do
    eval $(docker-machine env node-$i)
    docker swarm join \
    --token $TOKEN \
    --advertise-addr $(docker-machine ip node-$i) \
    $(docker-machine ip node-1):2377
done
```
