# 01 - Architecture Reseau

## Plan d'adressage IP

| Equipement | Interface | Adresse IP | Masque | Role |
|---|---|---|---|---|
| Routeur Cisco 1941W | Gi0/0.10 | 192.168.10.254 | /24 | Passerelle VLAN 10 |
| Routeur Cisco 1941W | Gi0/0.20 | 192.168.20.254 | /24 | Passerelle VLAN 20 |
| Routeur Cisco 1941W | Gi0/0.30 | 192.168.30.254 | /24 | Passerelle VLAN 30 |
| Routeur Cisco 1941W | Gi0/0.40 | 192.168.40.254 | /24 | Passerelle VLAN 40 |
| Routeur Cisco 1941W | Gi0/0.50 | 192.168.50.254 | /24 | Passerelle VLAN 50 |
| Routeur Cisco 1941W | Gi0/0.99 | 192.168.99.254 | /24 | Passerelle VLAN 99 |
| Switch Catalyst 2960-S | Vlan50 | 192.168.50.253 | /24 | Management switch |
| Serveur Debian 12 | enp0s8 | 192.168.50.20 | /24 | Services Docker |

## Segmentation VLAN

| VLAN | Nom | Reseau | Passerelle | Usage |
|---|---|---|---|---|
| 10 | IRIS Etudiants | 192.168.10.0/24 | 192.168.10.254 | Postes etudiants |
| 20 | Profs | 192.168.20.0/24 | 192.168.20.254 | Postes enseignants |
| 30 | Administration | 192.168.30.0/24 | 192.168.30.254 | Personnel administratif |
| 40 | Guest | 192.168.40.0/24 | 192.168.40.254 | Invites, Internet uniquement |
| 50 | Management | 192.168.50.0/24 | 192.168.50.254 | Serveurs infrastructure |
| 99 | PRE_AUTH | 192.168.99.0/24 | 192.168.99.254 | VLAN par defaut avant auth |

## Politique d'acces par VLAN

| VLAN | Acces autorise |
|---|---|
| VLAN 10 Etudiants | Nextcloud uniquement |
| VLAN 20 Profs | Nextcloud et Grafana |
| VLAN 30 Administration | Acces complet |
| VLAN 40 Guest | Internet uniquement |
| VLAN 50 Management | Acces complet |
| VLAN 99 PRE_AUTH | En attente d'affectation VLAN |

## Configuration reseau serveur

Fichier `/etc/network/interfaces` :

```
auto enp0s8
iface enp0s8 inet static
    address 192.168.50.20
    netmask 255.255.255.0
    gateway 192.168.50.254
    dns-nameservers 192.168.50.20 8.8.8.8
```
