# Sujet 5 - Architecture cible

## Metadonnees

- Sujet : 5 - Intranet + Annuaire + Cloud prive
- Version : 1.0
- Statut : cadrage cible
- Auteur : Codex
- Date : 2026-03-27

## Objectif

Mettre en place un environnement interne heberge sur des VMs permettant :

- un acces unifie aux services internes
- une gestion centralisee des utilisateurs via annuaire
- un espace cloud prive pour les fichiers, agendas et services collaboratifs
- une tracabilite minimale des connexions et actions d'administration

## Hypotheses

- le projet est heberge sur un hyperviseur local de type VirtualBox, VMware ou Proxmox
- l'architecture doit rester faisable dans un contexte pedagogique
- le sujet 2 reutilisera le pare-feu perimetrique et le reseau du sujet 5
- l'administration se fait depuis un poste interne dedie ou via VPN

## Choix d'architecture

Architecture cible recommandee :

- un pare-feu perimetrique OPNsense pour le routage, le filtrage et la future terminaison VPN
- un serveur d'annuaire dedie base sur OpenLDAP avec interface d'administration LDAP Account Manager
- un serveur cloud prive base sur Nextcloud
- un serveur de logs dedie pour centraliser les journaux utiles a l'exploitation

Cette cible privilegie la separation des roles pour simplifier le diagnostic, la securisation et la documentation.

## VMs et roles

| VM | Systeme | Role principal | Services |
| --- | --- | --- | --- |
| `fw-vpn-01` | OPNsense | passerelle, routage, filtrage | NAT, firewall, DNS resolver, NTP, OpenVPN a terme |
| `idm-01` | Debian 12 | annuaire centralise | OpenLDAP, LDAP Account Manager |
| `cloud-01` | Debian 12 | cloud prive et portail utilisateur | Nginx, PHP-FPM, Nextcloud, MariaDB, Redis |
| `log-01` | Debian 12 | centralisation des journaux | rsyslog, Loki, Grafana ou equivalent |

## Roles detailes

### `fw-vpn-01`

- point d'entree et de sortie unique du SI
- routage inter-reseaux internes
- application des ACL entre VLANs
- preparation du service VPN pour le sujet 2

### `idm-01`

- referentiel des utilisateurs et groupes
- base d'authentification pour Nextcloud
- interface web d'administration des comptes

### `cloud-01`

- publication du portail collaboratif via HTTPS
- stockage des fichiers
- gestion des agendas, contacts et applications collaboratives Nextcloud
- authentification deleguee a l'annuaire LDAP

### `log-01`

- reception des journaux en provenance du pare-feu et des serveurs
- conservation et consultation des evenements de connexion
- support a la demonstration et a l'audit

## Flux applicatifs cibles

| Source | Destination | Protocole | Port | Usage |
| --- | --- | --- | --- | --- |
| Poste utilisateur LAN | `cloud-01` | HTTPS | 443/TCP | acces portail Nextcloud |
| `cloud-01` | `idm-01` | LDAP | 389/TCP | recherche utilisateurs et groupes |
| `cloud-01` | `idm-01` | LDAPS | 636/TCP | variante cible securisee |
| Admin interne | `idm-01` | HTTPS | 443/TCP | administration LDAP Account Manager |
| Admin interne | `cloud-01` | SSH | 22/TCP | administration systeme |
| `fw-vpn-01` | `log-01` | Syslog | 514/UDP | journaux pare-feu |
| `idm-01` | `log-01` | Syslog | 514/UDP | journaux annuaire |
| `cloud-01` | `log-01` | Syslog / agent | 514/UDP | journaux application et systeme |

## Principes de securite

- aucune interface d'administration ne doit etre exposee directement sur Internet
- l'acces utilisateur au cloud se fait en HTTPS uniquement
- les comptes applicatifs sont relies a l'annuaire, pas geres localement dans chaque service
- les journaux techniques sont centralises sur une machine distincte
- la communication `cloud-01` vers `idm-01` doit evoluer vers LDAPS une fois les certificats internes poses

## Ordre de mise en oeuvre recommande

1. deployer `fw-vpn-01` et les reseaux internes
2. deployer `idm-01` et structurer l'annuaire
3. deployer `cloud-01` et raccorder Nextcloud a LDAP
4. deployer `log-01` et centraliser les journaux
5. valider les flux et la segmentation

## Resultat attendu

Le sujet 5 doit aboutir a un socle interne coherent, centralise et documente, capable d'etre etendu ensuite avec le VPN d'entreprise du sujet 2.
