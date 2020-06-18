<template>
  <div class="masonry-filtering-buttons-group">
    <button
      type="button"
      :class="{ active : ( select == -1 )}"
      @click="filter(-1)"
       class="btn btn-outline-primary"
    >Tout</button>
    <button
      type="button"
      v-for="(label, index) in filters"
      :key="index"
      @click="filter( index )"
      :class="{ active : ( index == select) }"
      class="btn btn-outline-primary"
    >{{ label }}</button>
  </div>
</template>

<script>
export default {
  name: "IstpFilterControlBar",
  props: {
    filters: Array
  },
  data: function() {
    return {
      select: -1
    };
  },
  methods: {
    /**
     * Demande à Isotope de filtrer les éléments correspond au filtre {id}
     * 
     * @Param {number} id - Id. du filtre à appliquer.
     */
    filter(id) {
      if (id !== this.$data.select) {
        this.$data.select = id;
        this.$emit("filtering", id);
      }
    }
  }
};
</script>

<style lang="scss">
@import "../scss/_breakpoints.scss";

.masonry-filtering-buttons-group {
  text-align: left;

  button {
    display: block;
    width: 100%;
    margin-bottom: 1rem;
    @include media-breakpoint-up(sm) {
      display: inline;
      width: auto;
      margin-right: 1rem;
    }
  }
}
</style>