<template>
  <div class="home">
    <FilterNav @filterChange="filterChangeEvent" :current="current" />

    <div v-if="projects.length">
      <div v-for="project in filteredProjects" :key="project.id">
        <SingleProject
          :project="project"
          @delete="handleDelete"
          @complete="handleComplete"
        />
      </div>
    </div>
  </div>
</template>

<script>
import { computed, onMounted, ref } from "@vue/runtime-core";
import SingleProject from "@/components/SingleProject.vue";
import FilterNav from "@/components/FilterNav.vue";

export default {
  name: "HomeView",
  components: {
    SingleProject,
    FilterNav,
  },
  setup() {
    const projects = ref([]);
    const current = ref("all");

    const handleDelete = (id) => {
      projects.value = projects.value.filter((project) => {
        return project.id !== id;
      });
    };

    const handleComplete = (id) => {
      let p = projects.value.find((project) => {
        return project.id === id;
      });
      p.complete = !p.complete;
    };

    const filterChangeEvent = (e) => {
      current.value = e;
    };

    const filteredProjects = computed(() => {
      if (current.value === "completed") {
        return projects.value.filter((project) => project.complete);
      }
      if (current.value === "ongoing") {
        return projects.value.filter((project) => !project.complete);
      }
      return projects.value;
    });

    onMounted(() => {
      fetch("http://localhost:3000/projects")
        .then((res) => res.json())
        .then((data) => (projects.value = data))
        .catch((err) => console.log(err.message));
    });
    return {
      projects,
      handleDelete,
      handleComplete,
      current,
      filterChangeEvent,
      filteredProjects,
    };
  },
};
</script>
