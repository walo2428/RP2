# 02 - Services Docker

## Reseaux Docker

| Reseau | Role |
|---|---|
| traefik-network | Partage entre Traefik et les services exposes |
| ldap-network | Partage entre OpenLDAP, Nextcloud et Grafana |
| monitoring-network | Reseau interne stack monitoring |
| nextcloud-network | Reseau interne Nextcloud et PostgreSQL |

## Arborescence des fichiers

```
/opt/iris-services/
    ldap/
        docker-compose.yml
    nextcloud/
        docker-compose.yml
        .env
        volumes/
    monitoring/
        docker-compose.yml
        .env
        prometheus/
            prometheus.yml
            alerts.yml
            alertmanager.yml
            blackbox.yml
            snmp.yml
        loki/loki-config.yml
        promtail/promtail-config.yml
        grafana/ldap.toml
    traefik/
        docker-compose.yml
        traefik.yml
        dynamic.yml
        certs/
            iris-nice.crt
            iris-nice.key
    dhcp/
        kea-dhcp4.conf
```

## Commandes de base

```bash
# Demarrer tous les services
cd /opt/iris-services/ldap && docker compose up -d
cd /opt/iris-services/traefik && docker compose up -d
cd /opt/iris-services/nextcloud && docker compose up -d
cd /opt/iris-services/monitoring && docker compose up -d

# Verifier l'etat
docker ps --format "table {{.Names}}\t{{.Status}}"

# Redemarrer une stack
cd /opt/iris-services/nextcloud
docker compose down && docker compose up -d
```
