# 04 - Stack Monitoring LGP

## Prometheus

- URL : https://prometheus.iris-nice.lan
- Targets supervises : node_exporter, blackbox, snmp_exporter, prometheus
- Page des alertes : https://prometheus.iris-nice.lan/alerts

## Alertmanager

URL : https://alertmanager.iris-nice.lan

| Regle | Condition | Severite |
|---|---|---|
| CPUHigh | CPU superieur a 80% pendant 5 minutes | warning |
| MemoryHigh | RAM superieure a 90% | warning |
| DiskFull | Disque superieur a 85% | critical |
| ServiceDown | Service HTTP non joignable | critical |
| InstanceDown | Target Prometheus indisponible | critical |
| HighLoad | Charge systeme elevee | warning |

## Grafana

- URL : https://grafana.iris-nice.lan
- Authentification : LDAP OpenLDAP Docker (host=openldap)
- Sources de donnees : Prometheus et Loki
- Tableaux de bord : Node Exporter Full, SNMP Cisco (ID 11169)

## Loki

- Retention des journaux : 8760 heures soit 12 mois
- Conformite LCEN respectee
- Collecte via Promtail : journaux syslog et conteneurs Docker

## Supervision SNMP

| Equipement | IP | Community SNMP | Version |
|---|---|---|---|
| Routeur Cisco 1941W | 192.168.50.254 | iris-nice | SNMPv2c |
| Switch Catalyst 2960-S | 192.168.50.253 | iris-nice | SNMPv2c |

OIDs collectes via IF-MIB :

| OID | Description |
|---|---|
| ifOperStatus | Etat operationnel des interfaces |
| ifInOctets | Volume de trafic entrant |
| ifOutOctets | Volume de trafic sortant |
| ifDescr | Description de l'interface |

## Centralisation des journaux Cisco

Chaine de collecte :

```
Equipements Cisco (syslog IOS)
    --> rsyslog (UDP 514)
    --> /var/log/cisco/<IP>.log
    --> Promtail
    --> Loki (retention 8760 heures)
    --> Grafana (visualisation)
```

Conformite LCEN : retention 8760 heures = 12 mois.
