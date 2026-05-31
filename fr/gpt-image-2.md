# Guide de génération d'images GPT-Image-2

## Prérequis

Avant de commencer, préparez les éléments suivants :

1. Un compte FishXCode
2. Un token API utilisable
3. Vérifier que votre token actuel peut accéder à `gpt-image-2`

Obtenir votre token :
- Console : [https://fishxcode.com/console/token](https://fishxcode.com/console/token)

::: tip Conseil
Si votre console affiche des groupes de modèles ou des permissions, vérifiez que votre token couvre bien `gpt-image-2`. En cas de doute, créez un nouveau token par défaut pour tester.
:::

## Outil visuel open source

Si vous ne voulez pas commencer par écrire du code, vous pouvez utiliser l'outil GPT-Image-2 open source de FishXCode pour tester vos prompts et générer des images directement :

- Démo en ligne : [https://fishxcode-gpt-image-2.lovable.app/](https://fishxcode-gpt-image-2.lovable.app/)
- Dépôt GitHub : [https://github.com/fishxcode/gpt-image-2](https://github.com/fishxcode/gpt-image-2)

::: tip Conseil
Utilisez d'abord l'outil pour vérifier votre token, votre prompt et vos paramètres d'image, puis réutilisez les mêmes paramètres dans votre code ou votre workflow.
:::

## Méthode 1 : Générer des images via l'Images API

C'est l'approche la plus directe, basée sur l'endpoint de génération d'images compatible OpenAI.

- Endpoint : `https://api.fishxcode.com/v1/images/generations`
- Modèle : `gpt-image-2`

### Exemple Python

```python
import base64
from openai import OpenAI

client = OpenAI(
    api_key="sk-your-fishxcode-token",
    base_url="https://api.fishxcode.com/v1"
)

result = client.images.generate(
    model="gpt-image-2",
    prompt="Un chat roux portant un casque d'astronaute, assis sur la lune, éclairage cinématographique, ultra détaillé",
    size="1024x1024"
)

image_base64 = result.data[0].b64_json

with open("gpt-image-2-output.png", "wb") as f:
    f.write(base64.b64decode(image_base64))
```

### Exemple Node.js

```javascript
import fs from "node:fs";
import OpenAI from "openai";

const client = new OpenAI({
  apiKey: "sk-your-fishxcode-token",
  baseURL: "https://api.fishxcode.com/v1",
});

const result = await client.images.generate({
  model: "gpt-image-2",
  prompt: "Une ville futuriste flottant au-dessus des nuages, style néon cyberpunk, ultra détaillée",
  size: "1024x1024",
});

const imageBase64 = result.data[0].b64_json;
fs.writeFileSync("gpt-image-2-output.png", Buffer.from(imageBase64, "base64"));
```

### Exemple curl

```bash
curl --request POST \
  --url https://api.fishxcode.com/v1/images/generations \
  --header "Authorization: Bearer sk-your-fishxcode-token" \
  --header "Content-Type: application/json" \
  --data '{
    "model": "gpt-image-2",
    "prompt": "Une affiche produit minimaliste sur fond blanc, avec un clavier en verre transparent flottant au centre, style photo publicitaire",
    "size": "1024x1024"
  }'
```

::: warning Attention
Le résultat principal retourné par l'Images API est souvent `b64_json`. Vous devez donc décoder cette chaîne Base64 pour obtenir un vrai fichier image.
:::

## Méthode 2 : Générer des images via Chat Completions

Si votre workflow repose déjà sur `/v1/chat/completions`, vous pouvez aussi appeler `gpt-image-2` par ce biais.

- Endpoint : `https://api.fishxcode.com/v1/chat/completions`
- Modèle : `gpt-image-2`

### Exemple Python

```python
from openai import OpenAI

client = OpenAI(
    api_key="sk-your-fishxcode-token",
    base_url="https://api.fishxcode.com/v1"
)

response = client.chat.completions.create(
    model="gpt-image-2",
    messages=[
        {
            "role": "user",
            "content": "Dessine un Shiba Inu portant des lunettes de soleil, assis dans une décapotable rouge vintage, style road trip"
        }
    ]
)

print(response)
```

### Exemple curl

```bash
curl --request POST \
  --url https://api.fishxcode.com/v1/chat/completions \
  --header "Authorization: Bearer sk-your-fishxcode-token" \
  --header "Content-Type: application/json" \
  --data '{
    "model": "gpt-image-2",
    "messages": [
      {
        "role": "user",
        "content": "Dessine un café japonais au coucher du soleil avec une composition cinématographique chaleureuse"
      }
    ]
  }'
```

::: tip Conseil
Si votre objectif est simplement d'obtenir une image de manière fiable, privilégiez `/v1/images/generations`. Utilisez `/v1/chat/completions` seulement si vous devez intégrer la génération d'images dans un workflow conversationnel existant.
:::

## Paramètres courants

Les paramètres couramment utilisés avec `gpt-image-2` sont :

| Paramètre | Signification | Exemple |
| --- | --- | --- |
| `model` | ID du modèle | `gpt-image-2` |
| `prompt` | Prompt d'image | `un hibou mécanique` |
| `size` | Taille de sortie | `1024x1024` |
| `n` | Nombre d'images | `1` |
| `background` | Mode d'arrière-plan | `transparent` / `white` |
| `quality` | Qualité de sortie | `high` / `medium` |

::: warning Attention
La compatibilité des paramètres peut varier selon les SDK et les couches proxy. En cas d'erreur, revenez d'abord à l'ensemble minimal : `model + prompt + size`.
:::

## Utiliser GPT-Image-2 dans Cherry Studio

Si vous préférez générer des images directement dans Cherry Studio, configurez-le ainsi :

### 1. Ajouter un provider compatible OpenAI

Créez un provider personnalisé dans Cherry Studio avec :

- API Key : votre token FishXCode
- Base URL : `https://api.fishxcode.com/v1`

### 2. Ajouter le modèle

Utilisez ce nom de modèle :

```text
gpt-image-2
```

Si Cherry Studio permet de définir le type de modèle, choisissez un type de génération d'images ou un type compatible OpenAI Images.

### 3. Commencer à générer

Créez une nouvelle session ou ouvrez le panneau de génération d'images, sélectionnez `gpt-image-2`, puis saisissez votre prompt.

::: tip Conseil
Si Cherry Studio n'affiche pas les images, vérifiez d'abord :
- que la Base URL est bien `https://api.fishxcode.com/v1`
- que votre version de Cherry Studio supporte correctement l'API OpenAI Images
:::

## FAQ

### Pourquoi la requête réussit-elle sans créer de fichier image ?

Parce que de nombreuses réponses renvoient `b64_json` au lieu d'un fichier déjà enregistré. Vous devez décoder cette chaîne Base64 et l'écrire vous-même dans un fichier `.png`.

### Quel endpoint faut-il privilégier ?

- Génération d'image uniquement : préférez `POST /v1/images/generations`
- Génération d'image intégrée à un workflow de chat : envisagez `POST /v1/chat/completions`

### Que faire si le modèle n'existe pas ou si je n'ai pas la permission ?

Vérifiez dans cet ordre :

1. Assurez-vous que le nom du modèle est `gpt-image-2`
2. Assurez-vous que le token provient de la [console FishXCode](https://fishxcode.com/console/token)
3. Assurez-vous que votre token a accès à `gpt-image-2`
4. Assurez-vous que la Base URL est `https://api.fishxcode.com/v1`

### Pourquoi certains paramètres supplémentaires échouent-ils ?

Parce que la compatibilité varie selon les clients, les versions de SDK et les couches proxy. Commencez par le minimum viable, puis ajoutez progressivement `quality`, `background`, `n` et les autres paramètres optionnels.
