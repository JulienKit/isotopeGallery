<template>
  <div class="istp-masonry">
    <component :is="filterControl" :filters="filters" @filtering="filtering"></component>
    <div v-if="!dataReady" class="spinner-border" role="status">
      <span class="sr-only">Loading...</span>
    </div>
    <div class="istp-masonry__grid" v-else>
      <figure
        v-for="(item, index) in items"
        :key="item.src"
        itemprop="associatedMedia"
        itemscope
        itemtype="http://schema.org/ImageObject"
        class="istp-masonry__item"
        :class="getItemFilters(item)"
      >
        <img
          @click="showPhotoSwipe(index)"
          :src="item.msrc"
          :width="item.w"
          :height="item.h"
          itemprop="thumbnail"
          :alt="item.title"
          class="img-fluid"
        />
      </figure>
    </div>
    <pswp v-if="items" :isOpen="isOpen" :items="items" :options="options" @close="hidePhotoSwipe"></pswp>
  </div>
</template>

<script>
import * as Isotope from "isotope-layout";
import * as ImagesLoaded from "imagesloaded";
import Pswp from "./Pswp.vue";
export default {
  name: "IsotopeGallery",
  components: {
    Pswp
  },
  props: {
    // Nom de la procédure de récupération des datas nécessaires au fonctionnement de la gallerie.
    fetch: {
      type: String,
      required: true
    },
    // Composant Vue de contrôle des filtres Isotope.
    filterControl: {
      type: Object,
      required: true
    }
  },
  data: function() {
    return {
      // vaux vrai si les images sont chargées
      imagesLoaded: false,
      // vaux vrai si les datas ont été chargées
      dataReady: false,
      // instance de Isotope
      Istp: Object,
      items: Array,
      categories: Array,
      /*
       * PhotoSwipe Options
       */
      isOpen: false,
      isOpenGallery: false,
      options: {
        index: 0,
        shareButtons: [
          {
            id: "facebook",
            label: "Partager sur Facebook",
            url: "https://www.facebook.com/sharer/sharer.php?u={{url}}"
          },
          {
            id: "twitter",
            label: "Tweeter",
            url: "https://twitter.com/intent/tweet?text={{text}}&url={{url}}"
          },
          {
            id: "pinterest",
            label: "Éplinger sur Pinterest",
            url:
              "http://www.pinterest.com/pin/create/button/?url={{url}}&media={{image_url}}&description={{text}}"
          },
          {
            id: "download",
            label: "Télécharger l'image",
            url: "{{raw_image_url}}",
            download: true
          }
        ]
      }
    };
  },
  beforeMount() {
    if (window[this.$props.fetch] === undefined)
      throw `[OxawebMasonry] fetch prop : Undefined fetching function "${this.$props.fetch}".`;

    /*window[this.$props.loadFn]().then((response) =>{
      this.$data.items = response;
    }).catch((e)=>{
      console.error(e);
    });*/
  },
  mounted() {
    // récupération des datas.
    window[this.$props.fetch]()
      .then(r => {
        this.$data.items = r.items;
        this.$data.categories = r.categories;
        this.$data.dataReady = true;
        this.beginIstp();
      })
      .catch(e => {
        console.error(e);
      });
  },
  computed: {
    filters(){

      try{
        if(this.$data.categories !== undefined){
          if(this.$data.categories.length !== 0){
            let filtersdata = [];
            for(let i=0;i<this.$data.categories.length;i++) {
              filtersdata.push(this.$data.categories[i].label);
            }
            return filtersdata;
          }
        }
      } catch(e){
  
        return []
      }

      return []
    }
  },
  methods: {
    /**
     * Intervient après la récupération des datas. Instancie Isotope. 
     */
    beginIstp() {
      this.$nextTick(function() {
        let grid = document.querySelector(".istp-masonry__grid");

        this.$data.Istp = new Isotope(grid, {
          itemSelector: ".istp-masonry__item",
          percentPosition: true,
          masonry: {
            columnWidth: ".istp-masonry__item",
            gutter: 0
          }
        });

        ImagesLoaded(grid).on("progress", () => {
          this.$data.Istp.layout();
        });
      });
    },
    /**
     * Récupère les filtres (s'il y en a) d'item pour l'attribut class de <figure>. 
     * 
     * @param item - item dont on veut les filtres.
     */
    getItemFilters(item){
      if(item.filters !== undefined) {
        if(item.filters.length){
          return item.filters.join(' ')
        }
      }

      return '';
    },
    /**
     * Arrange les images selon l'identifiant des filtres choisis. 
     * 
     * @Param {number} id - Id. du filtre à appliquer.
     */
    filtering(id) { //eslint-disable-line
      let filterValue = id == -1 ? "*" : this.$data.categories[id].filters;
      this.$data.Istp.arrange({filter : filterValue});
    },
    /*
     * méthode pour PhotoSwipe 
     */
    showPhotoSwipe(index) {
      this.isOpen = true;
      this.$set(this.options, "index", index);
    },
    hidePhotoSwipe() {
      this.isOpen = false;
    }
  }
};
</script>

<style lang="scss">
@import "../scss/breakpoints";

$columns-default: 3;

$columns: (
  md: 4,
  xl: 5
);

.istp-mansory {
  width: 100%;
}

.istp-masonry__grid {
  display: flex;
  flex-flow: wrap;

  .istp-masonry__item {
    flex: 0 1 auto;
    width: (100 / $columns-default)#{"%"};
    margin-bottom: 0;
    cursor: pointer;

    @each $breakp, $cols in $columns {
      @include media-breakpoint-up($breakp){
        width: round(100/$cols)#{"%"};
      }
    }

    img {
      width: 100%;
      padding: 0.5rem;
    }
  }
}
</style>
