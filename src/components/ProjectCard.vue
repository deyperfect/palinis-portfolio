<template>
    <div class="col-10 col-md-6 col-lg-3 mx-auto">
        <a class="project-link" :href="project.url" target="_blank">
            <div class="project-card">
                <div class="img-holder">
                    <img :src="project.image" :alt="project.title" class="project-image">
                </div>
                <div class="project-content">
                    <h3 class="project-title">{{ project.title }}</h3>
                    <p class="project-description">{{ project.description }}</p>
                </div>
            </div>
        </a>
    </div>
</template>

<script setup>
    defineProps({
        project: Object
    })
</script>