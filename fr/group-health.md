---
title: Etat de sante des groupes
description: Etat de sante des groupes FishXCode, details des tokens d'equipe, colonnes d'export et procedure de diagnostic.
---

# Etat de sante des groupes

L'etat de sante des groupes sert a distinguer une erreur isolee d'un probleme concentre sur un forfait, un modele, un groupe amont ou un membre d'equipe. Les administrateurs entreprise et equipe peuvent l'utiliser pour repondre rapidement a trois questions :

- Quel groupe a un taux de succes plus bas sur la periode choisie
- Quel utilisateur ou token concentre le volume de requetes, le cout ou les erreurs
- L'erreur est-elle limitee a un token ou touche-t-elle deja tout le groupe

Quand une API echoue, consultez d'abord l'etat de sante des groupes, puis ouvrez le journal d'utilisation de la requete pour retrouver le `request_id`.

::: info Perimetre des donnees
La page publique `status` integree ici interroge l'etat de sante des groupes utilises par tous les utilisateurs FishXCode sur la periode selectionnee. Elle reflete la disponibilite globale des groupes de la plateforme, avec un suivi en temps reel, impartial et stable.

La vue **Journaux d'utilisation -> Etat de sante des groupes** dans la console affiche les donnees visibles selon les droits du compte courant. Un utilisateur personnel voit generalement ses propres tokens ; les administrateurs entreprise et equipe peuvent analyser l'utilisation par utilisateur, nom d'utilisateur, token et groupe.
:::

<iframe
  src="https://status.fishxcode.com/group/global?view=list&sort=group&window=24h"
  title="Etat de sante des groupes FishXCode"
  loading="lazy"
  referrerpolicy="no-referrer-when-downgrade"
  style="width: 100%; height: 720px; border: 1px solid var(--vp-c-divider); border-radius: 8px; background: var(--vp-c-bg-soft);"
></iframe>

