# Isotope Gallery (VueJS)

Affiche une galerie Masonry avec possibilité de filtrer les images.

La galerie au layout masonry est affichée et controlée avec la librairie [Isotope](https://isotope.metafizzy.co/). Les photos sont des thumbnails et la librairie 
[PhotoSwipe](https://photoswipe.com/) permet d'ajouter de l'intéractivité en affichant au clic une version "grand format" de l'image et des contrôles au doigt sur smartphone.

## Get Started

La galerie peut être utilisée telle quelle une fois avoir construit les sources avec ```npm run build``` . 

Pour cela il faut : intégrer le JS et le CSS : 

```html
<link href="app.<cache>.css" rel="stylesheet">
```
```html
<script src="app.<cache>.js"></script>
```
Dans une page, intégrer un conteneur div#app :
```html
<div id="app"></div>
```
Et déclarer une fonction globale ```FetchImages()```  qui doit renvoyer la liste des images ainsi que les catégories de filtres sous la forme suivante : 
```js
{
    // catégorie de filtre
    "categories": [
        {
            
            "label": "", // libellé du filtre
            "filters": "" // class associées aux filtres : ".fitre-1 .filtre-2"
        }
    ],
    // liste des photos
    "items": [
        {
           
            "src": "", // src de la photo principale
            "w": 0, // largeur
            "h": 0, // hauteur
            "msrc":"", // src du thumbnail
            "filters": [ // filtres de la photo comme défini dans categories.filters
                "", ...
                // "filtre-1",
                // "filtre-2" ...
            ]
        }
    ]
}
```

## Les composants en jeu

### le composant PhotoSwipe **Pswp.vue**

Pswp provient du travail de @ethanlixian : https://github.com/Leesson/v-photoswipe, juste les libellés des contrôles ont été traduits en français.

### le composant **IsotopeGallery.vue**

Possède l'instance d'Isotope, affiche et contrôle la galerie. Utilise .Pswp ainsi qu'un composant de contrôle des filtres. 

Le composant nécessite 2 propriétés pour fonctionner : 
* ```fetch``` : nom de la méthode qui sera appelée pour récupérer les datas. 
* ```filter-control``` : nom du composant qui sera placé au dessus de la galerie et qui permet de filtrer les images. 

exemple : 
```js 
     <IsotopeGallery :filter-control="MonComposant" fetch="FetchImages" />
```

### le composant **IstpFilterControlBar.vue**

Affiche des boutons les uns à côtés des autres pour filtrer les images. 


### Créer son propre composant pour contrôler les filtres

Vous pouvez créer votre propre composant à passer à la propriété ```filter-control``` de ```<IsotopeGallery>```. 

#### Propriété filtrer

Ce composant devra avoir une propriété ```filters``` qui recevra du composant IsotopeGallery un tableau de chaîne correspondant aux différents filtres vous permettant de les afficher comme vous le souhaitez. 

Cette prop est alimentée par ```<IsotopeGallery>``` via les datas récupérées par la prop ```fetch``` de celle-ci et correspondant à ```categories``` . 

Exemple issu du composant **IstpFilterControlBar** qui boucle sur les filtres pour afficher des ```<button>```. 

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
#### L'évènement filtering

Pour pouvoir déclencher le filtrage, votre composant doit émettre un event ```filtering``` et lui fournir en paramètre l'indice du filtre dans le tableau de la propriété _filters_: 
```js
    filter(id) {
      if (id !== this.$data.select) {
        this.$data.select = id;
        this.$emit("filtering", id);
      }
    } 
```
Pour pouvoir tout afficher (comme une option "Tout") il faut passer à cet event l'indice "-1". 
```js
    this.$emit("filtering", -1)
```

### Modifier le nombre de colonnes selon la taille de l'écran

Le nombre de colonnes affichées par la galerie est modifiable dans le style du composant IsotopeGallery. 

La variable map scss qui contrôle le nombre de colonnes se nomme ```$column``` . Elle prend en clé le breakpoint Bootstrap (```sm, md, lg, xl ou xll```) et en valeur le nombre de colonnes souhaitées. 

Par défaut : 
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
