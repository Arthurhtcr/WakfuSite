<script setup>
import { ref,computed } from 'vue';
import donjons from './donjons.vue';
import tasks from './tasks.vue';
import metier from './xpmetier.vue';
import sublimation from './sublimation.vue';

const routes = {
  '/donjons': donjons,
  '/tasks':tasks,
  '/metier':metier,
  '/sublimation':sublimation
}

const currentPath = ref(window.location.hash)

window.addEventListener('hashchange', () => {
  currentPath.value = window.location.hash
})

const currentView = computed(() => {
  return routes[currentPath.value.slice(1) || '/']
})


</script>

<template>
    <div class="Links">
  <a href="#/donjons">donjons</a> 
  <a href="#/tasks">tasks</a>
  <a href="#/metier">metier</a>
  <a href="#/sublimation">sublimation</a>

  </div>
  <div class="content"> <component :is="currentView" /></div>

  
</template>

<style scoped>
html{
    background-color: rgb(41, 41, 41);
}
.links{
  text-decoration: none;
  color: hsla(160, 100%, 37%, 1);
  transition: 0.4s;
  padding: 3px;
  justify-content: center;
  text-align: center;
}
</style>