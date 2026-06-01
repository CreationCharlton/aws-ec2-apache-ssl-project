# Déploiement d'un site Web sur AWS EC2 avec Apache, DNS personnalisé et SSL Let's Encrypt

## Présentation

Ce projet documente le déploiement complet du site **Tamba Logistics** sur une infrastructure AWS. L'objectif était de concevoir, sécuriser et publier un site Web professionnel accessible via un nom de domaine personnalisé et protégé par HTTPS.

**Site Web :** https://www.tambalogistics.com

---

# Objectifs du projet

- Déployer un site Web sur une instance AWS EC2
- Configurer Apache HTTPD comme serveur Web
- Associer un nom de domaine personnalisé acheté chez GoDaddy
- Configurer les enregistrements DNS
- Sécuriser les accès SSH
- Mettre en place HTTPS avec Let's Encrypt
- Comprendre les notions de routage, NAT, DNS et Virtual Hosts
- Documenter l'architecture réseau complète

---

# Technologies utilisées

## Cloud

- Amazon Web Services (AWS)
- EC2
- VPC
- Security Groups

## Système

- Amazon Linux
- SSH
- Linux CLI

## Services Web

- Apache HTTPD
- Virtual Hosts
- SSL/TLS
- Let's Encrypt
- Certbot

## Réseau

- TCP/IP
- DHCP
- NAT
- DNS
- HTTPS
- Routage Internet

---

# Architecture réseau

## Réseau local

Mon poste de travail est connecté à un routeur Wi‑Fi.

### Adressage interne

| Équipement | Adresse |
|------------|----------|
| Routeur | 192.168.1.1 |
| Poste client | 192.168.1.110 |

Le routeur attribue automatiquement les adresses IP via DHCP.

### Accès Internet

Le routeur effectue une traduction d'adresses (NAT) afin de permettre aux équipements du réseau local d'accéder à Internet.

Adresse IP publique du routeur :

209.107.104.32

---

## Infrastructure AWS

### Instance EC2

| Élément | Valeur |
|----------|---------|
| Système d'exploitation | Amazon Linux |
| Service DNS | Hébergé sur l'instance |
| Serveur Web | Apache HTTPD |
| IP privée | 172.131.2.192 |
| IP publique | 3.146.52.130 |

### Domaine

- tambalogistics.com
- www.tambalogistics.com

---

# Configuration DNS

Le domaine a été enregistré chez GoDaddy.

## Enregistrement A

| Type | Nom | Valeur |
|--------|--------|--------|
| A | @ | 3.146.52.130 |

## Enregistrement CNAME

| Type | Nom | Valeur |
|--------|--------|--------|
| CNAME | www | tambalogistics.com |

Cette configuration permet de faire pointer le domaine vers l'instance EC2.

---

# Déploiement du site

Les fichiers du site ont été développés localement puis transférés vers l'instance EC2 à l'aide de SCP.

Exemple :

```bash
scp -i KEY.pem -r Tamba ec2-user@3.146.52.130:~
```

Les fichiers ont ensuite été copiés dans le répertoire Web :

```text
/var/www/html
```

---

# Installation d'Apache HTTPD

Installation :

```bash
sudo dnf install httpd -y
```

Activation du service :

```bash
sudo systemctl enable httpd
sudo systemctl start httpd
```

Validation :

```bash
sudo systemctl status httpd
```

---

# Configuration des Virtual Hosts

Configuration du domaine principal :

```apache
<VirtualHost *:80>
    ServerName tambalogistics.com
    ServerAlias www.tambalogistics.com
    DocumentRoot /var/www/html
</VirtualHost>
```

Cette configuration permet à Apache de servir le bon contenu en fonction du nom de domaine demandé.

---

# Sécurisation de l'accès SSH

L'accès SSH a été limité à une seule adresse IP publique autorisée.

Mesures appliquées :

- Authentification par clé privée (.pem)
- Restriction de l'accès SSH via Security Groups
- Désactivation de l'accès public généralisé

Port utilisé :

```text
22/TCP
```

---

# Configuration du Security Group AWS

## Règles entrantes

| Service | Port | Source |
|----------|------|---------|
| SSH | 22 | IP autorisée uniquement |
| HTTP | 80 | 0.0.0.0/0 |
| HTTPS | 443 | 0.0.0.0/0 |

### Sécurité supplémentaire

- ICMP (Ping) bloqué
- SSH restreint
- HTTP et HTTPS ouverts au public

---

# Mise en place du certificat SSL

Obtention du certificat Let's Encrypt :

```bash
sudo certbot certonly --webroot -w /var/www/html -d tambalogistics.com -d www.tambalogistics.com
```

Emplacement des certificats :

```text
/etc/letsencrypt/live/tambalogistics.com/
```

---

# Configuration HTTPS

```apache
<VirtualHost *:443>

ServerName tambalogistics.com
ServerAlias www.tambalogistics.com

DocumentRoot /var/www/html

SSLEngine on

SSLCertificateFile /etc/letsencrypt/live/tambalogistics.com/fullchain.pem
SSLCertificateKeyFile /etc/letsencrypt/live/tambalogistics.com/privkey.pem

</VirtualHost>
```

Résultat :

- Site sécurisé par HTTPS
- Certificat reconnu par les navigateurs
- Chiffrement des communications

---

# Défis rencontrés

Au cours du projet, plusieurs problèmes techniques ont été résolus :

- Configuration DNS incorrecte
- Conflits d'enregistrements CNAME
- Déploiement SCP vers EC2
- Permissions Linux
- Configuration Apache
- Validation Let's Encrypt
- Ouverture des ports HTTP/HTTPS
- Débogage réseau et sécurité

Ces étapes ont permis d'acquérir une expérience concrète de résolution d'incidents.

---

# Résultats obtenus

Le projet final comprend :

- Hébergement Web sur AWS
- Domaine personnalisé
- DNS fonctionnel
- Apache HTTPD opérationnel
- HTTPS actif
- Certificat SSL valide
- Sécurisation SSH
- Virtual Hosts
- Gestion des accès réseau
- Documentation complète

---

# Compétences démontrées

## Administration système

- Linux
- Gestion des services
- Gestion des permissions

## Réseau

- TCP/IP
- DHCP
- NAT
- DNS
- Routage

## Cloud Computing

- AWS EC2
- VPC
- Security Groups

## Hébergement Web

- Apache HTTPD
- Virtual Hosts
- SSL/TLS
- Let's Encrypt

## Dépannage

- Analyse réseau
- Diagnostic de services
- Résolution de problèmes de connectivité
- Débogage DNS et HTTPS

---

# Captures et documentation

Ajouter dans le dépôt GitHub :

```text
/docs
├── architecture-reseau.png
├── dns-godaddy.png
├── ec2-security-group.png
├── apache-virtualhost.png
├── ssl-letsencrypt.png
└── site-final.png
```

---

# Auteur

**Boubakar Maguiraga** aussi connu sous le pseudonyme de **Creation Charlton**

Projet personnel réalisé dans le cadre du développement de compétences en administration système, réseaux et technologies Cloud.

Infrastructure déployée sur AWS.
