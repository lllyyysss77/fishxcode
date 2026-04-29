# Foire aux questions

## Informations generales

### Qu'est-ce que FishXCode ?

FishXCode est un relais AI Coding qui prend en charge les modeles Claude et Codex sur plusieurs plateformes.

### Quels outils sont pris en charge ?

Les outils actuellement compatibles :

- **Claude Code** — CLI officiel d'Anthropic
- **OpenAI Codex** — Assistant de programmation d'OpenAI
- **RooCode** — Extension IA pour VS Code
- **Qwen Code** — Outil de programmation base sur Qwen d'Alibaba
- **Droid CLI** — CLI leger pour la programmation IA
- **OpenCode** — Outil terminal open source pour la programmation IA

### Qu'en est-il de la confidentialite de mes donnees ?

FishXCode fonctionne uniquement comme un relais d'API. Votre code et vos conversations ne sont pas stockes sur nos serveurs. Toutes les requetes sont transmises directement aux fournisseurs de modeles.

::: tip Conseil
Pour les projets sensibles, nous vous recommandons de consulter la politique de confidentialite de chaque fournisseur de modeles.
:::

## Compte et Token

### Comment creer un compte ?

Rendez-vous sur [fishxcode.com/register](https://fishxcode.com/register?aff=9CTW) et suivez les instructions pour vous inscrire.

### Comment obtenir un token API ?

Apres connexion, accedez a la [page de gestion des tokens](https://fishxcode.com/console/token) dans la console pour generer un nouveau token.

### Quelle est la duree de validite d'un token ?

Les tokens restent valides jusqu'a leur suppression ou regeneration manuelle. Nous recommandons de les renouveler regulierement pour des raisons de securite.

### Quelles sont les limites du quota d'utilisation ?

Chaque utilisateur beneficie d'un quota d'utilisation. Le montant exact varie en fonction des ressources disponibles sur la plateforme. Consultez votre console pour suivre votre consommation.

::: warning Attention
Une fois le quota epuise, les requetes seront rejetees. Prevoyez votre utilisation en consequence.
:::

## Configuration des outils

### Mes variables d'environnement ne fonctionnent pas

Verifiez les points suivants :

1. **Terminal non recharge** — Apres avoir modifie `.bashrc` / `.zshrc`, executez `source ~/.bashrc` ou ouvrez un nouveau terminal
2. **Erreur de saisie** — Les noms de variables sont sensibles a la casse : `ANTHROPIC_BASE_URL` n'est pas equivalent a `Anthropic_Base_Url`
3. **Espaces ou guillemets superflus** — Assurez-vous qu'il n'y a pas d'espaces parasites dans `export KEY="value"`

::: details Commandes de diagnostic rapide
```bash
# Verifier que les variables sont definies
echo $ANTHROPIC_BASE_URL
echo $ANTHROPIC_AUTH_TOKEN

# Si la sortie est vide, la variable n'est pas definie
```
:::

### Ou se trouvent les fichiers de configuration ?

| Outil | Emplacement |
|-------|-------------|
| Claude Code | Variables d'environnement |
| Codex | `~/.codex/config.toml` et `~/.codex/auth.json` |
| RooCode | Parametres JSON de VS Code |
| Qwen Code | Variables d'environnement |

### Impossible de se connecter a FishXCode

1. Verifiez que le `BASE_URL` est bien `https://fishxcode.com/` (attention au `/` final)
2. Assurez-vous que `fishxcode.com` est accessible depuis votre reseau
3. Si vous etes derriere un proxy d'entreprise, verifiez la configuration du proxy

## Choix du modele

### Comment choisir le bon modele ?

Selectionnez en fonction de votre besoin :

| Cas d'usage | Modele recommande | Raison |
|-------------|-------------------|--------|
| Programmation courante | `claude-sonnet-4-5-20250929` | Bon equilibre performance/rapidite |
| Completions rapides | `claude-3-5-haiku-20241022` | Temps de reponse court |
| Architecture complexe | `claude-sonnet-4-5-20250514` | Excellente capacite de raisonnement |

### Quelles sont les differences entre les modeles ?

- **Serie Sonnet** — Polyvalente, adaptee a la plupart des taches de programmation
- **Serie Haiku** — Legere et rapide, ideale pour les completions simples et le formatage
- Pour des comparaisons detaillees, consultez la documentation officielle de chaque fournisseur

### Comment changer de modele ?

Definissez la variable d'environnement `ANTHROPIC_MODEL` :

```bash
export ANTHROPIC_MODEL=claude-sonnet-4-5-20250929
```

## Depannage

### Erreur d'authentification (Auth Error)

::: warning Causes frequentes
- Le token contient des espaces supplementaires ou est incomplet
- Le token a ete supprime ou regenere
- `API_KEY` et `AUTH_TOKEN` doivent etre definis simultanement
:::

Solution : Accedez a la [console](https://fishxcode.com/console/token), verifiez l'etat de votre token et copiez-le a nouveau.

### Comment lire les journaux d'erreur ?

Accedez a [Console -> Journaux d'utilisation](https://fishxcode.com/console/log), choisissez le type **Journaux d'erreur**, puis filtrez par periode, modele, token, groupe, request ID, message d'erreur ou code de statut.

::: tip Conseils de diagnostic
- Copiez d'abord le `request_id` depuis la reponse API ou le journal, puis recherchez cette requete exacte
- Utilisez le filtre de message d'erreur pour rechercher des mots-cles dans `content`, par exemple `Invalid token` ou `Upstream request failed`
- Utilisez le code de statut pour regrouper rapidement les problemes, par exemple `401`, `429`, `502`, `503` ou `524`
:::

Messages d'erreur frequents :

| Contenu du journal | Signification | Action conseillee |
|--------------------|---------------|-------------------|
| `status_code=401, Invalid token` | Le token est invalide, mal copie ou expire | Recopiez le token depuis la console et supprimez les espaces en trop |
| `status_code=429, Account RPM limit exceeded` | Le compte amont a atteint sa limite de requetes par minute | Reduisez la concurrence et la frequence des essais, puis reessayez plus tard |
| `status_code=502, Upstream request failed` / `bad response status code 502` | Le service amont ou le reseau a renvoye une erreur | Reessayez plus tard ; si cela persiste, changez de modele ou contactez le support avec le `request_id` |
| `status_code=502, The origin web server returned an invalid or incomplete response to Cloudflare` | L'origine amont a renvoye une reponse invalide via Cloudflare | Generalement temporaire cote amont ; reessayez plus tard |
| `status_code=500, upstream error: do request failed` | L'envoi de la requete vers le service amont a echoue, souvent a cause du reseau ou d'une indisponibilite temporaire | Reessayez plus tard ; si cela persiste, transmettez le `request_id` au support |
| `status_code=520, bad response status code 520` | Cloudflare a renvoye une erreur inconnue, souvent liee a une reponse amont anormale ou a une connexion interrompue | Reessayez plus tard ; si cela se repete, traitez-le comme un incident amont |
| `status_code=524` / `bad response status code 524` | La reponse amont a depasse le delai Cloudflare de 120 secondes | Reduisez le contexte ou la longueur de sortie |
| `status_code=503, model gpt-image-2 is only supported on /v1/images/generations and /v1/images/edits` | Un modele image a ete appele sur le mauvais endpoint | Envoyez les requetes image vers l'endpoint images correspondant |
| `status_code=500, Image source is a local path that is not readable from this server` | La requete contient un chemin d'image local que l'upstream actuel ne peut pas lire, ce qui peut rendre la saisie dans le terminal inactive | Pour les projets frontend, verifiez d'abord les fichiers de dependances `lock` : supprimez les fichiers lock concernes, ou retirez les champs `png` anormaux qu'ils contiennent, puis rouvrez la session ; si vous devez toujours envoyer une image, utilisez une URL `http(s)` publique ou un payload base64 `data:image/...` |

### Delai d'attente depasse (Timeout)

Causes possibles :
1. Latence reseau elevee
2. Contexte d'entree trop volumineux necessitant un traitement plus long
3. Charge importante sur le service

Essayez de reduire le contexte d'entree ou reessayez apres quelques instants.

### Limite de debit atteinte (429)

Un code `429` signifie que vos requetes sont trop frequentes.

::: tip Comment reagir
- Ralentissez et attendez quelques secondes entre les requetes
- Evitez les appels API en boucle sans delai
- Verifiez qu'aucun autre processus n'utilise le meme token en parallele
:::

### Modele non disponible

Verifiez le nom exact du modele que vous avez specifie. Consultez la liste des modeles recommandes dans [Demarrage rapide](/fr/start). Certains modeles peuvent ne pas encore etre disponibles sur FishXCode.
