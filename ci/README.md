# Simple fortune cookie CI/CD with Jenkins

## Running

First set the permissions on the SSH private key as they are not supported by git.

```shell
./set_permissions.sh
```

```shell
docker compose up --build
```

## Reset

```shell
docker compose down -v
rm -rf jenkins grafana influxdb
```

## Connect to the builder with ssh

After `docker compose up --build`

```shell
ssh-keygen -R '[localhost]:9000'
ssh root@localhost -p 9000 -i secrets/id_ed25519
```

## Secrets

A sample SSH key is provided to allow access to
