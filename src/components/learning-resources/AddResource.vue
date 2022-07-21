<template>
<!-- we emitted an event listener from base-dialog and when that is called here it will trigger the confirmedError method -->
  <base-dialog v-if="inputIsInvalid" title="InvalidInput" @close='confirmedError'>
  <!-- we did not define the header template as we are using the default element by also passing in a prop from the title above -->
    <template #default>
      <p>Unfortunately , at least One input value is invalid</p>
      <p>Please check all input and make sure you enter correctly</p>
    </template>
    <template #actions>
      <base-button @click="confirmedError">Okay</base-button>
    </template>
  </base-dialog>



  <base-card>
    <form @submit.prevent="submitData">
      <div class="form-control">
        <label for="title">Title</label>
        <input id="title" name="title" type="text" ref="titleInput" />
      </div>
      <div class="form-control">
        <label for="description">Description</label>
        <textarea
          id="description"
          name="description"
          rows="3"
          ref="descInput"
        ></textarea>
      </div>
      <div class="form-control">
        <label for="link">Link</label>
        <input id="link" name="link" type="url" ref="linkInput" />
      </div>
      <div>
        <base-button type="submit">Add Resource</base-button>
      </div>
    </form>
  </base-card>
</template>

<script>
// we will be using ref to listen to the inputs and save them to a variable so that we can save the form input to be transfered to TheResource.vue and push them to the resources object
export default {
  inject: ['addResource'],
  data() {
    return {
      inputIsInvalid: false,
    };
  },
  methods: {
    submitData() {
      const enteredTitle = this.$refs.titleInput.value;
      const enteredDesc = this.$refs.descInput.value;
      const enteredLink = this.$refs.linkInput.value;

      if (
        enteredTitle.trim() === '' ||
        enteredDesc.trim() == '' ||
        enteredLink.trim() === ''
      ) {
        this.inputIsInvalid = true;
        return;
      }

      // we are calling the injected method from 'TheResource.vue' and using it here
      this.addResource(enteredTitle, enteredDesc, enteredLink);
    },
    confirmedError() {
      this.inputIsInvalid = false;
    },
  },
};
</script>



<style scoped>
label {
  font-weight: bold;
  display: block;
  margin-bottom: 0.5rem;
}

input,
textarea {
  display: block;
  width: 100%;
  font: inherit;
  padding: 0.15rem;
  border: 1px solid #ccc;
}

input:focus,
textarea:focus {
  outline: none;
  border-color: #3a0061;
  background-color: #f7ebff;
}

.form-control {
  margin: 1rem 0;
}
</style>