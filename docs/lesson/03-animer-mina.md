# Animer Mina et ses 3 formes (AnimatedSprite2D et strips)

> Niveau : débutant+. Durée : 20 min. Godot : 4.x

Mina bouge, mais elle glisse comme un fantôme. Objectif : lui donner des animations de marche et d'attente dans les 4 directions, et préparer ses trois formes (normale, bâton, mage), la base d'un futur système de classes.

---

## Comprendre les strips

Les sprites de Mina sont des strips : une seule image qui contient plusieurs poses côte à côte. Le nom indique le nombre de cases :

- `mina_walk_down_strip4.png` : 4 images (une animation de marche fluide).
- `mina_baton_idle_right_strip2.png` : 2 images.
- Un fichier sans `stripN` : une seule pose (image fixe).

Godot découpe ces bandes automatiquement. Il faut juste lui indiquer le nombre de cases.

---

## 1. Créer les animations dans AnimatedSprite2D

Sélectionner le nœud `AnimatedSprite2D` (enfant de `Player`). Dans l'inspecteur, à Sprite Frames, cliquer sur "Nouveau SpriteFrames", puis l'ouvrir (panneau en bas).

Pour chaque animation :

1. Cliquer sur "Ajouter une animation" et la nommer exactement comme dans le code.
2. Cliquer sur l'icône "Ajouter des images depuis une feuille de sprites" (la grille).
3. Choisir le fichier `_stripN.png` et indiquer Horizontal = N, Vertical = 1.
4. Sélectionner toutes les cases, puis Ajouter.

Créer ces 8 animations (les noms attendus par le code de Mina) :

| Animation | Fichier source | Cases |
|---|---|---|
| `walk_down` | `mina_walk_down_strip4.png` | 4 |
| `walk_up` | `mina_walk_up_strip4.png` | 4 |
| `walk_left` | `mina_walk_left_strip3.png` | 3 |
| `walk_right` | `mina_walk_right_strip3.png` | 3 |
| `idle_down` | (1re case de walk_down) | 1 |
| `idle_up` | (1re case de walk_up) | 1 |
| `idle_left` | (1re case de walk_left) | 1 |
| `idle_right` | (1re case de walk_right) | 1 |

Note : pour les `idle`, pas besoin d'un fichier dédié. Réutiliser la première image de la marche correspondante. Mina au repos, c'est la pose figée de départ.

Régler la vitesse (FPS) de chaque animation de marche autour de 6 à 8 FPS (le pixel art se prête aux animations un peu lentes) et cocher Loop (boucle).

---

## 2. Le code d'animation

Voici la fonction `animer_player()` du projet Mina. Elle complète le script de la [leçon précédente](./02-mouvement-topdown-mina.md) :

```gdscript
func animer_player():
	if direction != Vector2.ZERO:
		if direction.x > 0:
			animated_sprite_2d.play("walk_right")
		elif direction.x < 0:
			animated_sprite_2d.play("walk_left")
		elif direction.y > 0:
			animated_sprite_2d.play("walk_down")
		elif direction.y < 0:
			animated_sprite_2d.play("walk_up")
	else:
		if last_direction.x > 0:
			animated_sprite_2d.play("idle_right")
		elif last_direction.x < 0:
			animated_sprite_2d.play("idle_left")
		elif last_direction.y > 0:
			animated_sprite_2d.play("idle_down")
		elif last_direction.y < 0:
			animated_sprite_2d.play("idle_up")
```

Elle est appelée dans `_physics_process`, entre le déplacement et le `move_and_slide()` :

```gdscript
func _physics_process(_delta: float) -> void:
	avancer_player()
	animer_player()
	move_and_slide()
```

---

## 3. Explication

`if direction != Vector2.ZERO` : est-ce que Mina bouge ? Si oui, on joue une animation de marche. Sinon, une animation idle (repos).

`direction.x > 0` : elle va à droite. `.x < 0` : à gauche. `.y > 0` : vers le bas. `.y < 0` : vers le haut. En 2D, l'axe Y est inversé : le haut de l'écran correspond au Y négatif.

Pourquoi `last_direction` pour le idle : quand Mina s'arrête, `direction` vaut zéro et on ne saurait plus dans quel sens la faire regarder. On utilise donc la dernière direction connue (mémorisée à la leçon 2). Résultat : Mina s'arrête toujours face au bon côté.

---

## 4. Les 3 formes de Mina

Dans les assets, Mina existe en trois versions :

- `normal/` : l'aventurière de base.
- `baton/` : armée d'un bâton.
- `mage/` : la mage complète.

Pour l'instant, garder une seule forme active (la `normal`) pour ne pas se disperser. L'idée à retenir pour plus tard : chaque forme n'est qu'un jeu de SpriteFrames différent. Le jour où un système de classes ou d'équipement à la WoW deviendra utile, il suffira de remplacer le `sprite_frames` de l'`AnimatedSprite2D` selon la forme équipée :

```gdscript
# Exemple pour plus tard
@export var forme_normale: SpriteFrames
@export var forme_mage: SpriteFrames

func changer_forme(nouvelle: SpriteFrames):
	animated_sprite_2d.sprite_frames = nouvelle
```

Le code de déplacement et d'animation, lui, ne change pas : tant que chaque forme a bien les animations `walk_down`, `idle_left`, etc., tout continue de fonctionner. C'est l'intérêt d'une bonne architecture.

---

## Résultat

Mina marche, s'anime dans les 4 directions, et s'arrête face au bon côté. Prochaine grande étape : lui donner une épée et un premier ennemi (l'Œil de la Forêt). C'est là que le RPG commence vraiment.

Leçon précédente : [Mouvement top-down](./02-mouvement-topdown-mina.md)
