<template>
  <div id="maqolalar">
    <h1>This is maqola page</h1>
    <h1 v-if="xatolik">{{ xatolik }}</h1>
    <RouterLink
      v-else
      :to="{ name: 'maqola', params: { id: item.id } }"
      v-for="(item, index) in royxat"
      :key="index"
      >{{ item.nomi }}</RouterLink
    >
  </div>
</template>
<script setup>
import { ref } from "vue";
import axios from "axios";
const royxat = ref([]);
const xatolik = ref("");
axios
  .get("http://localhost:3000/maqolalar")
  .then((res) => {
    royxat.value = [...res.data];
  })
  .catch((err) => {
    xatolik.value = err.message;
  });
</script>
<style scoped lang="scss">
#maqolalar {
  width: 100%;
  padding: 20px;
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  gap: 10px;
  a {
    background-color: aquamarine;
    width: 200px;
    &:hover {
      background-color: rgb(59, 181, 181);
    }
  }
}
</style>
