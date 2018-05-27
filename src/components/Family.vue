<template>
  <div class="m-3">
    <h5 class="text-left mb-2">{{ family.title }}</h5>
    <b-table fixed small :items="family.categories" :fields="fields">
      <template slot="title" slot-scope="data">
        <a href="#">{{ data.item.title }}</a>
      </template>
      <template slot="bottleCount" slot-scope="data">
        {{ randomBottleCount() }}
      </template>
      <template slot="delete" slot-scope="data">
        Delete
      </template>
    </b-table>
  </div>
</template>

<script>
export default {
  props: {
    family: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      fields: [
        {
          key: 'title',
          label: 'Category Name'
        },
        'bottleCount',
        'delete'
      ]
    }
  },
  methods: {
    randomBottleCount() {
      return Math.floor(Math.random() * Math.floor(10));
    }
  }
}
</script>

<style lang="css">
</style>
