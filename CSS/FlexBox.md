# FlexBox

## Alignement d'un groupe d'élément

### <u>Horizontal</u> :

Cette propriété CSS définit l'alignement horizontal des éléments qui sont présent dans le container actuel. 

```css
.class {
    display: flex;
    justify-content: ...
}
```

Il existe différentes propriétés : 
- `flex-start` : Les éléments s'alignent au côté gauche du conteneur
- `flex-end` : Les éléments s'alignent au côté droit du conteneur
- `center` : Les éléments s'alignent au centre du conteneur
- `space-between` : Les éléments s'affichent avec un espace égal entre eux
- `space-around` : Les éléments s'affichent avec un espacement égal à autour d'eux (entre eux + bord du container)

### <u>Vertical</u> : 

Cette propriété CSS définit l'alignement vertical des éléments qui sont présent dans le container actuel. 

```css
.class {
    display: flex;
    align-items: ...
}
```    

Il existe différentes propriétés : 
- `flex-start` : Les éléments s'alignent au côté gauche du conteneur
- `flex-end` : Les éléments s'alignent au côté droit du conteneur
- `center` : Les éléments s'alignent au centre du conteneur
- `baseline` : Les éléments s'alignent à la ligne de base du conteneur
- `stretch` : Les éléments sont étirés pour s'adapter au conteneur

---

## Alignement d'un élément unique

Cette propriété CSS définit l'alignement horizontal des éléments qui sont présent dans le container actuel. Elle reprend les mêmes propriétés que <u>[`align-items`](#uverticalu)</u>

---

## Alignement des lignes

Cette propriété CSS `align-content` détermine l'espace entre les lignes, alors que `align-items` détermine comment les éléments dans leur ensemble sont alignées à l'intérieur du conteneur. Quand il n'y a qu'une seule ligne, `align-content` n'a aucun effet.

```css
.class {
    display: flex;
    align-items: ...
}
```   

Il existe différentes propriétés : 
- `flex-start` : Les lignes sont amassées dans le haut du conteneur
- `flex-end` : Les lignes sont amassées dans le bas du conteneur
- `center` : Les lignes sont amassées dans le centre vertical du conteneur
- `space-between` : Les lignes s'affichent avec un espace égal entre elles
- `space-around` : Les lignes s'affichent avec un espace égal autour d'elles
- `stretch` : Les lignes sont étirées pour s'adapter au conteneur

---


## Direction d'un groupe d'élément

Cette propriété CSS définit la direction dans laquelle les éléments sont placés dans le conteneur actuel. 

```css
.class {
    display: flex;
    flex-direction: ...
}
```  

Il existe différentes propriétés : 
- `row` : Les éléments sont disposés de manière horizontale dans le sens choisi dans le HTML
- `row-reverse` : Les éléments sont disposés de manière horizontale dans le sens inverse choisi dans le HTML
- `column` : Les éléments sont disposés de haut en bas
- `column-reverse` : Les éléments sont disposés de bas en haut

En cas de couplage entre `flex-direction` et `justify-content` / `align-items` il faut adapté le comportement. Si l'on se trouve avec une direction `...-reverse` le `start` et le `end` de l'alignement horizontal seront opposés.

---

## Ordre d'un élément

Parfois, inverser l'ordre de la rangée ou la colonne ne suffit pas. Dans ces cas, on peut appliquer la propriété order à des éléments individuels. Par défaut, les éléments ont une valeur de 0, mais on peut utiliser cette propriété pour changer la valeur à un entier positif ou négatif.

```css
.class {
    display: flex;
    order: ...
}
```

---

## Passage à la ligne

Elle permet de gérer le passage à la ligne ou non des éléments. 

```css
.class {
    display: flex;
    flex-wrap: ...
}
```

Il existe différentes propriétés : 
- `nowrap` : Tous les éléments sont tenus sur une seule ligne
- `wrap` : Les éléments s'enveloppent sur plusieurs lignes au besoin
- `wrap-reverse` : Les éléments s'enveloppent sur plusieurs lignes dans l'ordre inversé

### <u>Raccourci</u> :

Comme la propriété `flex-wrap` est très souvent utilisée en même temps que `flex-direction` il existe une propriété qui permet de cumuler les deux en un. C'est `flex-flow` qui prend en premier paramètre une valeur du `flex-direction` et en second une valeur de `flex-wrap`.

```css
.class {
    display: flex;
    flex-wrap: flex-direction flex-wrap
}
```

--- 

## Entraînement 

https://flexboxfroggy.com/#fr 

## Documentation

https://css-tricks.com/snippets/css/a-guide-to-flexbox/
