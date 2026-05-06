# 03 - OpenLDAP

OpenLDAP est deploye en conteneur Docker. Il constitue l'annuaire central d'authentification utilise par Nextcloud, Grafana et phpLDAPadmin.

- Image : osixia/openldap
- Domaine : dc=iris-nice,dc=lan
- Port : 389 TCP
- Reseau Docker : ldap-network

## Structure de l'annuaire

```
dc=iris-nice,dc=lan
    ou=Users
        uid=admin-iris    (Admins)
        uid=directeur     (Admins)
        uid=secretariat   (Admins)
        uid=ybourquard    (Profs)
        uid=tferrut       (Profs)
        uid=amsaid        (Etudiants SISR)
        uid=vandreo       (Etudiants SISR)
        uid=rbears        (Etudiants SISR)
        uid=nbelloum      (Etudiants SISR)
        uid=llavenir      (Etudiants SISR)
        uid=jmarcucci     (Etudiants SISR)
        uid=tquenette     (Etudiants SISR)
        uid=esaoud        (Etudiants SISR)
        uid=otalibi       (Etudiants SISR)
        uid=hthouvenin    (Etudiants SISR)
        uid=yadidi        (Etudiants SLAM)
        uid=mboukhatem    (Etudiants SLAM)
        uid=kjuhasz       (Etudiants SLAM)
        uid=dlyulchak     (Etudiants SLAM)
        uid=ksenasson     (Etudiants SLAM)
    ou=Groups
        cn=Admins
        cn=Profs
        cn=Etudiants
```

## Utilisateurs

### Professeurs — groupe Profs

| Nom complet | Identifiant |
|---|---|
| Yan Bourquard | ybourquard |
| Terrence Ferrut | tferrut |

### Etudiants SISR — groupe Etudiants

| Nom complet | Identifiant |
|---|---|
| Ahmed Moussa Said | amsaid |
| Vincent Andreo | vandreo |
| Remi Bears | rbears |
| Nedj Belloum | nbelloum |
| Louka Lavenir | llavenir |
| Julien Marcucci | jmarcucci |
| Tiago Quenette | tquenette |
| Edib Saoud | esaoud |
| Omar Talibi | otalibi |
| Hendrik Thouvenin | hthouvenin |

### Etudiants SLAM — groupe Etudiants

| Nom complet | Identifiant |
|---|---|
| Yanis Adidi | yadidi |
| Mohamed Boukhatem | mboukhatem |
| Klaudia Juhasz | kjuhasz |
| Denys Lyulchak | dlyulchak |
| Kevin Senasson | ksenasson |

### Administration — groupe Admins

| Nom complet | Identifiant |
|---|---|
| Administrateur IRIS | admin-iris |
| Directeur IRIS | directeur |
| Secretariat IRIS | secretariat |

Les mots de passe ne sont pas publies dans ce depot.

## Mapping groupes vers roles Grafana

| Groupe LDAP | Role Grafana |
|---|---|
| Admins | Admin |
| Profs | Editor |
| Etudiants | Viewer |

## Commandes de verification

```bash
# Lister toutes les entrees
docker exec openldap ldapsearch -x \
  -H ldap://localhost \
  -D "cn=admin,dc=iris-nice,dc=lan" \
  -w "***" \
  -b "dc=iris-nice,dc=lan" "(objectClass=*)" dn

# Tester connexion LDAP depuis Nextcloud
docker exec -u 33 nextcloud-nextcloud-1 php occ ldap:test-config s01

# Tester connectivite depuis Grafana
docker exec monitoring-grafana-1 curl -s ldap://openldap:389
```
