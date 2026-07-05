# Guide de traduction MagicalSky

Ce guide explique comment modifier ou ajouter des traductions dans les fichiers de langue.

## Structure du fichier

```yaml
magicalsky:
  <serveur>:
    <template>:
      <chemin>: "Message"
```

- **`magicalsky`** : clé racine, fixe, ne jamais y toucher.
- **`<serveur>`** : `default` pour les notifications communes à tous les serveurs, ou le nom d'un serveur spécifique (ex: `Event`) pour une notif qui ne concerne que celui-ci.
- **`<template>`** : catégorie du message (`error`, `validation`, `info`, `gift`, `warning`, `message`, `logger`, `todo`, `bossbar`...). Ne pas déplacer une clé d'un template à un autre sans raison : ça changerait sa présentation (couleur, icône, son).
- **`<chemin>`** : l'identifiant unique du message. **Ne jamais renommer ou déplacer ces clés** — seule la valeur (le texte) doit être modifiée.

## Ce qu'il ne faut jamais modifier

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

Chaque langue a son propre fichier avec **exactement la même arborescence de clés** :

```
notif/
├── fr_FR.yml
├── en_US.yml
└── es_ES.yml
```

## Exemple

```yaml
magicalsky:
  default:
    message:
      island:
        kick:
          broadcast: <color:#ffc933>{player}</color> vient d'expulser <color:#ffc933>{target}</color> de l'île
          kicked: <color:#ffc933>{player}</color> vient de t'expulser de l'île
    error:
      command:
        cooldown: Tu dois attendre encore {time} avant de pouvoir faire à nouveau cette commande
```

Version anglaise correspondante (même clés, texte traduit) :

```yaml
magicalsky:
  default:
    message:
      island:
        kick:
          broadcast: <color:#ffc933>{player}</color> just kicked <color:#ffc933>{target}</color> from the island
          kicked: You were kicked from the island by <color:#ffc933>{player}</color>
    error:
      command:
        cooldown: You must wait {time} before using this command again
```