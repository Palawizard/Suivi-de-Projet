# Sujet 2 - Architecture cible

## Metadonnees

- Sujet : 2 - VPN d'entreprise
- Version : 1.0
- Statut : cadrage cible
- Auteur : Codex
- Date : 2026-03-27

## Objectif

Permettre aux collaborateurs et administrateurs d'acceder a distance aux ressources internes du sujet 5 de maniere securisee, tracee et maitrisee.

## Principe retenu

Le sujet 2 s'appuie sur le socle reseau et systeme du sujet 5.

Choix recommande :

- terminaison VPN sur `fw-vpn-01`
- technologie VPN : OpenVPN en mode acces distant
- authentification : certificat client + identifiants utilisateur
- journalisation : traces de connexion remontees sur `log-01`

Ce choix repond mieux au besoin de certificats et de demonstration d'un acces distant controle que WireGuard dans ce contexte pedagogique.

## Composants impliques

| Composant | Role |
| --- | --- |
| `fw-vpn-01` | serveur OpenVPN, autorite de certification locale, filtrage des acces VPN |
| `log-01` | centralisation des connexions VPN et journaux de securite |
| `cloud-01` | ressource principale accessible aux utilisateurs distants |
| `idm-01` | ressource d'administration et annuaire, acces restreint aux admins |
| poste client VPN | terminal nomade avec client OpenVPN et certificat |

## Profils d'acces

### Profil `users_nomades`

- acces a `cloud-01` en HTTPS
- resolution DNS interne
- aucun acces direct aux interfaces d'administration

### Profil `admins_it`

- acces a `cloud-01` en HTTPS et SSH
- acces a `idm-01` pour l'administration annuaire
- acces a `log-01` pour consultation des journaux

## Flux VPN cibles

| Source | Destination | Protocole | Port | Usage |
| --- | --- | --- | --- | --- |
| Internet | `fw-vpn-01` | OpenVPN | 1194/UDP | terminaison VPN distante |
| Client VPN | `fw-vpn-01` | DNS | 53/TCP-UDP | resolution DNS interne |
| Client VPN | `cloud-01` | HTTPS | 443/TCP | acces cloud prive |
| Client VPN admin | `cloud-01` | SSH | 22/TCP | administration systeme |
| Client VPN admin | `idm-01` | HTTPS | 443/TCP | administration annuaire |
| Client VPN admin | `idm-01` | SSH | 22/TCP | maintenance systeme |
| `fw-vpn-01` | `log-01` | Syslog | 514/UDP | journalisation des connexions VPN |

## Principes de securite

- aucune ressource interne n'est exposee directement sur Internet hors port VPN
- l'acces VPN impose un certificat client emis par l'autorite locale
- les droits d'acces sont differencies entre utilisateurs standards et administrateurs
- les ACL VPN limitent les flux a ce qui est strictement necessaire
- les journaux de connexion sont conserves pour la demonstration et l'audit

## Resultat attendu

Le sujet 2 doit fournir un acces distant fiable aux ressources internes, sans contourner la segmentation definie pour le sujet 5.
