# 05 - DHCP Kea, DNS Bind9 et Traefik

## DHCP Kea

Le service Kea DHCP4 est installe directement sur le systeme Debian.
Le routeur Cisco transmet les requetes DHCP via `ip helper-address 192.168.50.20`.

| VLAN | Reseau | Plage | Passerelle | DNS |
|---|---|---|---|---|
| 10 | 192.168.10.0/24 | .10 a .250 | 192.168.10.254 | 192.168.50.20 |
| 20 | 192.168.20.0/24 | .10 a .250 | 192.168.20.254 | 192.168.50.20 |
| 30 | 192.168.30.0/24 | .10 a .250 | 192.168.30.254 | 192.168.50.20 |
| 40 | 192.168.40.0/24 | .10 a .250 | 192.168.40.254 | 8.8.8.8 |
| 50 | 192.168.50.0/24 | .10 a .250 | 192.168.50.254 | 192.168.50.20 |
| 99 | 192.168.99.0/24 | .10 a .250 | 192.168.99.254 | 192.168.50.20 |

Le VLAN 40 Guest utilise le DNS 8.8.8.8 pour interdire l'acces aux services internes.

```bash
sudo systemctl status kea-dhcp4-server
cat /var/lib/kea/kea-leases4.csv
```

## DNS Bind9

- Service : named, active au demarrage
- Zone maitre : iris-nice.lan
- Redirecteurs : 8.8.8.8 et 8.8.4.4

| Nom de domaine | Adresse IP |
|---|---|
| cloud.iris-nice.lan | 192.168.50.20 |
| grafana.iris-nice.lan | 192.168.50.20 |
| prometheus.iris-nice.lan | 192.168.50.20 |
| alertmanager.iris-nice.lan | 192.168.50.20 |
| ldap.iris-nice.lan | 192.168.50.20 |
| traefik.iris-nice.lan | 192.168.50.20 |

```bash
dig @192.168.50.20 cloud.iris-nice.lan
sudo systemctl status named
```

## Traefik v2.11

- Certificat SSL auto-signe, valide 10 ans, SAN DNS et IP
- Redirection HTTP vers HTTPS automatique
- Decouverte automatique des services via labels Docker
- En-tetes de securite : HSTS, X-Content-Type-Options, Referrer-Policy

Generation du certificat :

```bash
openssl req -x509 -nodes -days 3650 -newkey rsa:2048 \
  -keyout certs/iris-nice.key \
  -out certs/iris-nice.crt \
  -subj "/C=FR/ST=PACA/L=Nice/O=IRIS Nice/CN=iris-nice.lan" \
  -addext "subjectAltName=DNS:iris-nice.lan,DNS:*.iris-nice.lan,IP:192.168.50.20"
```
