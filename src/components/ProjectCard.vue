<script setup>

	import {defineProps} from 'vue';

	defineProps({
		project:Object
	})
	
</script>

<template>

	<div class="work__box">
	  <div class="work__text">
	    <h3>{{project.title}}</h3>
	    <p>
	      {{project.description}}
	    </p>

	    <div class="work__links">
	      <a href="https://github.com/" target="_blank" class="link__text">
	        Visit Site <span>&rarr;</span>
	      </a>
	      <a href="https://github.com/" title="View Source Code" target="_blank">
	        <img src="/images/github.svg" class="work__code" alt="GitHub">
	      </a>
	    </div>
	  </div>
	  <div class="work__image-box">
	    <img :src="project.image" class="work__image" alt="Project 1" />
	  </div>
	</div>

</template>

<style scoped>
	
</style>