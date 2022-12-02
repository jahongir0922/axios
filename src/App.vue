<script setup>
import { RouterLink, RouterView } from "vue-router";
</script>

<template>
  <div id="app">
    <header>
      <nav>
        <RouterLink to="/">Home</RouterLink>
        <RouterLink to="/maqolalar">Maqola</RouterLink>
      </nav>
    </header>
    <RouterView />
  </div>
</template>

<style scoped lang="scss">
#app {
  width: 100%;
  height: 100vh;
  nav {
    a {
      padding: 15px;
      background-color: aquamarine;
      border-radius: 5px;
      &:hover {
        background-color: rgb(59, 181, 181);
      }
    }
    padding: 20px;
    width: 100%;
    font-size: 12px;
    text-align: center;
    margin-top: 2rem;
    display: flex;
    gap: 10px;
    justify-content: end;
  }
}
</style>
