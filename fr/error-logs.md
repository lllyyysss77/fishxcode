---
title: Guide des journaux d'erreur
description: Champs des journaux d'erreur FishXCode, codes de statut, erreurs frequentes et procedure de diagnostic.
---

# Guide des journaux d'erreur

Les journaux d'erreur servent a identifier la cause precise d'un echec de requete. Pour depanner, consultez d'abord [l'etat de sante des groupes](/fr/group-health) afin d'evaluer l'impact, puis utilisez le `request_id` dans les journaux d'erreur pour retrouver la requete exacte.

Entree : [Console -> Journaux d'utilisation](https://fishxcode.com/console/log). Passez le type de journal sur **Journaux d'erreur**, puis filtrez par periode, modele, token, groupe, ID de requete, message d'erreur ou code de statut.

::: tip Lecture rapide
- `request_id` sert a retrouver une requete unique
- `status_code` sert a regrouper les erreurs par type
- `content` sert a rechercher des mots-cles d'erreur
- `group` sert a voir si le probleme se concentre sur un groupe
:::

## Champs

| Champ | Signification | Valeur pour le diagnostic |
|-------|---------------|---------------------------|
| Temps | Heure a laquelle la requete a echoue | Voir si le probleme persiste |
| Modele | Nom du modele utilise par la requete | Voir si un seul modele est touche |
| Token | Nom de l'API Key ou du token utilise | Voir si un seul token est touche |
| Groupe | Groupe de forfait, de modele ou de ressource amont | Voir si l'anomalie se concentre sur un groupe |
| ID de requete | Identifiant unique d'une requete, souvent `request_id` | A fournir en priorite au support |
| Message d'erreur | Texte de l'echec, souvent dans `content` | Chercher l'erreur exacte par mot-cle |
| Code de statut | Code HTTP ou amont, par exemple `401`, `429`, `502` | Classer rapidement le type d'erreur |

## Ordre de diagnostic

1. Copiez le `request_id` depuis la reponse API ou le journal, puis recherchez cette requete exacte.
2. Sans `request_id`, reduisez le perimetre avec la periode, le modele et le token.
3. Regroupez par code de statut, par exemple `401`, `413`, `429`, `502`, `503`.
4. Recherchez des mots-cles dans le message d'erreur, par exemple `Invalid API key`, `daily limit exceeded`, `Upstream request failed`.
5. Si la meme erreur se repete sur un groupe, consultez [l'etat de sante des groupes](/fr/group-health) pour confirmer l'impact.

## Attribution du probleme

Les journaux d'erreur aident a distinguer un probleme d'utilisation, un probleme amont ou un probleme de plateforme. Ne vous basez pas seulement sur le code de statut ; regardez aussi l'impact.

| Attribution | Critere | Signes typiques | Priorite |
|-------------|---------|-----------------|----------|
| Probleme d'utilisation | Un seul appel, un seul token ou une configuration cliente specifique | Corps de requete invalide, parametre incorrect, token mal copie, contexte trop grand, mauvais endpoint | Verifier la configuration locale, le corps de requete, le modele, le token et la version de l'outil |
| Probleme amont | Plusieurs requetes montrent des echecs amont, des delais ou des limites amont | `Upstream request failed`, `all upstreams failed`, `bad response status code 502/524`, limitation du compte amont | Reessayer plus tard, reduire la concurrence, changer de modele ou de groupe si necessaire |
| Probleme de plateforme | Noeud, pool de ressources ou canal indisponible cote plateforme | `system disk overloaded`, `No available accounts`, `No available channel`, `Service Unavailable` cote plateforme | Reessayer plus tard ; si cela persiste, contacter le support avec le groupe, la periode et le `request_id` |
| A confirmer par groupe | Une seule ligne ne suffit pas pour attribuer la cause | `429`, `502`, `503`, qui peuvent avoir plusieurs causes | Consulter [l'etat de sante des groupes](/fr/group-health) pour voir si l'erreur est concentree |

::: warning Attention
Ne concluez pas a partir d'un seul `502` ou `503`. Une requete isolee peut echouer a cause d'une variation temporaire amont ; une forte concentration d'echecs dans le meme groupe et la meme periode indique mieux un probleme amont ou de ressources.
:::

## Codes de statut

| Code | Signification frequente | Attribution initiale | Priorite |
|------|-------------------------|----------------------|----------|
| `400` | Corps de requete, parametre ou message d'outil invalide | Probleme d'utilisation | Verifier le corps de requete, l'endpoint et le schema d'outil |
| `401` | Token invalide, desactive ou authentification echouee | Probleme d'utilisation | Recopier le token et verifier son etat |
| `403` | Acces refuse par l'amont, souvent lie aux droits ou au modele | Probleme d'utilisation ou amont | Verifier forfait, groupe et droits du modele |
| `413` | Corps de requete trop grand | Probleme d'utilisation | Reduire le contexte, diviser les fichiers, compresser les images |
| `429` | Limite de frequence, limite journaliere ou credentials en refroidissement | A confirmer par groupe | Reduire la concurrence, attendre le quota ou la fin du refroidissement |
| `500` | Erreur interne du relais ou erreur amont encapsulee | A confirmer par le message | Lire le message complet pour identifier la cause reelle |
| `502` | Service amont ou reseau intermediaire anormal | Probleme amont ou de liaison | Reessayer plus tard, changer de modele/groupe si necessaire |
| `503` | Service, canal, compte ou ressource de plateforme temporairement indisponible | Probleme amont ou plateforme | Reessayer plus tard ; si cela persiste, contacter le support |
| `504` / `521` / `522` / `524` | Delai de connexion, lecture ou reponse au niveau passerelle | Probleme amont ou de liaison | Reduire les longues taches, reessayer plus tard |

## Messages d'erreur frequents

| Contenu du journal | Signification | Attribution initiale | Action conseillee |
|--------------------|---------------|----------------------|-------------------|
| `status_code=401, Invalid token` | Token invalide, mal copie ou expire | Probleme d'utilisation | Recopier le token depuis la console et supprimer les espaces en trop |
| `status_code=401, Invalid API key or key is pending admin approval` | API Key invalide, ou nouvelle key en attente d'approbation/activation | Probleme d'utilisation ou etat du compte | Utiliser le dernier token de la console ; si le token vient d'etre cree ou le forfait change, attendre l'activation puis reessayer |
| `status_code=403, bad response status code 403` | L'amont refuse la requete, souvent a cause des droits, de l'etat du compte ou de l'acces modele | Probleme d'utilisation ou amont | Verifier token, groupe de forfait et droits du modele ; si cela persiste, changer de modele ou contacter le support avec le `request_id` |
| `status_code=413, openai_error` / `bad response status code 413` | Corps de requete trop grand, souvent contexte, fichier, image ou resultat d'outil | Probleme d'utilisation | Reduire le contexte, limiter le contenu envoye, diviser les gros fichiers ou compresser les images |
| `status_code=429, Account RPM limit exceeded` | Le compte amont a atteint sa limite de requetes par minute | Probleme d'utilisation ou limite amont | Reduire concurrence et frequence des essais, puis reessayer plus tard |
| `status_code=429, Account daily limit exceeded` | Le compte amont a atteint sa limite journaliere | Limite amont | Attendre la remise a zero journaliere ou changer de modele/groupe disponible |
| `status_code=429, All credentials for model ... are cooling down` | Tous les credentials amont du modele sont en refroidissement | Limite amont | Reduire les relances, attendre la fin du refroidissement ; pour une tache urgente, changer temporairement de modele |
| `status_code=500, requete echouee [429]: {"message":"Too many requests, please wait before trying again."}` | L'amont renvoie une limite `429`, encapsulee en `500` par le relais | Probleme d'utilisation ou limite amont | Traiter comme une limite : reduire la concurrence, allonger l'intervalle de retry, eviter les relances immediates |
| `status_code=500, auth_unavailable: no auth available` | Aucun credential amont disponible pour ce modele ou ce groupe | Probleme de plateforme | Reessayer plus tard ; si cela persiste, changer de modele/groupe ou contacter le support |
| `status_code=502, Upstream request failed` / `bad response status code 502` / `all upstreams failed` | Le service amont ou le reseau intermediaire a echoue, ou tous les amonts disponibles ont echoue | Probleme amont | Reessayer plus tard ; si cela persiste, changer de modele ou contacter le support avec le `request_id` |
| `status_code=502, openai_error` | L'amont compatible OpenAI a renvoye une erreur sans cause plus precise | Probleme amont | Traiter d'abord comme une erreur amont ; si la requete se reproduit, reduire le contexte et fournir le `request_id` au support |
| `status_code=502, The origin web server returned an invalid or incomplete response to Cloudflare` | L'origine amont a renvoye une reponse invalide via Cloudflare | Probleme amont | Generalement temporaire cote amont, reessayer plus tard |
| `status_code=500, upstream error: do request failed` | L'envoi vers l'amont a echoue, souvent par reseau ou indisponibilite temporaire | Probleme amont ou de liaison | Reessayer plus tard ; si cela persiste, fournir le `request_id` au support |
| `status_code=520, bad response status code 520` | Cloudflare renvoie une erreur inconnue, souvent liee a une reponse amont anormale ou a une connexion interrompue | Probleme amont ou de liaison | Reessayer plus tard ; si cela se concentre, traiter comme incident amont |
| `status_code=521` / `522` / `504` / `524` | Delai ou erreur de connexion/lecture/reponse via Cloudflare ou passerelle amont | Probleme amont ou de liaison | Reessayer plus tard ; si cela n'arrive que sur les longues taches, reduire contexte, sortie ou chaine d'outils |
| `status_code=503, Service Unavailable` | Service amont indisponible ou groupe sans ressource disponible | A confirmer par groupe | Reessayer plus tard ; si cela persiste, changer de modele/groupe ou contacter le support |
| `status_code=503, system disk overloaded` | Le disque du noeud de service est trop charge ; la plateforme refuse temporairement pour proteger le service | Probleme de plateforme | Probleme de capacite/noeud cote plateforme, reessayer plus tard ; si cela persiste, contacter le support |
| `status_code=503, No available accounts: no available accounts` | Aucun compte amont disponible dans le groupe courant | Probleme de plateforme | Changer de modele/groupe ou reessayer plus tard ; si cela dure, contacter le support |
| `status_code=503, No available channel for model ... under group ...` | Aucun canal disponible pour ce modele dans ce groupe | Probleme d'utilisation ou plateforme | Verifier le nom du modele et la prise en charge du forfait, puis choisir un modele supporte par ce groupe |
| `status_code=503, model gpt-image-2 is only supported on /v1/images/generations and /v1/images/edits` | Le modele image est appele sur le mauvais endpoint | Probleme d'utilisation | Envoyer les requetes image vers l'endpoint images correspondant |
| `status_code=500, Image source is a local path that is not readable from this server` | La requete contient un chemin local que l'amont ne peut pas lire, ce qui peut bloquer la saisie terminal | Probleme d'utilisation | Pour un projet frontend, verifier les fichiers `lock` : supprimer le lock concerne ou retirer les champs `png` anormaux, puis rouvrir la session ; sinon utiliser une URL `http(s)` publique ou un `data:image/...` base64 |
| `status_code=500, failed to parse multipart form` | Le corps d'upload image/fichier n'est pas conforme | Probleme d'utilisation | Verifier les champs `multipart/form-data`, le champ fichier et les en-tetes ; ne pas ecrire un mauvais boundary a la main |
| `status_code=400, Invalid request: prompt is required` | La requete image generation/edition n'a pas de `prompt` | Probleme d'utilisation | Ajouter un `prompt` non vide et confirmer que la requete va vers l'endpoint image |
| `status_code=400, Unsupported parameter: messages` | L'endpoint et les parametres ne correspondent pas, souvent des parametres Chat/Responses envoyes a un endpoint qui ne prend pas `messages` | Probleme d'utilisation | Verifier `base_url`, endpoint et type de modele de l'outil, puis adapter le corps de requete |
| `tool_use ids were found without tool_result blocks immediately after` | La sequence des messages d'outil ne respecte pas le protocole Claude | Probleme d'utilisation | Chaque `tool_use` doit etre suivi du `tool_result` correspondant ; si l'outil genere cela, mettre a jour le client ou rouvrir la session |
| `Invalid schema for function ... None is not of type 'array'` | Le schema de fonction d'outil est invalide, souvent `parameters` ou un champ tableau vide/mal type | Probleme d'utilisation | Verifier les definitions MCP/outils, fournir `[]` pour les tableaux et respecter JSON Schema |
| `status_code=500, not implemented` | L'endpoint, la capacite du modele ou le chemin d'appel d'outil n'est pas encore implemente | Probleme d'utilisation ou plateforme | Confirmer que l'endpoint et le modele sont supportes ; changer de modele ou contacter le support si necessaire |

## Traitement par type

### Authentification et droits

Surveillez `401`, `403`, `Invalid API key`, `pending admin approval`, `No available channel`. Confirmez d'abord que le token vient de la console, qu'il est copie completement et qu'il a les droits pour le modele et le groupe.

### Format de requete

Surveillez `400`, `413`, `Unsupported parameter`, `prompt is required`, `Invalid schema`. Verifiez d'abord l'endpoint, les champs du corps, le schema d'outil, les parametres image et la taille du contexte.

### Limites et quota

Surveillez `429`, `Max 10/min`, `daily limit exceeded`, `cooling down`. Ne relancez pas sans delai ; reduisez la concurrence selon la limite indiquee, ou attendez le quota/credential.

### Amont et ressources plateforme

Surveillez `502`, `503`, `504`, `520`, `521`, `522`, `524`, `Service Unavailable`, `system disk overloaded`. Reessayez d'abord plus tard ; si cela se concentre sur le meme groupe, consultez [l'etat de sante des groupes](/fr/group-health).

## Informations utiles au support

Dans la liste `console/log`, vous pouvez copier en un clic les details de diagnostic depuis le message d'erreur. Quand vous contactez le support, fournissez ces informations en une fois a l'equipe technique pour limiter les allers-retours :

- ID utilisateur
- Periode : debut du probleme et derniere occurrence
- Groupe : `group`
- Modele : modele utilise par la requete
- Code de statut : par exemple `429`, `413`, `502`, `503`
- Contenu de l'erreur : `error_reasons.content`
- ID de requete : `request_id` dans le journal unitaire ou la reponse API
- Impact : un seul token, un seul modele, un seul groupe ou plusieurs groupes

::: tip Conclusion
Les journaux d'erreur expliquent pourquoi une requete precise a echoue ; l'etat de sante des groupes indique si le probleme est concentre. Utilisez les deux pour depanner plus vite.
:::
