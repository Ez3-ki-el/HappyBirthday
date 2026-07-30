# Feliz cumpleaños, Andrea

Site statique d'anniversaire, une seule page, sans dépendance ni build.
Thème : atelier de poterie mexicaine. Douze pièces sont posées sur des étagères ;
on en prend une et elle livre un compliment.

## Fichiers

| Fichier | Rôle |
|---|---|
| `index.html` | Tout le site : HTML, CSS, JS, poteries en SVG. Aucun asset externe hormis les polices Google. |
| `.nojekyll` | Désactive le pipeline Jekyll de GitHub Pages, qui n'a rien à faire ici. |
| `README.md` | Ce fichier. |

## Modifier les textes

Tout est dans un seul tableau, en haut du `<script>` de `index.html` :

```js
const PIEZAS = [
  { texto:"Tienes manos que saben volver bonito todo lo que tocan.",
    forma:"olla", nombre:"olla de barro", color:"#b5541f", motif:"#f6e6c8", escala:0.99 },
  ...
];
```

- `texto` : le compliment affiché sur la carte. C'est le seul champ à toucher pour réécrire un message.
- `forma` : silhouette de la pièce, au choix `olla`, `jarron`, `cazuela`, `cantaro`.
- `nombre` : nom de la pièce, affiché en petit sous le compliment.
- `color` : couleur de la terre ou de l'émail.
- `motif` : couleur des décors peints sur la pièce.
- `escala` : taille relative sur l'étagère, entre `0.90` et `1.06`.

Le nombre de pièces est libre : le compteur, les perles du bas et le final s'y
adaptent automatiquement. Pour que les étagères restent bien remplies, un total
divisible par 2, 3, 4 et 6 est idéal (12 l'est).

Le message final se modifie directement dans le HTML, bloc `<div class="finale">`.

## Comportement

- Clic ou tap sur une pièce : elle se soulève, garde un point d'or, et une carte s'ouvre avec son compliment.
- Une poussière d'argile part de la pièce au premier contact.
- Les perles en bas de page se colorent à la couleur de chaque pièce déjà prise.
- Les douze pièces trouvées : le message final s'affiche, avec une pluie d'éclats d'émail.
- `Échap`, la croix ou un clic à côté ferment une carte.
- `volver al estante` remet tout à zéro.

## Responsive

Les étagères sont recomposées en JavaScript selon la largeur, pour qu'un vrai
plateau en bois passe sous chaque rangée :

| Largeur | Colonnes | Étagères |
|---|---|---|
| < 360 px | 2 | 6 |
| 360 - 660 px | 3 | 4 |
| 660 - 1000 px | 4 | 3 |
| > 1000 px | 6 | 2 |

Sur un téléphone de 375 px, la page entière (titre, les quatre étagères et le
compteur) tient dans un seul écran sans défilement. La rotation de l'écran
recompose les étagères en conservant les pièces déjà prises.

## Accessibilité

- Chaque pièce est un `<button>` atteignable au clavier, libellé `pieza N de 12`.
- Les cartes et le final sont des `role="dialog"` avec `aria-modal`, fermables à `Échap`.
- Le compliment est annoncé dans une région `aria-live`.
- `prefers-reduced-motion` coupe la poussière, les éclats d'émail et les transitions.

## Mise en ligne sur GitHub Pages

```bash
git init -b main
git add .
git commit -m "Site d'anniversaire pour Andrea"
git remote add origin https://github.com/Ez3-ki-el/HappyBirthday.github.io.git
git push -u origin main
```

Puis dans le dépôt : `Settings` > `Pages` > `Build and deployment` >
`Deploy from a branch` > branche `main`, dossier `/ (root)`.

Le dépôt s'appelant `HappyBirthday.github.io` sous le compte `Ez3-ki-el`, GitHub
le sert comme *project page* :

```
https://ez3-ki-el.github.io/HappyBirthday.github.io/
```

Pour obtenir l'adresse courte `https://ez3-ki-el.github.io/`, il faut renommer le
dépôt en exactement `Ez3-ki-el.github.io`. Le site fonctionne dans les deux cas.
