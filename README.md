# Guide de traduction MagicalSky

Ce guide explique comment modifier ou ajouter des traductions dans les fichiers de langue.

## Structure du dépôt

```
notif/
├── fr_FR/
│   ├── bossbar.yml
│   ├── error.yml
│   ├── event.yml
│   ├── gift.yml
│   ├── info.yml
│   ├── logger.yml
│   ├── message.yml
│   ├── todo.yml
│   └── validation.yml
└── en_US/
    └── ...  (même arborescence de fichiers)
```

- Chaque **langue** a son propre dossier (`fr_FR/`, `en_US/`, ...).
- Chaque dossier contient **un fichier par catégorie** (`template`) : `error`, `validation`, `info`, `gift`, `warning`, `message`, `logger`, `todo`, `bossbar`...
- Un fichier ne contient **aucune clé englobante** : `error.yml` commence directement par ses sous-clés (ex: `command:`), pas par `error:` ni `magicalsky:`. Le préfixe complet est réinjecté par le loader du jeu à partir du nom du fichier et du dossier de langue.

## Structure à l'intérieur d'un fichier

```yaml
<chemin>: "Message"
```

- **`<chemin>`** : l'identifiant unique du message. **Ne jamais renommer ou déplacer ces clés** — seule la valeur (le texte) doit être modifiée.

## Ce qu'il ne faut jamais modifier

### Les fichiers et dossiers
- Ne renomme pas un dossier de langue (`fr_FR`, `en_US`, ...).
- Ne renomme pas un fichier de catégorie (`error.yml`, `message.yml`, ...) et n'y déplace pas une clé venant d'un autre fichier sans raison : ça changerait sa présentation (couleur, icône, son).

### Les clés
Seule la **valeur** (le texte après `:`) doit être traduite/modifiée. Ne touche jamais :
- au nom des clés (`kicked`, `broadcast`, `island`, etc.)
- à leur emplacement dans l'arborescence
- à l'indentation YAML

### Les placeholders `{...}`
Les mots entre accolades (`{player}`, `{time}`, `{amount}`, etc.) sont injectés automatiquement par le jeu au moment de l'affichage. Ils doivent être **conservés tels quels** dans ta traduction, mais tu peux les déplacer dans la phrase pour respecter la grammaire de la langue cible.

```yaml
# Français
kicked: <color:#ffc933>{player}</color> vient de t'expulser de l'île
```
```yaml
# Anglais — {player} déplacé, mais toujours présent
kicked: You were kicked from the island by <color:#ffc933>{player}</color>
```

Ne jamais renommer un placeholder (`{player}` → `{joueur}`), il ne serait plus reconnu par le jeu et s'afficherait tel quel.

### Les balises de mise en forme
Le texte contient des balises qui gèrent les couleurs, sons, liens cliquables, etc. Elles doivent être conservées et correctement fermées.

Balises courantes que tu retrouveras :
- `<color:#xxxxxx>...</color>` ou `<white>`, `<red>`, `<green>`, etc. : couleur du texte
- `<bold>...</bold>` : texte en gras
- `<glyph:nom_icone>` : icône du jeu (autonome, pas de balise fermante)
- `<sound:NOM_DU_SON[...]>` : joue un son (autonome, pas de balise fermante), certains sons sont customs, si vous avez une idée de son, n'hésitez pas à créer un ticket pour qu'on l'ajoute
- `<click:run_command:'/commande'>...</click>` : rend le texte cliquable pour exécuter une commande
- `<click:open_url:'https://...'>...</click>` : rend le texte cliquable pour ouvrir un lien
- `<hover:show_text:'...'>...</hover>` : affiche un texte au survol de la souris
- `<newline>` : retour à la ligne (autonome, pas de balise fermante)

Tu peux traduire le texte **à l'intérieur** de ces balises librement, mais :
- garde la balise et sa fermante intactes (`<color:#ffc933>texte</color>` → `<color:#ffc933>text</color>`)

```yaml
# Ne change QUE le texte visible, pas le reste
sorcier_required: >-
  Tu dois avoir le grade <glyph:sorcier> pour utiliser cet île.
  <click:run_command:'/boutique'>Clique ici pour accéder à
  [<color:#ffc933>notre boutique</color>]</click> et l'obtenir
```

## Comportement si une clé n'existe pas

Si une traduction est manquante dans un fichier de langue, le **chemin brut** de la clé s'affiche dans le chat (ex: `island.kick.kicked`) à la place du message. Si tu vois ça en jeu, c'est qu'il manque une traduction — ouvre une PR pour la compléter.

## Support multi-langue

Chaque langue a son propre dossier avec **exactement les mêmes fichiers et la même arborescence de clés** à l'intérieur de chacun.

## Exemple

`notif/fr_FR/message.yml` :
```yaml
island:
  kick:
    broadcast: <color:#ffc933>{player}</color> vient d'expulser <color:#ffc933>{target}</color> de l'île
    kicked: <color:#ffc933>{player}</color> vient de t'expulser de l'île
```

`notif/fr_FR/error.yml` :
```yaml
command:
  cooldown: Tu dois attendre encore {time} avant de pouvoir faire à nouveau cette commande
```

Versions anglaises correspondantes (mêmes chemins de fichiers et de clés, texte traduit) :

`notif/en_US/message.yml` :
```yaml
island:
  kick:
    broadcast: <color:#ffc933>{player}</color> just kicked <color:#ffc933>{target}</color> from the island
    kicked: You were kicked from the island by <color:#ffc933>{player}</color>
```

`notif/en_US/error.yml` :
```yaml
command:
  cooldown: You must wait {time} before using this command again
```