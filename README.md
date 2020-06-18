# Isotope Gallery (VueJS)

Affiche une galerie Masonry intéractif avec des filtres avec VueJS, Isotope, PhotoSwipe et est prévu pour être affiché avec Bootstrap.

Construit avec un composant IsotopeGallery qui est indépendant des filtres, on peut lui fournir son propre composant permettant de contrôler les filtres.

Le composant PhotoSwipe Pswp provient du travail de @ethanlixian : https://github.com/Leesson/v-photoswipe, juste les libellés des contrôles ont été traduits en français.

## Get Started

Intégrer le JS et le CSS : 

```html
<link href="app.<cache>.css" rel="stylesheet">
```
```html
<script src="app.<cache>.js"></script>
```
Dans la page, intégrer un conteneur div#app :
```html
<div id="app"></div>
```
Par défaut la galerie utilise une fonction "FetchImages" pour s'alimenter qui doit renvoyer un tableau de données sous la forme : 
```js
{
    // Catégorie de filtre
    "categories": [
        {
            
            "label": "", // Libellé du bouton
            "filters": "" // Les filtres
        }
    ],
    // La liste des photos
    "items": [
        {
            // Photo principal
            "src": "", // Url
            "w": 0, // Largeur
            "h": 0, // Hauteur
            // Photo vignette (thumbnail)
            "msrc":"", // Url
            "mw": 0, // Largeur
            "mh": 0, // Hauteur
            "filters": [ // Filtres de la photo
                "", ...
            ]
        }
    ]
}
```

## Intégration via le composant IsotopeGallery

Le composant nécessite 2 propriétés :
* 'fetch' qui correspond au nom de la méthode qui sera appelé pour récupérer les datas. 
* 'filterControl' qui correspond au nom du composant VueJS qui sera utilisé au dessus de la galerie pour intéragir avec les filtres.

exemple : 
```js 
     <IsotopeGallery :filter-control="MonComposant" fetch="FetchImages" />
```

## Créer son propre composant pour contrôler les filtres

Vous pouvez créer votre propre composant à passer à la propriété 'filter-control' de IsotopeGallery. 

Il doit avoir une propriété 'filters' qui recevra du composant IsotopeGallery un tableau de chaîne correspondant aux différents filtres vous permettant de les afficher comme vous le souhaitez. 

Exemple issu du composant IstpFilterControlBar qui boucle sur les filtres pour afficher des ```<button>```. 

```js
    <button
      type="button"
      v-for="(label, index) in filters"
      :key="index"
      @click="filter( index )"
      :class="{ active : ( index == select) }"
      class="btn btn-outline-primary"
    >{{ label }}
    </button>
```
### La méthode de filtre 

Pour pouvoir déclencher le filtrage votre composant doit émettre un event "**filtering**" et lui donner en paramètre l'indice du filtre dans le tableau de la propriété _filters_: 
```js
    filter(id) {
      if (id !== this.$data.select) {
        this.$data.select = id;
        this.$emit("filtering", id);
      }
    } 
```
Pour pouvoir tout afficher (comme une option "Tout") il faut passer à cette event l'indice "-1". 
```js
    this.$emit("filtering", -1)
```

### Modifier le nombre de colonnes selon la taille de l'écran

Le nombre de colonnes affichées par la galerie est modifiable dans le style du composant IsotopeGallery. 

La variable map scss qui contrôle le nombre de colonnes se nomme ```$column``` . Elle prend en clé le breakpoint Bootstrap (sm, md, lg, xl ou xll) et en valeur le nombre de colonnes souhaité. 

exemple : 
```scss
    $columns: (
        md: 4,
        xl: 5
    );
```

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).
