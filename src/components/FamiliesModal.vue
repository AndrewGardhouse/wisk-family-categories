<template>
  <b-modal id="familiesModal" size="lg" centered ok-only title="Families & Distributors">
    <div v-if="showFamilies">
      <div class="header">
        <h3 class="text-left d-inline-block">Custom Categories</h3>
        <b-button-group class="float-right" size="sm">
          <b-button class="m-1" variant="primary">Add New Family</b-button>
          <b-button class="m-1" variant="primary">Add New Category</b-button>
        </b-button-group>
      </div>
      <hr>
      <div v-for="family in families" class="m-3">
        <b-form inline v-if="family.editFamily" v-on:submit.prevent="closeFamilyField(family)" class="mb-2">
          <b-form-group>
            <b-form-input name="title" v-model="family.title" type="text" required></b-form-input>
            <b-button type="submit" variant="primary">Ok</b-button>
          </b-form-group>
        </b-form>
        <h5 class="text-left mb-2" v-else>
          {{ family.title }}
          <small>
            (<a href="#" v-on:click.prevent="showEditFamilyField(family)">Edit</a> | <a href="#" v-on:click.prevent="deleteFamily(family)" class="text-danger">Delete</a>)
          </small>
        </h5>
        <b-table fixed small :items="family.categories" :fields="fields">
          <template slot="title" slot-scope="data">
            <a href="#" v-on:click.prevent="openEditCategoryForm(data.item)">
              {{ data.item.title }}
            </a>
          </template>
          <template slot="bottleCount" slot-scope="data">
            {{ randomBottleCount() }}
          </template>
          <template slot="delete" slot-scope="data">
            <a href="#" v-on:click.prevent="deleteCategory(family.id, data.item)" class="text-danger">
              Delete
            </a>
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
        <b-button type="submit" variant="primary" v-on:click="updateCategory">Ok</b-button>
      </b-form-group>
    </div>
  </b-modal>
</template>

<script>
import axios from 'axios';

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
        res.data.data.forEach(family => family.editFamily = false);
        this.families = res.data.data.filter((family) => family.title !== 'Unknown');
      })
      .catch((err) => {
        console.error(err);
      })
  },
  methods: {
    randomBottleCount() {
      return Math.floor(Math.random() * Math.floor(10));
    },
    openEditCategoryForm(category) {
      this.showFamilies = false;
      this.category = category;
    },
    updateCategory() {
      this.showFamilies = true;
    },
    cancelUpdate() {
      this.showFamilies = true;
      this.category = {};
    },
    deleteCategory(familyId, category) {
      const categories = this.families.find(family => family.id === familyId).categories;
      const categoryIndex = categories.indexOf(category);

      categories.splice(categoryIndex, 1);
    },
    deleteFamily(family) {
      const familyIndex = this.families.indexOf(family);

      this.families.splice(familyIndex, 1);
    },
    showEditFamilyField(editableFamily) {
      const family = this.families.find(family => family === editableFamily);
      family.editFamily = true;
    },
    closeFamilyField(editableFamily) {
      const family = this.families.find(family => family === editableFamily);
      family.editFamily = false;
    }
  }
}
</script>

<style>
</style>
