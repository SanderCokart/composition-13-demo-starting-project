<template>
  <base-container v-if="user">
    <h2>{{ user.fullName }}: Projects</h2>
    <base-search v-if="hasProjects" @search="updateSearch" :search-term="enteredSearchTerm"></base-search>
    <ul v-if="hasProjects">
      <project-item v-for="prj in availableProjects" :key="prj.id" :title="prj.title"></project-item>
    </ul>
    <h3 v-else>No projects found.</h3>
  </base-container>
  <base-container v-else>
    <h3>No user selected.</h3>
  </base-container>
</template>

<script>
import ProjectItem from './ProjectItem.vue';
import {computed, ref, watch} from 'vue';

export default {
  components: {
    ProjectItem,
  },
  props: ['user'],
  setup(props) {
    const enteredSearchTerm = ref('');
    const activeSearchTerm = ref('');

    const availableProjects = computed(() => {
      if (activeSearchTerm.value) {
        return props.user.projects.filter(prj => prj.title.includes(activeSearchTerm));
      }
      return props.user.projects;
    });

    const hasProjects = computed(() => {
      return props.user.projects && availableProjects.value.length > 0;
    });

    const updateSearch = (val) => {
      enteredSearchTerm.value = val;
    };

    watch(enteredSearchTerm, (newVal) => {
      setTimeout(() => {
        if (newVal === enteredSearchTerm.value) {
          activeSearchTerm.value = newVal;
        }
      }, 300);
    });

    watch(props.user, () => {
      enteredSearchTerm.value = '';
    });

    return {availableProjects, hasProjects, enteredSearchTerm, activeSearchTerm, updateSearch};
  },
};

</script>

<style scoped>
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
</style>