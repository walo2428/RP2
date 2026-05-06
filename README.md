# RP-02 — Supervision, Monitoring et Services Web Internes — IRIS Nice

BTS SIO option SISR — Session 2026 — TALIBI Omar
Mediaschool IRIS Nice — Periode : 23/03/2026 au 10/04/2026

---

## Contexte

Dans le cadre du BTS SIO SISR, reponse a l'appel d'offre RP-03 de l'ecole IRIS Nice.
L'ecole ne disposait d'aucun service web interne securise, d'aucune supervision centralisee et d'aucune centralisation des journaux systeme.

Ce projet deploie une infrastructure complete de supervision, monitoring et services web internes sur un serveur Debian 12, entierement conteneurisee avec Docker.

---

## Architecture

```
                         Internet
                            |
                    Routeur Cisco 1941W
                     192.168.50.254
                  NAT | ACL | Router-on-stick
                  ip helper-address 192.168.50.20
                            |
                     Trunk 802.1Q
                            |
                  Switch Catalyst 2960-S
                     SW3-IRIS - 192.168.50.253
                            |
              +-------------+-------------+
              |                           |
     Utilisateurs filaires          Serveur Debian 12
                                     192.168.50.20
                                    VLAN 50 Management
                                     Docker Engine 29
                            |
              VLANs dynamiques (10, 20, 30, 40, 50, 99)
```

---

## Composants

| Composant | Technologie | IP |
|---|---|---|
| Serveur Linux | Debian 12 + Docker Engine 29 | 192.168.50.20 |
| Switch | Cisco Catalyst 2960-S SW3-IRIS | 192.168.50.253 |
| Routeur | Cisco 1941W NAT, ACL, Router-on-stick | 192.168.50.254 |

---

## Segmentation reseau — 6 VLANs

| VLAN | Nom | Reseau | Usage |
|---|---|---|---|
| 10 | IRIS Etudiants | 192.168.10.0/24 | Postes etudiants |
| 20 | Profs | 192.168.20.0/24 | Postes enseignants |
| 30 | Administration | 192.168.30.0/24 | Personnel administratif |
| 40 | Guest | 192.168.40.0/24 | Invites, Internet uniquement |
| 50 | Management | 192.168.50.0/24 | Serveurs infrastructure |
| 99 | PRE_AUTH | 192.168.99.0/24 | VLAN par defaut avant authentification |

---

## Services deployes

### Conteneurs Docker (14)

| Service | Image | Port | URL | Auth |
|---|---|---|---|---|
| OpenLDAP | osixia/openldap | 389 | - | - |
| Nextcloud Hub 28 | nextcloud:28 | 8080 | cloud.iris-nice.lan | LDAP |
| PostgreSQL 15 | postgres:15 | - | - | - |
| phpLDAPadmin | osixia/phpldapadmin | 8081 | ldap.iris-nice.lan | LDAP |
| Prometheus | prom/prometheus | 9090 | prometheus.iris-nice.lan | - |
| Grafana OSS | grafana/grafana | 3000 | grafana.iris-nice.lan | LDAP |
| Loki | grafana/loki | - | - | - |
| Promtail | grafana/promtail | - | - | - |
| Alertmanager | prom/alertmanager | 9093 | alertmanager.iris-nice.lan | - |
| Node Exporter | prom/node-exporter | - | - | - |
| Blackbox Exporter | prom/blackbox-exporter | - | - | - |
| SNMP Exporter | prom/snmp-exporter | 9116 | - | - |
| Traefik v2.11 | traefik:v2.11 | 80/443 | traefik.iris-nice.lan | BasicAuth |

### Services systeme (3)

| Service | Port | Role |
|---|---|---|
| Kea DHCP4 | 67/UDP | Attribution des IPs par VLAN |
| Bind9 DNS | 53 | Resolution de noms iris-nice.lan |
| rsyslog | 514/UDP | Reception des journaux Cisco |

---

## URLs des services

| Service | URL |
|---|---|
| Nextcloud | https://cloud.iris-nice.lan |
| Grafana | https://grafana.iris-nice.lan |
| Prometheus | https://prometheus.iris-nice.lan |
| Alertmanager | https://alertmanager.iris-nice.lan |
| phpLDAPadmin | https://ldap.iris-nice.lan |
| Traefik Dashboard | https://traefik.iris-nice.lan |

Le certificat SSL est auto-signe. Accepter l'avertissement navigateur au premier acces.

---

## Structure du projet

```
.
+-- README.md
+-- .gitignore
+-- .env.example
+-- Documentation/
|   +-- 01_Architecture_Reseau.md
|   +-- 02_Services_Docker.md
|   +-- 03_OpenLDAP.md
|   +-- 04_Monitoring_LGP.md
|   +-- 05_DHCP_DNS_Traefik.md
|   +-- 06_Cisco_Equipements.md
|   +-- 07_Procedures_Exploitation.md
+-- configs/
|   +-- docker/
|   |   +-- nextcloud/docker-compose.yml
|   |   +-- monitoring/docker-compose.yml
|   |   +-- monitoring/prometheus/
|   |   +-- monitoring/grafana/ldap.toml
|   |   +-- monitoring/loki/loki-config.yml
|   |   +-- monitoring/promtail/promtail-config.yml
|   |   +-- traefik/traefik.yml + dynamic.yml
|   |   +-- ldap/docker-compose.yml
|   +-- cisco/
|   |   +-- SW3-IRIS.cfg
|   |   +-- R1-IRIS.cfg
|   +-- dns/
|   |   +-- iris-nice.lan.db
|   +-- dhcp/
|       +-- kea-dhcp4.conf
+-- schema/
    +-- schema-reseau.png
```

---

## Auteur

TALIBI Omar — BTS SIO option SISR — IRIS Nice — Session 2026
