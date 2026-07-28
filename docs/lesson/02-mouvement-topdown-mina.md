# Faire marcher Mina dans 4 directions (mouvement top-down)

> Niveau : débutant. Durée : 15 min. Godot : 4.x

Objectif : faire bouger Mina dans les 4 directions (haut, bas, gauche, droite) au clavier, façon Zelda vu de dessus. Le code ci-dessous est le vrai script `player.gd` du projet Mina, décortiqué ligne par ligne.

---

## Les nœuds nécessaires

- Un nœud `CharacterBody2D` (le corps physique du perso, idéal pour un joueur qui se déplace).
- Un enfant `AnimatedSprite2D` (le dessin animé de Mina, préparé dans la [leçon 3](./03-animer-mina.md)).
- Un `CollisionShape2D` (pour que Mina ne traverse pas les murs).

L'arbre ressemble à ceci :

```
Player (CharacterBody2D)
├── AnimatedSprite2D
└── CollisionShape2D
```

---

## 1. Créer les touches (Input Map)

Aller dans Projet > Paramètres du projet > Contrôles (Input Map) et créer 4 actions :

| Action | Touche (AZERTY) |
|---|---|
| `gauche` | Q |
| `droite` | D |
| `haut` | Z |
| `bas` | S |

Note : on donne des noms d'action (`gauche`, `droite`...) plutôt que des touches en dur. Ainsi, pour ajouter une manette ou changer les touches plus tard, tout se règle ici sans toucher au code.

---

## 2. Le script complet

Ce script est attaché au nœud `Player`. C'est le code réel de Mina :

```gdscript
extends CharacterBody2D

@export var vitesse: float = 60.0
@onready var animated_sprite_2d: AnimatedSprite2D = $AnimatedSprite2D

var direction: Vector2 = Vector2.ZERO
var last_direction: Vector2 = Vector2.DOWN

func _physics_process(_delta: float) -> void:
	avancer_player()
	animer_player()
	move_and_slide()

func avancer_player():
	direction = Input.get_vector("gauche", "droite", "haut", "bas")

	# Garder en mémoire la dernière direction
	if direction != Vector2.ZERO:
		last_direction = direction

	velocity = direction * vitesse
```

(La partie animation est détaillée juste après.)

---

## 3. Explication ligne par ligne

`@export var vitesse: float = 60.0`
Le `@export` fait apparaître la vitesse dans l'inspecteur de Godot : réglable à la souris sans rouvrir le code.

`@onready var animated_sprite_2d = $AnimatedSprite2D`
`@onready` attend que la scène soit prête, puis récupère le sprite enfant. Le `$` signifie "va chercher le nœud enfant qui porte ce nom".

`Input.get_vector("gauche", "droite", "haut", "bas")`
La ligne centrale. Elle renvoie directement un Vector2 (une direction) selon les touches appuyées. D donne `(1, 0)`, Z donne `(0, -1)`. Elle normalise aussi les diagonales : sans ça, marcher en diagonale serait plus rapide qu'en ligne droite (un piège classique du débutant).

`if direction != Vector2.ZERO: last_direction = direction`
Quand Mina s'arrête, `direction` redevient zéro. Mais on veut qu'elle continue de regarder dans la dernière direction où elle allait (pour l'animation d'attente). On mémorise donc la dernière direction non nulle.

`velocity = direction * vitesse`
La vitesse finale : la direction (x1) multipliée par la vitesse (x60).

`move_and_slide()`
La commande qui applique le mouvement et gère les collisions (Mina glisse le long des murs au lieu de se coincer). Fournie par le `CharacterBody2D`.

---

## Résultat

Au lancement, Mina se déplace dans les 4 directions et s'arrête proprement. Il reste à l'animer pour qu'elle ait l'air vivante.

Leçon suivante : [Animer Mina et ses 3 formes](./03-animer-mina.md)
