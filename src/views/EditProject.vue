<template>
  <form @submit.prevent="handleSubmit">
    <label>Title:</label>
    <input type="text" required v-model="title" />
    <label>Details:</label>
    <textarea required v-model="details"></textarea>
    <button>Update Project</button>
  </form>
</template>

<script>
import { ref } from "@vue/reactivity";
import { onMounted } from "@vue/runtime-core";
import { useRouter } from "vue-router";
export default {
  props: ["id"],
  setup(props) {
    const router = useRouter();
    const title = ref("");
    const details = ref("");
    const url = ref("http://localhost:3000/projects/" + props.id);

    const handleSubmit = () => {
      fetch(url.value, {
        method: "PATCH",
        headers: { "Content-type": "application/json" },
        body: JSON.stringify({
          title: title.value,
          details: details.value,
        }),
      })
        .then(() => {
          router.push({ name: "home" });
        })
        .catch((err) => console.log(err.message));
    };

    onMounted(() => {
      fetch(url.value)
        .then((res) => res.json())
        .then((data) => {
          title.value = data.title;
          details.value = data.details;
        })
        .catch((err) => console.log(err.message));
    });

    return {
      title,
      details,
      handleSubmit,
    };
  },
};
</script>

<style>
</style>