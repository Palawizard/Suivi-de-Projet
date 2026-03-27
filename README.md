# Suivi de Projet

## Contexte

Ce dépôt centralise les livrables techniques du projet d'infrastructure Ynov.

Ordre de traitement retenu :

1. Sujet 5 : Intranet + Annuaire + Cloud prive
2. Sujet 2 : VPN d'entreprise

La gestion de projet est deja traitee en dehors de ce depot. Ici, le depot sert a produire et versionner :

- le cadrage technique
- l'architecture cible
- la documentation d'exploitation
- les schemas, captures et procedures

## Strategie Git

Branches de reference :

- `main` : branche stable, version de reference
- `dev` : branche d'integration
- `feat/*` : branches de travail par fonctionnalite ou lot documentaire

Convention de nommage pour les branches de travail :

- `feat/sujet5-socle`
- `feat/sujet2-vpn`
- `feat/docs-architecture`

Workflow cible :

1. creer une branche `feat/*` depuis `dev`
2. produire les livrables et committer par etape coherent
3. pousser la branche sur `origin`
4. fusionner dans `dev`
5. promouvoir ensuite `dev` vers `main`

## Structure du depot

```text
.
|-- README.md
`-- docs/
    |-- architecture/
    |-- procedures/
    |-- captures/
    `-- templates/
```

## Livrables attendus

- cadrage d'architecture Sujet 5
- cadrage d'architecture Sujet 2
- plan d'adressage
- schema reseau
- documentation d'administration et d'exploitation

## Regles documentaires

- tout document structurant est redige en Markdown
- une page = un sujet clair, un titre clair, une date de mise a jour
- les captures sont stockees dans `docs/captures/`
- les schemas source et exports sont stockes dans `docs/architecture/`
- les procedures sont redigees pour etre rejouables

## Suivi des etapes

Le travail est livre par etapes avec :

- ajout des fichiers
- commit Git
- push sur le depot distant
