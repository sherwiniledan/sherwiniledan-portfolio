<script setup>
  import Navbar from './components/Navbar.vue';
  import Home from './components/Home.vue';
  import Projects from './components/Projects.vue';
  import Tools from './components/Tools.vue';
  import Contacts from './components/Contacts.vue';
  import Footer from './components/Footer.vue';
</script>
  
<template>
  <Navbar />
    <div class="container-fluid bg-dark text-white">
      <Home />
    </div>
    <div class="container-fluid">
      <Projects />
      <Tools />
      <Contacts />
    </div>
  <Footer />
</template>

<style scoped></style>
