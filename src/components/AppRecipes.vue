<template>
  <div class="jumbotron vertical-center">
    <div class="container">
      <div class="row">
        <div class="col-sm-12">
          <h1>Recipes</h1>
          <hr />
          <br />
          <!-- Alert Message -->
          <b-alert v-if="showMessage" variant="success" show>{{
            message
          }}</b-alert>
          <!-- b-alert v-if="error" variant="danger" show>{{ error }}</b-alert-->

          <button
            type="button"
            class="btn btn-success btn-sm"
            v-b-modal.recipe-modal
          >
            Create Recipe
          </button>
          <br /><br />
          <table class="table table-hover">
            <thead>
              <tr>
                <th scope="col">Name</th>
                <th scope="col">Ingredients</th>
                <th scope="col">Steps</th>
                <th scope="col">Rating</th>
                <th scope="col">Favourite</th>
                <th scope="col">Actions</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="recipe in recipes" :key="recipe.id">
                <td>{{ recipe.name }}</td>
                <td>{{ recipe.ingredients }}</td>
                <td>{{ recipe.steps }}</td>
                <td>{{ recipe.rating }}</td>
                <td>{{ recipe.favourite }}</td>
                <td>
                  <div class="btn-group" role="group">
                    <button
                      type="button"
                      class="btn btn-info btn-sm"
                      v-b-modal.edit-recipe-modal
                      @click="editRecipe(recipe)"
                    >
                      Edit
                    </button>
                    <button
                      type="button"
                      class="btn btn-danger btn-sm"
                      @click="deleteRecipe(recipe)"
                    >
                      Delete
                    </button>
                  </div>
                </td>
              </tr>
            </tbody>
          </table>
          <footer class="text-center">
            Copyright &copy; All Rights Reserved.
          </footer>
        </div>
      </div>
      <b-modal
        ref="addRecipeModal"
        id="recipe-modal"
        title="Create a new recipe."
        hide-backdrop
        hide-footer
      >
        <b-form @submit="onSubmit" class="w-100">
          <b-form-group
            id="form-name-group"
            label="Recipe Name:"
            label-for="form-name-input"
          >
            <b-form-input
              id="form-name-input"
              type="text"
              v-model="createRecipeForm.name"
              placeholder="Recipe Name"
              required
            >
            </b-form-input>
          </b-form-group>
          <b-form-group
            id="form-ingredients-group"
            label="Ingredients:"
            label-for="form-ingredients-input"
          >
            <b-form-input
              id="form-ingredients-input"
              type="text"
              v-model="createRecipeForm.ingredients"
              placeholder="Ingredients"
              required
            >
            </b-form-input>
          </b-form-group>
          <b-form-group
            id="form-name-group"
            label="Steps:"
            label-for="form-steps-input"
          >
            <b-form-input
              id="form-steps-input"
              type="text"
              v-model="createRecipeForm.steps"
              placeholder="Steps"
              required
            >
            </b-form-input>
          </b-form-group>
          <b-button type="submit" variant="outline-info">Submit</b-button>
        </b-form>
      </b-modal>
      <!-- End of Modal for Create Recipe-->
      <!-- Start of Modal for Edit Recipe-->
      <b-modal
        ref="editRecipeModal"
        id="edit-recipe-modal"
        title="Edit the recipe."
        hide-backdrop
        hide-footer
      >
        <b-form @submit="onSubmitUpdate" class="w-100">
          <b-form-group
            id="form-edit-name-group"
            label="Recipe Name:"
            label-for="form-edit-name-input"
          >
            <b-form-input
              id="form-edit-name-input"
              type="text"
              v-model="editRecipeForm.name"
              placeholder="Recipe Name"
              required
            >
            </b-form-input>
          </b-form-group>
          <b-form-group
            id="form-edit-name-group"
            label="Ingredients:"
            label-for="form-edit-ingredients-input"
          >
            <b-form-input
              id="form-edit-ingredients-input"
              type="text"
              v-model="editRecipeForm.ingredients"
              placeholder="Ingredients"
              required
            >
            </b-form-input>
          </b-form-group>
          <b-form-group
            id="form-edit-steps-group"
            label="Steps"
            label-for="form-edit-steps-input"
          >
            <b-form-input
              id="form-edit-steps-input"
              type="text"
              v-model="editRecipeForm.steps"
              placeholder="Steps"
              required
            >
            </b-form-input>
          </b-form-group>
          <b-form-group
            id="form-rating-group"
            label="Rating:"
            label-for="form-rating-input"
          >
            <b-form-input
              id="form-rating-input"
              type="number"
              min=1
              max=5
              v-model.number="editRecipeForm.rating"
              placeholder="Rating"
              required
            >
            </b-form-input>
          </b-form-group>
          <b-form-group
            id="form-favourite-group"
            label="Favourite:"
            label-for="form-favourite-input"
          >
            <b-form-checkbox
              id="form-favourite-input"
              type="checkbox"
              v-model="editRecipeForm.favourite"
              required
            >
            </b-form-checkbox>
          </b-form-group>
          <b-button type="submit" variant="outline-info">Update</b-button>
        </b-form>
      </b-modal>
      <!-- End of Modal for Edit Recipe-->
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "AppRecipes",
  data() {
    return {
      recipes: [],
      createRecipeForm: {
        name: "",
        ingredients: "",
        steps: "",
      },
      editRecipeForm: {
        id: "",
        name: "",
      },
      showMessage: false,
      message: "",
    };
  },
  methods: {
    /***************************************************
     * RESTful requests
     ***************************************************/

    //GET function
    RESTgetRecipes() {
      const path = `${process.env.VUE_APP_ROOT_URL}/recipes`;
      axios
        .get(path)
        .then((response) => {
          this.recipes = response.data.recipes;
        })
        .catch((error) => {
          console.error(error);
        });
    },

    // POST function
    RESTcreateRecipe(payload) {
      const path = `${process.env.VUE_APP_ROOT_URL}/recipes`;
      axios
        .post(path, payload)
        .then((response) => {
          this.RESTgetRecipes();
          // For message alert
          this.message = "Recipe Created succesfully!";
          // To actually show the message
          this.showMessage = true;
          // To hide the message after 3 seconds
          setTimeout(() => {
            this.showMessage = false;
          }, 3000);
        })
        .catch((error) => {
          console.error(error);
          this.RESTgetRecipes();
        });
    },

    // Update function
    RESTupdateRecipe(payload, recipeId) {
      const path = `${process.env.VUE_APP_ROOT_URL}/recipes/${recipeId}`;
      axios
        .put(path, payload)
        .then((response) => {
          this.RESTgetRecipes();
          // For message alert
          this.message = "Recipe Updated succesfully!";
          // To actually show the message
          this.showMessage = true;
          // To hide the message after 3 seconds
          setTimeout(() => {
            this.showMessage = false;
          }, 3000);
        })
        .catch((error) => {
          console.error(error);
          this.RESTgetRecipes();
        });
    },

    // Delete recipe
    RESTdeleteRecipe(recipeId) {
      const path = `${process.env.VUE_APP_ROOT_URL}/recipes/${recipeId}`;
      axios
        .delete(path)
        .then((response) => {
          this.RESTgetRecipes();
          // For message alert
          this.message = "Recipe Deleted succesfully!";
          // To actually show the message
          this.showMessage = true;
          // To hide the message after 3 seconds
          setTimeout(() => {
            this.showMessage = false;
          }, 3000);
        })
        .catch((error) => {
          console.error(error);
          this.RESTgetRecipes();
        });
    },

    /***************************************************
     * FORM MANAGEMENT
     * *************************************************/

    // Initialize forms empty
    initForm() {
      this.createRecipeForm.name = "";
      this.createRecipeForm.ingredients = "";
      this.createRecipeForm.steps = "";
      this.editRecipeForm.id = "";
      this.editRecipeForm.name = "";
      this.editRecipeForm.ingredients= "";
      this.editRecipeForm.rating = "";
      this.editRecipeForm.favourite = "";
    },

    // Handle submit event for create recipe
    onSubmit(e) {
      e.preventDefault(); //prevent default form submit form the browser
      this.$refs.addRecipeModal.hide(); //hide the modal when submitted
      const payload = {
        name: this.createRecipeForm.name,
        ingredients: this.createRecipeForm.ingredients,
        steps: this.createRecipeForm.steps,
      };
      this.RESTcreateRecipe(payload);
      this.initForm();
    },

    // Handle submit event for edit recipe
    onSubmitUpdate(e) {
      e.preventDefault(); //prevent default form submit form the browser
      this.$refs.editRecipeModal.hide(); //hide the modal when submitted
      const payload = {
        name: this.editRecipeForm.name,
        ingredients: this.editRecipeForm.ingredients,
        steps: this.editRecipeForm.steps,
        rating: this.editRecipeForm.rating,
        favourite: this.editRecipeForm.favourite,
      };
      this.RESTupdateRecipe(payload, this.editRecipeForm.id);
      this.initForm();
    },

    // Handle edit button
    editRecipe(recipe) {
      this.editRecipeForm = recipe;
    },

    // Handle Delete button
    deleteRecipe(recipe) {
      this.RESTdeleteRecipe(recipe.id);
    },
  },

  /***************************************************
   * LIFECYClE HOOKS
   ***************************************************/
  created() {
    this.RESTgetRecipes();
  },
};
</script>
