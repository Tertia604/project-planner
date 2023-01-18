<template>
  <div class="project" :class="{ complete: project.complete }">
    <div class="actions">
      <h3 @click="showDetails = !showDetails">
        {{ project.title }}
      </h3>
      <div class="icons">
        <router-link :to="{ name: 'editProject', params: { id: project.id } }">
          <span class="material-icons">edit</span>
        </router-link>
        <span @click="deleteProject" class="material-icons">delete</span>
        <span @click="toggleComplete" class="material-icons tick">done</span>
      </div>
    </div>
    <div v-if="showDetails" class="details">
      <p>
        {{ project.details }}
      </p>
    </div>
  </div>
</template>

<script>
import { ref } from "@vue/reactivity";
export default {
  props: ["project"],
  setup(props, context) {
    const showDetails = ref(false);
    const url = ref("http://localhost:3000/projects/" + props.project.id);

    const deleteProject = () => {
      fetch(url.value, {
        method: "DELETE",
      })
        .then(() => {
          context.emit("delete", props.project.id);
        })
        .catch((err) => console.log(err.message));
    };

    const toggleComplete = () => {
      fetch(url.value, {
        method: "PATCH",
        headers: { "Content-type": "application/json" },
        body: JSON.stringify({
          complete: !props.project.complete,
        }),
      })
        .then(() => {
          context.emit("complete", props.project.id);
        })
        .catch((err) => console.log(err.message));
    };

    return {
      showDetails,
      deleteProject,
      toggleComplete,
    };
  },
};
</script>

<style lang="scss">
.project {
  margin: 20px auto;
  background: white;
  padding: 10px 20px;
  border-radius: 5px;
  box-shadow: 1px 2px 3px rgba(0, 0, 0, 0.5);
  border-left: 4px solid #e90074;
  &.complete {
    border-left: 4px solid #00c389;
    .tick {
      color: #00c389;
    }
  }
}
h3 {
  cursor: pointer;
}

.actions {
  display: flex;
  justify-content: space-between;
  align-items: center;
  .material-icons {
    font-size: 24px;
    margin-left: 10px;
    color: #bbb;
    cursor: pointer;
    &:hover {
      color: #777;
    }
  }
}
</style>