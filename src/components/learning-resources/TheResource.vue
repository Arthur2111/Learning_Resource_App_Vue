<template>
  <base-card>
    <!-- note how our event listenor is defined here and not in the slot component of base button -->
    <!-- this is due to a behaviour of vue the click listener fall through to the base button -->
    <base-button
      @click="setSelectedTab('stored-resource')"
      :mode="storedResButtonMode"
      >Stored Resources</base-button
    >
    <base-button
      @click="setSelectedTab('add-resource')"
      :mode="addResButtonMode"
      >Add Resource</base-button
    >
  </base-card>
  <!-- notice how if we used stored resources in the dynamic components, it actually requires a prop of resource -->
  <!-- hence we can use provide and inject to overcome this issue -->
  <!-- note that because this is a dynamic component we cannot create a custom method as we usually do to add resources we can use provide and inject -->

  <keep-alive>
    <component :is="selectedTab"></component>
  </keep-alive>
</template>

<script>
// stored resources require a prop to loop through data
import StoredResource from './StoredResource.vue';
import AddResource from './AddResource.vue';
import BaseButton from '../UI/BaseButton.vue';

export default {
  components: {
    StoredResource,
    BaseButton,
    AddResource,
  },
  data() {
    return {
      selectedTab: 'stored-resources',
      storedResource: [
        {
          id: 'official-guide',
          title: 'Official Guide',
          description: 'the official Vue.js documentation',
          link: 'https://vuejs.org',
        },
        {
          id: 'google',
          title: 'Google',
          description: 'We need to learn how to use google',
          link: 'https://google.com',
        },
      ],
    };
  },
  computed: {
    storedResButtonMode() {
      return this.selectedTab === 'stored-resources' ? null : 'flat';
    },
    addResButtonMode() {
      return this.selectedTab === 'add-resource' ? null : 'flat';
    },
  },
  //   we will provide the resources to the storedResources component as it requires a prop
  provide() {
    return {
      resources: this.storedResource,
      // we will use provide and inject... providing this method to our AddResource component
      addResource: this.addResource,
      deleteResource: this.removeResource,
    };
  },
  methods: {
    setSelectedTab(tab) {
      this.selectedTab = tab;
    },
    // this method will allow us to create a new resource once called
    // when we add a resource using push or unshift the array in memory will be changed as well hence any component using the using the resouces array will be notified
    addResource(title, description, url) {
      const newResource = {
        id: new Date().toISOString(),
        title: title,
        description: description,
        link: url,
      };
      //   we can call unshift to add the resource to the top
      this.storedResource.unshift(newResource);
      this.selectedTab = 'stored-resource';
    },
    removeResource(resId) {
      // this function will allow us to remove resources AND allow the resources array to pick up changes to be rendered
      // if we were to overide the current resources using .filter() method the resource will be deleted but not showed on the frontEnd.
      const resIndex = this.storedResource.findIndex((res) => res.id === resId);
      console.log(resIndex);
      // once getting the index we can
      this.storedResource.splice(resIndex, 1);
    },
  },
};
</script>