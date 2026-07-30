# Feliz cumpleaños, Andrea

Site statique d'anniversaire, une seule page, sans dépendance ni build.
Thème : atelier de poterie mexicaine. Douze pièces sont posées sur des étagères ;
on en prend une et elle livre un compliment.

## Fichiers

| Fichier | Rôle |
|---|---|
| `index.html` | Tout le site : HTML, CSS, JS, poteries en SVG. Aucun asset externe hormis les polices Google. |
| `HB.mp3` | La musique de l'easter egg. 320 kbps CBR, 44,1 kHz, 4 min 20 s, 10,4 Mo. |
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

## Easter egg : la musique

**Un appui long d'environ 0,6 seconde sur le prénom « Andrea » lance le morceau.**
Un second appui long le coupe, avec un fondu de 0,7 s. Pendant l'appui, le prénom
s'auréole progressivement : c'est le seul indice donné.

Le morceau démarre à 20 secondes, et reboucle à 20 secondes plutôt qu'au début.
Pour changer ce point de départ, une seule constante en haut du bloc easter egg :

```js
const ARRANQUE = 20;   // seconde de depart, et de rebouclage
const RETENER = 620;   // duree de l'appui long, en ms
```

Quand la musique joue, les poteries dansent réellement sur le kick. Le son passe
par un `AnalyserNode` Web Audio, dont on lit l'énergie des basses (bandes de 43 à
150 Hz), avec :

- une **normalisation auto-adaptative** pic/plancher, pour que l'amplitude reste
  correcte quelle que soit la section du morceau, sans réglage manuel ;
- une **courbe en puissance 2,5** et une décroissance de 0,84 par image, pour que
  le mouvement rebondisse au lieu de flotter ;
- un **décalage par pièce** (`_lag`, huit images d'historique) pour que le
  mouvement ondule le long de l'étagère au lieu de bouger d'un bloc.

La lumière de l'atelier pulse aussi, et le prénom rougeoie au rythme des basses.

Après avoir vu le message final, le prénom se met à respirer doucement toutes les
4,6 s pour rendre l'easter egg trouvable. Pour supprimer cet indice, retire
`document.body.classList.add('pista')` dans `cerrarFinal()`.

### Limites connues

- **Le bouton silencieux de l'iPhone coupe le son**, quel que soit le volume.
  C'est le comportement d'iOS sur l'audio d'une page web, il n'y a pas de
  contournement fiable.
- **L'hébergeur doit gérer les requêtes HTTP `Range`**, sinon le navigateur
  considère le flux non-seekable et le démarrage à 20 s échoue silencieusement,
  le morceau partant du début. GitHub Pages les gère. Vérifié : `Accept-Ranges:
  bytes` et `206 Partial Content` sur la production.
- Le fichier n'est **pas** téléchargé au chargement de la page. Le préchargement
  se déclenche à la première poterie prise, ou au début de l'appui long.
- Onglet caché : `requestAnimationFrame` se met en pause, donc la danse se fige
  pendant que le son continue. Le niveau est remis à zéro pour éviter que les
  pièces restent bloquées en position haute.

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
- `prefers-reduced-motion` coupe la poussière, les éclats d'émail et les transitions. La musique reste disponible, mais les poteries ne dansent pas.
- L'allumage et l'extinction de la musique sont annoncés dans la région `aria-live`.

## Mise en ligne sur GitHub Pages

Le site est en ligne à cette adresse :

```
https://ez3-ki-el.github.io/HappyBirthday/
```

Dépôt : `https://github.com/Ez3-ki-el/HappyBirthday`, publié depuis la branche
`main`, dossier `/ (root)`, réglable dans `Settings` > `Pages`.

Pour publier une modification :

```bash
git add -A
git commit -m "Le message qui va bien"
git push origin main
```

GitHub Pages republie en une minute environ. Si l'ancienne version s'affiche
encore, c'est le cache du navigateur : un rechargement forcé suffit.

Pour obtenir l'adresse courte `https://ez3-ki-el.github.io/` sans sous-dossier,
il faudrait renommer le dépôt en exactement `Ez3-ki-el.github.io`.
