<template>
  <b-modal id="familiesModal" size="lg" centered ok-only title="Families & Distributors">
    <div v-if="showFamilies">
      <div class="header">
        <h3 class="text-left">Custom Categories</h3>
      </div>
      <hr>
      <div v-for="family in families" class="m-3">
        <h5 class="text-left mb-2">{{ family.title }}</h5>
        <b-table fixed small :items="family.categories" :fields="fields">
          <template slot="title" slot-scope="data">
            <a href="#" v-on:click="openForm(family.id, data.item.id)">{{ data.item.title }}</a>
          </template>
          <template slot="bottleCount" slot-scope="data">
            {{ randomBottleCount() }}
          </template>
          <template slot="delete" slot-scope="data">
            Delete
          </template>
        </b-table>
      </div>
    </div>
    <div v-else>
      <table class="table">
        <thead>
          <tr>
            <th>Title</th>
            <th>Density of Alcohol</th>
            <th>Excluded From Variance</th>
            <th>Partial</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td>
              <b-form-group>
                <b-form-input name="title" v-model="category.title" type="text"></b-form-input>
              </b-form-group>
            </td>
            <td>
              <b-form-group>
                <b-form-input name="range" list="tickmarks" v-model.number="category.density" min="0.5" max="1.5" step="0.01" type="number"></b-form-input>
              </b-form-group>
            </td>
            <td>
              <b-form-group>
                <b-form-checkbox id="excluded_from_variance" v-model="category.excluded_from_variance"></b-form-checkbox>
              </b-form-group>
            </td>
            <td>
              <b-form-group>
                <b-form-radio-group id="partial" v-model="category.partial" :selected-value="category.partial" name="categoryPartial" stacked>
                  <b-form-radio value="slider">Slider</b-form-radio>
                  <b-form-radio value="scale">Scale</b-form-radio>
                  <b-form-radio value="none">None</b-form-radio>
                </b-form-radio-group>
              </b-form-group>
            </td>
          </tr>
        </tbody>
      </table>
      <b-form-group class="float-right">
        <b-button type="cancel" v-on:click="cancelUpdate" variant="danger">Cancel</b-button>
        <b-button type="submit" variant="primary" v-on:click="updateCategory">Submit</b-button>
      </b-form-group>
    </div>
  </b-modal>
</template>

<script>
import axios from 'axios'

export default {
  data() {
    return {
      families: [],
      showFamilies: true,
      fields: [
        {
          key: 'title',
          label: 'Category Name'
        },
        'bottleCount',
        'delete'
      ],
      category: {}
    }
  },
  created() {
    axios.get('./static/categories-families.json')
      .then((res) => {
        this.families = res.data.data.filter((family) => family.title !== 'Unknown')
        this.category = this.families[0].categories[0]
      })
      .catch((err) => {
        console.error(err);
      })
  },
  methods: {
    randomBottleCount() {
      return Math.floor(Math.random() * Math.floor(10));
    },
    openForm(familyId, categoryId) {
      const selectedFamily = this.families.filter(family => family.id === familyId)[0]

      this.showFamilies = false
      this.category = selectedFamily.categories.filter(category => category.id === categoryId)[0]
    },
    updateCategory() {
      this.showFamilies = true
    },
    cancelUpdate() {
      this.showFamilies = true
    }
  }
}
</script>

<style>
</style>
