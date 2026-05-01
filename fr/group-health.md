---
title: Etat de sante des groupes
description: Etat de sante des groupes FishXCode, champs, categories d'erreurs et procedure de diagnostic.
---

# Etat de sante des groupes

L'etat de sante des groupes sert a distinguer une erreur isolee d'un probleme concentre sur un forfait, un modele ou un groupe amont. Quand une API echoue, consultez d'abord l'etat de sante des groupes, puis ouvrez le journal d'utilisation de la requete pour retrouver le `request_id`.

::: info Perimetre des donnees
La page publique `status` integree ici interroge l'etat de sante des groupes utilises par tous les utilisateurs FishXCode sur la periode selectionnee. Elle reflete la disponibilite globale des groupes de la plateforme, avec un suivi en temps reel, impartial et stable.

La vue **Journaux d'utilisation -> Etat de sante des groupes** dans la console ne compte que les requetes de l'utilisateur courant. Elle sert a diagnostiquer ses propres tokens, modeles et requetes.
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

Voici une capture d'exemple de **Journaux d'utilisation -> Etat de sante des groupes**, pour la periode du 6 avril 2026 au 1er mai 2026. Elle montre le taux de succes, le nombre de requetes, le temps moyen, la derniere requete et les causes d'echec du groupe global.

![Exemple d'etat de sante des groupes dans la console](/img/group-health.png)

::: tip Principe
Evaluez d'abord l'impact, puis traitez l'erreur unitaire. Un journal unitaire sert a diagnostiquer une requete precise ; l'etat de sante des groupes sert a voir si le probleme est concentre.
:::

Pour expliquer un message d'erreur unitaire, consultez le [guide des journaux d'erreur](/fr/error-logs).

## Champs

| Champ | Signification | Valeur pour le diagnostic |
|-------|---------------|---------------------------|
| `group` | Groupe de la requete, par exemple forfait, groupe par defaut ou groupe de modele | Voir si le probleme se concentre sur un forfait, un modele ou un pool amont |
| `total_count` | Nombre total de requetes dans la periode | Evaluer si l'echantillon est suffisant |
| `success_count` | Nombre de requetes reussies | Comparer avec `total_count` pour mesurer la disponibilite |
| `error_count` | Nombre de requetes echouees | Si le nombre monte, regarder d'abord `error_reasons` |
| `success_rate` | Taux de succes | Un taux nettement bas indique souvent un probleme concentre |
| `avg_use_time` | Temps moyen, en secondes | Si le temps monte, verifier longs contextes, longues sorties, outils et lenteur amont |
| `quota` | Quota du groupe ou valeur de quota statistique | Croiser avec le forfait et le solde console |
| `tokens` | Consommation de tokens dans la periode | Detecter une consommation anormale ou des requetes a gros contexte |
| `first_seen_at` | Premiere apparition dans la periode statistique | Situer le debut du probleme |
| `last_seen_at` | Derniere apparition dans la periode statistique | Voir si le probleme continue |
| `error_reasons` | Causes d'erreur frequentes et nombre d'occurrences | Traiter d'abord l'erreur la plus frequente, pas seulement la derniere ligne |

## Procedure de diagnostic

### 1. Regarder le taux de succes et le nombre d'erreurs

Si `success_rate` reste proche du niveau habituel et que `error_count` est faible, il s'agit souvent d'une erreur occasionnelle. Copiez le `request_id` de la requete et poursuivez dans le journal unitaire.

Si le `success_rate` d'un groupe est clairement plus bas que les autres, ou si `error_count` augmente fortement, depannez d'abord par groupe : modele, token, compte amont, droits du forfait et ressources de plateforme.

### 2. Lire les principales causes d'erreur

`error_reasons` est generalement affiche par nombre d'occurrences. Traitez d'abord les erreurs les plus frequentes, puis les erreurs rares. Les erreurs frequentes representent le type d'incident dominant dans la periode.

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
