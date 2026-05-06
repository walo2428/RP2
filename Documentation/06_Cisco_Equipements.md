# 06 - Equipements Cisco

## Routeur Cisco 1941W

### Role

- NAT (translation des adresses vers Internet)
- ACL (controle d'acces par VLAN)
- Router-on-stick (routage inter-VLAN via sous-interfaces)
- Relais DHCP vers le serveur Kea (ip helper-address 192.168.50.20)

### Elements cles de configuration

```
! Sous-interfaces VLAN avec relais DHCP
interface GigabitEthernet0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.254 255.255.255.0
 ip helper-address 192.168.50.20

! (repeter pour VLAN 20, 30, 40, 50, 99)

! SNMP
snmp-server community iris-nice RO
snmp-server host 192.168.50.20 version 2c iris-nice

! Syslog
logging host 192.168.50.20
logging source-interface GigabitEthernet0/0.50
service timestamps log datetime msec
```

La configuration complete est disponible dans `configs/cisco/R1-IRIS.cfg`.

## Switch Catalyst 2960-S — SW3-IRIS

| Parametre | Valeur |
|---|---|
| Nom d'hote | SW3-IRIS |
| IP de management | 192.168.50.253 (Vlan50) |
| Passerelle | 192.168.50.254 |
| Port uplink routeur | Gi2/0/1 (trunk 802.1Q) |
| Ports management | Gi2/0/2 a Gi2/0/11 (VLAN 50) |
| Ports utilisateurs | Gi2/0/12 a Gi2/0/48 (VLAN 99, 802.1X) |
| VLANs configures | 10, 20, 30, 40, 50, 99 |
| SNMP | community iris-nice vers 192.168.50.20 |
| Syslog | logging host 192.168.50.20 UDP 514 |
| Acces distant | SSH uniquement |

La configuration complete est disponible dans `configs/cisco/SW3-IRIS.cfg`.

### Sauvegarde et restauration

```bash
# Sauvegarder la config vers le serveur TFTP
SW3-IRIS# copy running-config tftp:
Address: 192.168.50.20
Filename: SW3-IRIS.cfg

# Restaurer depuis le serveur TFTP
SW3-IRIS# copy tftp: startup-config
Address: 192.168.50.20
Filename: SW3-IRIS.cfg
SW3-IRIS# reload
```
