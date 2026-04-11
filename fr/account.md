---
title: Création de compte et gestion
description: Inscription FishXCode, obtention de clé API, recharge et sécurité du compte.
---

# Création de compte et gestion

> Vous avez déjà un compte ? Rendez-vous directement sur [Démarrage rapide](/fr/start) pour configurer vos outils.
> Découvrir la plateforme ? Consultez le [Guide plateforme](/fr/platform).

## 1. Obtenir la clé API

### 1. Créer un compte

Rendez-vous sur [fishxcode.com](https://fishxcode.com/register?aff=9CTW) et cliquez sur **S'inscrire** :

![Page d'accueil FishXCode](/img/start/api-01-home.png)

Choisissez votre méthode d'inscription (GitHub, LinuxDO ou nom d'utilisateur) :

![Choix de la méthode d'inscription](/img/start/api-02-register.png)

Renseignez votre nom d'utilisateur, mot de passe et confirmation du mot de passe pour finaliser l'inscription :

![Formulaire d'inscription](/img/start/api-03-register-form.png)

### 2. Se connecter

Une fois inscrit, connectez-vous avec votre nom d'utilisateur et votre mot de passe :

![Page de connexion](/img/start/api-04-login.png)

Après connexion, vous accédez à la console :

![Page d'accueil de la console](/img/start/api-05-console.png)

### 3. Créer un token

Allez dans **Console → Gestion des tokens → Ajouter un token** et remplissez le formulaire :

![Ajouter un token](/img/start/api-06-token-create.png)

- Pour le **groupe de tokens**, choisissez de préférence le **canal officiel**. Ce groupe inclut l'ensemble des modèles Claude et sélectionne automatiquement le modèle selon la complexité de la tâche. Les canaux et modèles disponibles sont susceptibles de changer — consultez la [page Tarifs](https://fishxcode.com/pricing) pour les informations à jour.

Une fois créé, cliquez sur le bouton **Copier** dans la liste des tokens pour récupérer votre clé API (format : `sk-xxx`) :

![Copier le token](/img/start/api-07-token-copy.png)

### 4. Recharger le solde

Allez dans **Console → Gestion du portefeuille** ; les paiements Alipay, WeChat et codes de recharge sont acceptés :

![Page de recharge](/img/start/api-08-wallet.png)

| Méthode | Chemin |
|---|---|
| Alipay | Saisir/sélectionner le montant → Alipay |
| WeChat | Saisir/sélectionner le montant → WeChat |
| Code de recharge | Saisir le code dans la zone dédiée → Cliquer sur Échanger |

::: tip Multiplicateur de taux
Le `0.5x` dans le nom d'un groupe indique le multiplicateur de consommation. Plus la valeur est faible, plus vous obtenez de tokens. Par exemple, `0.5x` signifie 10 millions de tokens officiels pour 1 yuan (contre 5 millions au tarif officiel).
:::

---

## 2. Gestion des tokens

### Détails des groupes de tokens

Lors de la création d'un token, vous devez choisir un groupe qui détermine les modèles disponibles et le multiplicateur de consommation :

| Type de groupe | Description | Cas d'usage |
|---|---|---|
| **Canal officiel** | Inclut l'ensemble des modèles Claude, routage automatique | Recommandé pour la majorité des utilisateurs |
| **Groupes faible taux** | Multiplicateur de consommation réduit, meilleur rapport qualité-prix | Appels fréquents, scénarios sensibles aux coûts |

::: tip Recommandation
Si vous ne savez pas quel groupe choisir, sélectionnez simplement le **canal officiel**. Le système choisira automatiquement le modèle le plus adapté en fonction de la complexité de la tâche.
:::

### Recommandations de sécurité des tokens

- **Rotation régulière** : renouvelez vos tokens tous les 30 à 90 jours afin de réduire les risques de fuite
- **Pas de stockage en clair** : évitez d'inscrire votre clé API directement dans le code source ou de la committer dans un dépôt Git ; privilégiez les variables d'environnement
- **Un token par projet** : créez un token indépendant pour chaque projet afin de faciliter la gestion et le suivi
- **En cas de fuite** : si vous suspectez qu'un token a été compromis, supprimez-le immédiatement depuis la console et créez-en un nouveau

### Modifier et supprimer des tokens

Depuis la page **Console → Gestion des tokens** :

- **Consulter les détails** : cliquez sur le nom du token pour afficher le groupe, la portée des modèles et la date de création
- **Copier le token** : cliquez sur le bouton de copie dans la liste
- **Supprimer un token** : cliquez sur le bouton de suppression ; le token sera immédiatement invalidé et les outils qui l'utilisent ne pourront plus effectuer d'appels

::: warning Attention
La suppression est irréversible. Assurez-vous de ne plus utiliser le token avant de le supprimer.
:::

---

## 3. Sécurité du compte

### Mot de passe et sécurité de connexion

- Utilisez un **mot de passe fort** (au moins 8 caractères, incluant majuscules, minuscules et chiffres)
- N'utilisez pas le même mot de passe sur plusieurs plateformes
- En cas d'oubli, utilisez la procédure de réinitialisation disponible sur la page de connexion

### Connexion tierce

FishXCode prend en charge les méthodes de connexion tierces suivantes :

| Méthode | Description |
|---|---|
| **GitHub** | Recommandé pour les développeurs, connexion en un clic |
| **LinuxDO** | Connexion directe pour les membres de la communauté |

Après avoir lié un compte tiers, vous pouvez vous connecter directement via la plateforme correspondante, sans saisir de mot de passe.

### Détection d'anomalies et résolution

Si vous constatez l'une des situations suivantes, nous vous recommandons d'agir immédiatement :

1. **Consommation anormalement élevée** → Vérifiez si un token a été compromis ; si nécessaire, supprimez tous les tokens et recréez-en de nouveaux
2. **Impossibilité de se connecter** → Essayez la réinitialisation du mot de passe ; si le problème persiste, contactez le support
3. **Déduction de solde anormale** → Consultez le journal d'utilisation pour vérifier les appels enregistrés et confirmer s'il s'agit d'une consommation normale

::: tip Contacter le support
En cas d'anomalie sur votre compte, rendez-vous sur la page [Nous contacter](https://fishxcode.com/contact) pour obtenir de l'aide.
:::

---

## 4. Questions fréquentes

### Code de vérification non reçu ?

1. Vérifiez que l'adresse e-mail est correcte (orthographe et nom de domaine)
2. Consultez votre dossier de courrier indésirable (spam)
3. Patientez 1 à 2 minutes avant de réessayer ; évitez les demandes répétées
4. Si le problème persiste, essayez une autre adresse e-mail ou utilisez la connexion tierce

### Où retrouver les tokens créés ?

Rendez-vous dans **Console → Gestion des tokens**. Les tokens déjà créés apparaissent dans la liste. Cliquez sur le bouton de copie pour obtenir la clé API au format `sk-xxx`.

### Recharge non créditée ?

1. Vérifiez que le paiement a bien été effectué (consultez l'historique des transactions Alipay/WeChat)
2. Patientez 1 à 5 minutes ; un léger délai est possible
3. Actualisez la page du portefeuille dans la console
4. Si le solde n'apparaît toujours pas après 10 minutes, contactez le support en fournissant une capture d'écran du paiement

### Comment consulter le détail des consommations ?

Rendez-vous dans **Console → Journal d'utilisation** pour consulter, pour chaque appel API :
- La date/heure et le nom du modèle
- La quantité de tokens consommés et le coût
- L'adresse IP de la requête et les détails associés
