<template>
  <b-modal id="familiesModal" size="lg" title="Families & Distributors">
    <div class="header">
      <h3 class="text-left">Custom Categories</h3>
    </div>
    <hr>
    <Family v-for="family in families" :family="family" :key="family.id"></Family>
  </b-modal>
</template>

<script>
import axios from 'axios'
import Family from './Family'

export default {
  components: {
    Family
  },
  data() {
    return {
      families: []
    }
  },
  created() {
    axios.get('./static/categories-families.json')
      .then((res) => {
        this.families = res.data.data.filter((family) => family.title !== 'Unknown')
      })
      .catch((err) => {
        console.error(err);
      })
  }
}
</script>

<style>
</style>
