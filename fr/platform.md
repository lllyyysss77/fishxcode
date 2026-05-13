---
title: Navigation plateforme et guide système
description: Architecture FishXCode, fonctionnalités console, routes et référence documentaire.
---

# Navigation plateforme et guide système

> Cette page présente la structure globale de la plateforme FishXCode et vous aide à localiser rapidement les fonctionnalités dont vous avez besoin.
> Besoin de créer un compte ? Consultez [Création de compte et gestion](/fr/account).

## 1. Vue d'ensemble

FishXCode est une plateforme d'accès IA destinée aux développeurs et aux équipes. Considérez-la comme un point d'entrée unifié : en façade, le site vitrine, les tarifs et la documentation ; en arrière-plan, la console, les tokens, la recharge, la facturation et l'historique d'utilisation.

La plateforme fournit un service de relais autour des principaux modèles IA. Les canaux et modèles disponibles sont susceptibles de changer — consultez la [page Tarifs](https://fishxcode.com/pricing) pour les informations à jour. De l'inscription à la création de tokens, en passant par le choix d'un forfait, la connexion des outils et le suivi de la consommation, l'ensemble du parcours s'effectue au sein du même système.

---

## 2. Modules du site

Le système se compose de 4 modules principaux :

| Route | Page | Module | Description |
|---|---|---|---|
| `/` | Page d'accueil | Site vitrine | Présentation des capacités de la plateforme, affichage des annonces, orientation vers l'inscription ou la documentation |
| `/pricing` | Tarifs | Site vitrine | Consultation des prix des modèles, des forfaits et des multiplicateurs de consommation par groupe |
| `/login` | Connexion | Centre d'authentification | Connexion par nom d'utilisateur / e-mail, prise en charge de GitHub et LinuxDO |
| `/register` | Inscription | Centre d'authentification | Point d'entrée pour les nouveaux utilisateurs |
| `/console` | Dashboard | Console | Page de synthèse : solde, statistiques de consommation, nombre de requêtes et notifications système |
| `/console/package` | Gestion des forfaits | Console | Consultation et souscription de forfaits, affichage des modèles couverts |
| `/console/token` | Gestion des tokens | Console | Création, copie et suppression de clés API, gestion des groupes et des dates d'expiration |
| `/console/log` | Journal d'utilisation | Console | Consultation de chaque appel : horodatage, modèle, consommation et détails |
| `/console/topup` | Recharge | Console | Rechargement du solde, utilisation de codes de recharge, historique des rechargements |
| `/console/invoice` | Gestion des factures | Console | Soumission de demandes de facturation, suivi de l'avancement |
| `/console/invite` | Parrainage | Console | Lien d'invitation, règles de récompense, gains et classement |
| `/console/personal` | Paramètres personnels | Console | Informations du compte, groupe par défaut, récompenses de connexion quotidienne |
| `/status` | État du service | Page publique | Vérification du fonctionnement de chaque groupe de service |
| `/contact` | Nous contacter | Page publique | Canaux de contact officiels et support après-vente |
| `/docs` | Centre de documentation | Page publique | Tutoriels d'intégration, guides d'outils et FAQ |

---

## 3. Référence rapide console

Après connexion, toutes les opérations sont centralisées dans la console. Retrouvez rapidement ce dont vous avez besoin par cas d'usage :

### Mise en service

1. Créer un compte → Se connecter à la console
2. **Gestion des tokens** : créer une clé API (choisir le groupe approprié)
3. Configurer le Base URL et la clé API dans votre outil

### Achats et recharge

- **Gestion des forfaits** : consulter les forfaits disponibles, souscrire le plan adapté
- **Page de recharge** : paiement par Alipay, WeChat ou code de recharge
- **Consultation du solde** : affiché en temps réel sur la page d'accueil du Dashboard

### Gestion de l'utilisation

- **Journal d'utilisation** : historique de chaque appel, incluant le modèle, la consommation, l'IP et les détails
- **Gestion des tokens** : état des tokens, portée des groupes, suppression des tokens obsolètes
- **Paramètres personnels** : modification du groupe par défaut, récompenses de connexion quotidienne et statistiques de base

### Support et assistance

- **Gestion des factures** : soumission de demandes de facturation pour les entreprises ou les besoins de remboursement
- **Parrainage** : invitation de nouveaux utilisateurs via un lien de parrainage et suivi des gains
- **État du service** : vérification du fonctionnement des groupes en cas d'anomalie lors des appels
- **Nous contacter** : assistance pour les problèmes d'utilisation, les questions commerciales et le support après-vente

---

## 4. Référence documentaire

`/docs` correspond au centre de documentation FishXCode. Son contenu est en correspondance directe avec le site de documentation actuel :

| Page de documentation | Route | Contenu |
|---|---|---|
| Accueil | `/` | Page d'accueil de la documentation et vue d'ensemble du produit |
| Démarrage rapide | `/start` | Tutoriel d'intégration de Claude Code |
| Création de compte | `/account` | Inscription, connexion, tokens, recharge et gestion du compte |
| Intégration ZCF | `/zcf` | Workflow ZCF et configuration MCP |
| OpenAI Codex | `/codex` | Guide d'intégration Codex |
| Cursor | `/cursor` | Guide d'intégration Cursor |
| RooCode | `/roocode` | Guide d'intégration RooCode |
| Qwen Code | `/qwencode` | Guide d'intégration Qwen Code |
| Droid CLI | `/droid` | Guide d'intégration Droid CLI |
| OpenCode | `/opencode` | Guide d'intégration OpenCode |
| OpenClaw | `/openclaw` | Guide d'intégration OpenClaw |
| WorkBuddy | `/workbuddy` | Guide d'intégration WorkBuddy |
| CodeBuddy | `/codebuddy` | Guide d'intégration CodeBuddy |
| QClaw | `/qclaw` | Guide d'intégration QClaw |
| Comparaison d'outils | `/compare` | Comparaison entre les différents outils |
| Modèles supportés | `/models` | Liste des modèles et périmètre de fonctionnalités |
| FAQ | `/faq` | Questions fréquentes et dépannage |
| Journal des modifications | `/changelog` | Historique des mises à jour |
| Conditions d'utilisation | `/terms` | Conditions de la plateforme |
| Politique de remboursement | `/refund` | Remboursement du solde, remboursement/conversion des forfaits et règles de compensation |
| Politique de confidentialité | `/privacy` | Informations relatives à la confidentialité |

::: tip En résumé
L'utilisateur commence par découvrir la plateforme via le site vitrine, puis crée un compte et active le service via la console. Les pages tokens, forfaits, recharge et journal d'utilisation couvrent l'ensemble du cycle d'utilisation, tandis que la documentation et la page d'état assurent l'information et le support.
:::