Si la page d'etat ci-dessus ne se charge pas correctement, ouvrez directement [l'etat de sante des groupes FishXCode](https://status.fishxcode.com/group/global?view=list&sort=group&window=24h).

Entree console : [Console -> Journaux d'utilisation](https://fishxcode.com/console/log). Dans les journaux d'erreur ou les vues statistiques, filtrez par periode, modele, token, groupe, message d'erreur et code de statut.

## Exemple console

Voici une capture d'exemple de **Journaux d'utilisation -> Etat de sante des groupes**. Elle montre le taux de succes, le nombre de requetes, le cout, les donnees de cache, le temps moyen, la derniere requete et les causes d'echec.

![Exemple d'etat de sante des groupes dans la console](/img/group-health.png)

::: tip Principe
Evaluez d'abord l'impact, puis traitez l'erreur unitaire. Un journal unitaire sert a diagnostiquer une requete precise ; l'etat de sante des groupes sert a voir si le probleme est concentre.
:::

Pour expliquer un message d'erreur unitaire, consultez le [guide des journaux d'erreur](/fr/error-logs).

## Colonnes de la liste

La liste de la console et l'export CSV utilisent les memes colonnes d'affichage. La liste contient deux types de lignes :

- **Ligne groupe** : resume l'etat global d'un groupe sur la periode choisie.
- **Ligne token** : affiche les details utilisateur et token sous un groupe, pour aider les administrateurs entreprise et equipe a localiser un membre, un projet ou un service.

| Colonne affichee | Lignes concernees | Description | Utilisation |
|------------------|-------------------|-------------|-------------|
| Type | Ligne groupe, ligne token | Indique si la ligne est un resume `Groupe` ou un detail `Token` | Voir d'abord les lignes groupe, puis les lignes token pour localiser le membre ou token |
| Groupe | Ligne groupe, ligne token | Groupes vus sur la periode choisie, incluant groupes a l'usage, groupes de forfait, groupe par defaut ou groupe de modele | Voir si le probleme se concentre sur un forfait, un modele ou un pool amont |
| ID utilisateur | Ligne token | ID de l'utilisateur qui a utilise le token | Localiser le compte membre lors d'un diagnostic entreprise |
| Nom d'utilisateur | Ligne token | Nom d'utilisateur qui a utilise le token | Rapports d'equipe, communication avec le membre et controle des droits |
| Token | Ligne token | Nom du token configure dans la console | Verifier si l'anomalie est limitee a un token |
| Taux de succes | Ligne groupe, ligne token | Taux de succes = requetes reussies / total des requetes | A surveiller sous 80 % ; si le taux est nettement plus bas que les lignes voisines, verifier ce groupe ou token en priorite |
| Requetes | Ligne groupe, ligne token | Nombre total de requetes sur la periode choisie | Ne pas sur-interpreter le taux de succes si l'echantillon est faible |
| Succes | Ligne groupe, ligne token | Nombre de requetes reussies ayant retourne 2xx | Le lire avec Requetes et Erreurs pour evaluer la disponibilite |
| Erreurs | Ligne groupe, ligne token | Nombre de requetes en erreur (4xx/5xx) | Si le nombre monte, verifier d'abord Cause d'echec et les journaux d'erreur |
| Cout | Ligne groupe, ligne token | Consommation de quota/cout cumulee sur la periode, exportee au format monetaire de la console | Comptabilite d'equipe, repartition par projet et detection de cout anormal |
| Taux de cache | Ligne groupe, ligne token | Taux de cache = tokens en cache / total des tokens | Plus il est haut, plus c'est economique ; les parties en cache sont souvent facturees moins cher ou gratuitement |
| Tokens en cache | Ligne groupe, ligne token | Nombre de tokens ayant touche le cache sur la periode | Cette partie est souvent facturee avec une forte reduction ; plus il y en a, plus l'economie est grande |
| Requetes en cache | Ligne groupe, ligne token | Nombre de requetes ayant touche le cache au moins une fois | Mesurer combien de requetes ont vraiment utilise le cache |
| Part requetes cache | Ligne groupe, ligne token | Part requetes cache = requetes avec cache / total des requetes | Plus elle est haute, plus d'appels beneficient de la remise cache |
| Tokens cache moyens | Ligne groupe, ligne token | Nombre moyen de tokens par hit de cache | Comparer l'efficacite de reutilisation entre membres, services ou groupes |
| Temps moyen | Ligne groupe, ligne token | Temps moyen par requete, en secondes | Plus il est bas, plus l'amont repond vite ; si le temps monte, verifier longs contextes, longues sorties et chaines d'outils |
| Heure de debut | Ligne groupe, ligne token | Premiere apparition de ce groupe ou token dans la periode courante | Situer le debut du probleme ou du trafic |
| Derniere requete | Ligne groupe, ligne token | Apparition la plus recente de ce groupe ou token dans la periode courante | Voir si le probleme ou le trafic continue |
| Cause d'echec | Ligne groupe | Principales causes d'echec par frequence, avec code de statut et nombre ; vide ou `-` sans erreur | Traiter d'abord l'erreur la plus frequente, pas seulement la derniere ligne |

::: info Source des champs
Les colonnes affichees sont generees a partir de statistiques agregees. Pour l'usage quotidien, prenez la liste console et l'export CSV comme reference ; ne faites le lien avec les noms de champs bruts que pour une integration API ou un diagnostic technique.
:::

::: tip Diagnostic d'equipe
Regardez d'abord les lignes groupe pour savoir s'il s'agit d'un probleme de pool de ressources, puis les lignes token pour voir si un utilisateur ou un token en est la cause. Si le taux de succes du groupe est normal mais qu'un token a beaucoup d'erreurs, verifiez d'abord le token, le nom du modele, la configuration cliente ou le corps de requete de ce membre.
:::

## Export CSV

L'export CSV reprend les memes colonnes que la liste courante. Il convient aux rapports hebdomadaires, a la repartition des couts, aux revues d'incident et au rapprochement d'usage des membres.

Apres export, vous pouvez previsualiser le fichier avec le [visualiseur CSV en ligne](https://tools.beer/zh/csv/viewer/). Il prend en charge le glisser-deposer ou la selection d'un fichier CSV, ainsi que le collage de texte CSV, pratique pour verifier rapidement les colonnes et les causes d'echec.

| Comportement export | Description |
|---------------------|-------------|
| Ligne groupe | `Type` vaut `Groupe` ; ID utilisateur, nom d'utilisateur et token sont generalement vides, ce qui represente le resume du groupe |
| Ligne token | `Type` vaut `Token` ; ID utilisateur, nom d'utilisateur et token sont affiches, ce qui represente le detail membre ou token sous le groupe |
| Format monetaire | `Cout` utilise le format monetaire de la console, par exemple `¥905.48` |
| Format pourcentage | Taux de succes, taux de cache et part requetes cache sont exportes en pourcentage |
| Format numerique | Les grands nombres peuvent contenir des separateurs de milliers, utiles pour lecture directe ou import tableur |
| Format temporel | Heure de debut et Derniere requete sont exportees en heure locale pour les aligner avec l'incident |
| Cause d'echec | Plusieurs erreurs frequentes sont fusionnees avec leur nombre d'occurrences ; vide ou `-` sans erreur |

## Procedure de diagnostic

### 1. Evaluer l'impact

Regardez d'abord les lignes ou `Type=Groupe`. Si le Taux de succes reste proche du niveau habituel et que les Erreurs sont faibles, il s'agit souvent d'une erreur occasionnelle. Copiez le `request_id` de la requete et poursuivez dans le journal unitaire.

Si le Taux de succes d'un groupe est clairement plus bas que les autres, ou si les Erreurs augmentent fortement, depannez d'abord par groupe : modele, token, compte amont, droits du forfait et ressources de plateforme.

En contexte entreprise ou equipe, regardez ensuite les lignes `Type=Token` de ce groupe. Si un seul utilisateur ou token est anormal, verifiez d'abord sa configuration cliente, son token, son nom de modele, son corps de requete et sa strategie de concurrence.

### 2. Lire les principales causes d'echec

Cause d'echec est generalement affichee par nombre d'occurrences. Traitez d'abord les erreurs les plus frequentes, puis les erreurs rares. Les erreurs frequentes representent le type d'incident dominant dans la periode.

| Type d'erreur | Mots-cles frequents | Attribution initiale | Verification prioritaire |
|---------------|---------------------|----------------------|--------------------------|
| Limite de frequence | `Account RPM limit exceeded`, `Max 10/min`, `Max 5/min` | Probleme d'utilisation ou limite amont | Trop de concurrence ou trop de requetes par minute |
| Limite journaliere | `Account daily limit exceeded` | Limite amont | Quota journalier amont epuise |
| Credentials en refroidissement | `All credentials ... are cooling down` | Limite amont | Tous les credentials amont du modele sont en refroidissement |
| Corps trop grand | `status_code=413`, `openai_error` | Probleme d'utilisation | Contexte, fichier, image ou resultat d'outil trop grand |
| Droits ou authentification | `401`, `403`, `Invalid API key`, `pending admin approval` | Probleme d'utilisation ou etat du compte | Token, forfait, groupe ou droits modele incorrects |
| Aucune ressource disponible | `No available accounts`, `No available channel`, `auth_unavailable` | Probleme plateforme ou configuration | Aucun compte, canal ou credential disponible dans ce groupe |
| Erreur amont | `502`, `all upstreams failed`, `Upstream request failed` | Probleme amont | Service amont ou reseau intermediaire anormal |
| Delai passerelle | `504`, `521`, `522`, `524` | Probleme amont ou de liaison | Connexion, lecture ou reponse amont trop lente |
| Protection ressources plateforme | `system disk overloaded`, `Service Unavailable` | Probleme plateforme | Noeud plateforme ou ressource amont temporairement indisponible |
| Format API image | `gpt-image-2`, `prompt is required`, `multipart form` | Probleme d'utilisation | Endpoint image, prompt ou format d'upload incorrect |
| Format d'appel outil | `tool_use`, `tool_result`, `Invalid schema` | Probleme d'utilisation | Messages outil client ou JSON Schema non conformes |

### 3. Traiter selon l'impact

| Symptome | Cause plus probable | Action conseillee |
|----------|---------------------|-------------------|
| Un seul token echoue | Configuration du token, droits ou format de requete local | Recopier le token, verifier la configuration cliente et le corps de requete |
| Un seul modele echoue | Droits modele, canal modele ou ressource modele amont | Changer vers un modele equivalent, confirmer que le forfait prend en charge ce modele |
| Un seul groupe a un faible taux de succes | Pool du groupe, droits du forfait ou compte amont | Changer de groupe/modele, fournir au support le groupe et la periode |
| Plusieurs groupes montrent `502`, `504`, `521`, `522`, `524` | Probleme amont ou de reseau | Reessayer plus tard, reduire les longues taches ; contacter le support si cela persiste |
| Plusieurs requetes montrent `413` | Corps de requete trop grand | Reduire le contexte, diviser les fichiers, compresser les images ou reduire les resultats d'outils |
| Plusieurs requetes montrent `429` | Frequence trop elevee, quota journalier epuise ou credentials en refroidissement | Reduire la concurrence ; distinguer RPM, daily limit et cooling down dans les logs |

### 4. Croiser cout et cache

| Symptome | Cause plus probable | Action conseillee |
|----------|---------------------|-------------------|
| Cout nettement plus haut que les autres tokens du meme groupe | Grand contexte, longue sortie, appels frequents ou taches repetees | Croiser Requetes, Temps moyen et journaux d'erreur pour localiser le service ou membre |
| Taux de cache eleve mais Part requetes cache faible | Peu de grosses requetes touchent le cache | Verifier si seules des taches fixes reutilisent le contexte |
| Part requetes cache elevee mais Tokens cache moyens faibles | Beaucoup de requetes touchent le cache, mais le gain unitaire est faible | Verifier si le contexte est trop court ou si le contenu cache est instable |
| Un token a un Temps moyen nettement plus haut | Taches client lourdes, long contexte, longue sortie ou amont lent | Comparer Requetes, cache, Cause d'echec et journaux unitaires de ce token |

## Informations utiles au support

Pour les problemes simples, consultez d'abord le [guide des journaux d'erreur](/fr/error-logs) et [l'etat de sante des groupes](/fr/group-health). Si le probleme persiste, ouvrez les details du journal d'erreur dans `console/log` et cliquez sur l'icone de copie pour copier les details de diagnostic en un clic. Quand vous contactez le support, fournissez ces informations en une fois a l'equipe technique pour limiter les allers-retours :

- ID utilisateur
- Periode : debut du probleme et derniere occurrence
- Groupe : `group`
- Modele : modele utilise par la requete
- Code de statut : par exemple `429`, `413`, `502`, `503`
- Contenu de l'erreur : `error_reasons.content`
- ID de requete : `request_id` dans le journal unitaire ou la reponse API
- Impact : un seul token, un seul modele, un seul groupe ou plusieurs groupes

::: tip Conclusion rapide
`401` / `403` concernent surtout les droits, `413` le corps de requete, `429` la frequence et le quota, `502` / `504` / `524` l'amont et les longues taches, `503` les ressources temporairement indisponibles.
:::
