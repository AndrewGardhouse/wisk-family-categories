<template>
  <b-modal id="familiesModal" size="lg" centered ok-only title="Families & Distributors">
    <div v-if="showEditCategoryForm">
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
                <b-form-input name="range" v-model.number="category.density" min="0.5" max="1.5" step="0.01" type="number"></b-form-input>
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
    <div v-else-if="showNewFamilyForm">
      <h3 class="mb-4">Add New Alcohol Family</h3>
      <b-form v-on:submit.prevent="createFamily">
        <b-form-group label="<strong>Title</strong>">
          <b-form-input name="familyTitle" v-model="newFamilyTitle" type="text" placeholder="Add Family Name" required></b-form-input>
        </b-form-group>
        <b-form-group class="float-right">
          <b-button variant="danger" v-on:click="cancelCreateFamily">Cancel</b-button>
          <b-button type="submit" variant="primary">Add Family</b-button>
        </b-form-group>
      </b-form>
    </div>
    <div v-else-if="showNewCategoryForm">
      <h3 class="mb-4">Add Category</h3>

      <b-form v-on:submit.prevent="createCategory">

        <b-form-group label="<strong>Family</strong>">
          <select class="form-control" id="exampleFormControlSelect1" v-model="newCategoryFamily">
            <option value="" selected disabled>Please select</option>
            <option v-for="family in families" :value="family.title">{{ family.title }}</option>
          </select>
        </b-form-group>

        <b-form-group label="<strong>Title</strong>">
          <b-form-input name="categoryTitle" v-model="newCategory.title" type="text" placeholder="Add Category Name" required></b-form-input>
        </b-form-group>

        <b-form-group label="<strong>Density</strong>">
          <b-form-input name="categoryDensity" v-model.number="newCategory.density" min="0.5" max="1.5" step="0.01" type="number"></b-form-input>
        </b-form-group>

        <b-form-group>
          <b-form-checkbox id="excluded_from_variance" v-model="newCategory.excluded_from_variance">
            <strong>Excluded From Variance</strong>
          </b-form-checkbox>
        </b-form-group>

        <b-form-group label="<strong>Partial</strong>">
          <b-form-radio-group id="partial" v-model="newCategory.partial" :selected-value="newCategory.partial" name="categoryPartial">
            <b-form-radio value="slider">Slider</b-form-radio>
            <b-form-radio value="scale">Scale</b-form-radio>
            <b-form-radio value="none">None</b-form-radio>
          </b-form-radio-group>
        </b-form-group>

        <b-form-group class="float-right">
          <b-button variant="danger" v-on:click="cancelCreateCategory">Cancel</b-button>
          <b-button type="submit" variant="primary">Add Category</b-button>
        </b-form-group>
      </b-form>
    </div>
    <div v-else>
      <div class="header">
        <h3 class="text-left d-inline-block">Custom Categories</h3>
        <b-button-group class="float-right" size="sm">
          <b-button class="m-1" variant="primary" v-on:click="openNewFamilyForm">Add New Family</b-button>
          <b-button class="m-1" variant="primary" v-on:click="openNewCategoryForm">Add New Category</b-button>
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
              <Icon name="trash-alt"></Icon>
            </a>
          </template>
        </b-table>
      </div>
    </div>
  </b-modal>
</template>

<script>
import axios from 'axios';
import 'vue-awesome/icons/trash-alt';
import Icon from 'vue-awesome/components/Icon'

export default {
  components: {
    Icon
  },
  data() {
    return {
      families: [],
      showEditCategoryForm: false,
      showNewFamilyForm: false,
      showNewCategoryForm: false,
      fields: [
        {
          key: 'title',
          label: 'Category Name'
        },
        'bottleCount',
        'delete'
      ],
      category: {},
      newFamilyTitle: '',
      newCategory: {
        title: '',
        density: 1,
        excluded_from_variance: false,
        partial: 'none'
      },
      newCategoryFamily: ''
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
      this.showEditCategoryForm = true;
      this.category = category;
    },
    openNewCategoryForm() {
      this.showNewCategoryForm = true;
    },
    openNewFamilyForm() {
      this.showNewFamilyForm = true;
    },
    updateCategory() {
      this.showEditCategoryForm = false;
      this.category = {};
    },
    cancelUpdate() {
      this.showEditCategoryForm = false;
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
    },
    createFamily() {
      const newFamily = {
        title: this.newFamilyTitle,
        id: Math.floor(Math.random() * Math.floor(999)),
        venue_id: Math.floor(Math.random() * Math.floor(999)),
        categories: []
      };

      this.families.unshift(newFamily);
      this.showNewFamilyForm = false;
    },
    cancelCreateFamily() {
      this.showNewFamilyForm = false;
    },
    createCategory() {
      // push new category to selected family
      this.families.find(family => family.title === this.newCategoryFamily).categories.push(this.newCategory);

      // reset new category object and family
      this.newCategoryFamily = '';
      this.newCategory = {
        title: '',
        density: 1,
        excluded_from_variance: false,
        partial: 'none'
      };

      // close form section
      this.showNewCategoryForm = false;
    },
    cancelCreateCategory() {
      this.showNewCategoryForm = false;
    },
  }
}
</script>

<style>
</style>
