<template>
    <section class="projects-section" id="projects">
        <div class="container mx-auto">
            <h2 class="section-title">My Projects</h2>
            <div class="row g-4">
                <ProjectCard
                    v-for="project in projects"
                    :key="project.id"
                    :project="project"
                />
            </div>
        </div>
    </section>
</template>

<script setup>
    import ProjectCard from "./ProjectCard.vue";
    import projects from "../data/projects.json";
</script>