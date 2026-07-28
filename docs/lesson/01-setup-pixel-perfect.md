# Régler Godot pour un pixel art bien net

> Niveau : débutant. Durée : 5 min. Godot : 4.x

Avant même de faire bouger Mina, il faut dire à Godot que le jeu est en pixel art, pour qu'il ne floute pas les pixels. Sans ça, le rendu devient baveux dès qu'on agrandit la fenêtre. Cette leçon règle ce point une bonne fois pour toutes.

---

## Le but

Un rendu pixel-perfect : chaque pixel du dessin reste un carré net, même en plein écran.

---

## 1. Régler la résolution de base

Aller dans Projet > Paramètres du projet > Affichage > Fenêtre.

On travaille sur une petite résolution de rendu, puis on l'agrandit proprement :

| Réglage | Valeur | Pourquoi |
|---|---|---|
| Viewport Width | `320` | La vraie largeur en pixels du jeu |
| Viewport Height | `180` | La vraie hauteur (ratio 16:9) |
| Window Width Override | `1280` | Taille de la fenêtre à l'écran (320 x 4) |
| Window Height Override | `720` | (180 x 4) |

On dessine en 320x180, mais on affiche en 1280x720. Chaque pixel du jeu devient un carré de 4x4 pixels bien propre.

---

## 2. Le stretch (l'étirement)

Toujours dans Affichage > Fenêtre, section Stretch :

- Mode : `canvas_items`
- Scale Mode : `integer`

Le `integer` est la clé du pixel-perfect : il interdit à Godot d'agrandir les pixels en 3,5x ou 2,7x (ce qui les déforme). Il n'autorise que x2, x3, x4, donc toujours net.

---

## 3. Couper le filtrage des textures

Aller dans Rendu > Textures :

- Default Texture Filter : `Nearest` (valeur `0`)

Par défaut Godot lisse les textures (pratique en 3D, mauvais pour du pixel art). En `Nearest`, il garde les bords bien carrés.

---

## Vérifier dans le fichier project.godot

En ouvrant `project.godot`, on doit retrouver ceci (la config exacte de Mina) :

```ini
[display]

window/size/viewport_width=320
window/size/viewport_height=180
window/size/window_width_override=1280
window/size/window_height_override=720
window/stretch/mode="canvas_items"
window/stretch/scale_mode="integer"

[rendering]

textures/canvas_textures/default_texture_filter=0
```

---

## Résultat

Lancer le jeu (F5) et redimensionner la fenêtre : les pixels restent nets. On peut passer au déplacement de Mina.

Leçon suivante : [Faire marcher Mina dans 4 directions](./02-mouvement-topdown-mina.md)
