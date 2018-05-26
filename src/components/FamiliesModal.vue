<template>
  <b-modal id="familiesModal" title="Families Modal"></b-modal>
</template>

<script>
import axios from 'axios'

export default {
  data() {
    return {
      families: []
    }
  },
  created() {
    axios.get('./static/categories-families.json')
      .then((res) => {
        this.families = res.data.data
      })
      .catch((err) => {
        console.error(err);
      })
  }
}
</script>

<style>
</style>
