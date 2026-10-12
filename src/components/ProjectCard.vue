<template>
  <div class="card w-100 h-100">
    <img :src="project.image" class="card-img-top card-img" :alt="project.title">
    <div class="card-body">
      <h5 class="card-title">{{ project.title }}</h5>
      <p class="card-text">
        {{ project.description }}
      </p>
    </div>
  </div>
</template>

<script setup>	
defineProps({
  project: Object
});
</script>