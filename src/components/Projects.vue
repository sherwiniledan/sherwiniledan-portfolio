<template>
  <!-- Start of Projects Section -->
  <section id="projects" class="container text-center mt-3">
    <h3>My Projects</h3>

    <!-- Bootstrap 5 auto-wraps into 3 columns per row on medium+ screens -->
    <div class="row row-cols-1 row-cols-md-3 g-4 my-4">
      <div v-for="project in projects" :key="project.id" class="col">
        <ProjectCard :project="project" />
      </div>
    </div>
  </section>
</template>

<script setup>
import ProjectCard from './ProjectCard.vue';
import projects from '../data/projects.json';
</script>