<script setup>
	import projects from '../data/projects.json';
	import ProjectCard from './ProjectCard.vue';

</script>

<template>
	<!-- ***** Work ***** -->

	<section class="work" id="projects">
	  <div class="row">
	    <h2>My Projects</h2>
	    <div class="work__boxes">

	      <!-- Each div with the work__box class is a project. -->

	      <ProjectCard
	    		v-for="project in projects"
	    		:key="project.id"
	    		:project="project"
	    	 />

	    </div>
	  </div>
	</section>

</template>

<style scoped>
	
</style>