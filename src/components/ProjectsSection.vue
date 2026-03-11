<script setup>
import projectThumb from '../assets/images/project-thumb.png'
import openIcon from '../assets/images/open-in-new-icon.svg'

const projects = [
  { name: 'Project 1', image: projectThumb },
  { name: 'Project 2', image: projectThumb },
  { name: 'Project 3', image: projectThumb },
  { name: 'Project 4', image: projectThumb },
]
</script>

<template>
  <section id="projects" class="py-20 px-8">
    <div class="max-w-[1540px] mx-auto">
      <h2 class="font-roboto text-3xl md:text-[49px] font-bold text-gray-5 mb-16">projects( )</h2>

      <!-- Projects horizontal scroll -->
      <div class="flex gap-10 overflow-x-auto pb-6 snap-x snap-mandatory scrollbar-hide">
        <div
          v-for="(project, i) in projects"
          :key="i"
          class="relative flex-shrink-0 w-[360px] md:w-[486px] h-[280px] md:h-[361px] rounded-[14px] overflow-hidden border-2 border-white shadow-[0_4px_10px_5px_rgba(0,0,0,0.25)] group cursor-pointer snap-center"
        >
          <!-- Image -->
          <img
            :src="project.image"
            :alt="project.name"
            class="w-full h-full object-cover"
          />

          <!-- Title bar at bottom -->
          <div class="absolute bottom-0 left-0 right-0 h-[79px] bg-card-overlay rounded-b-[14px] flex items-center px-6">
            <h3 class="font-roboto text-2xl md:text-[35px] font-bold text-white">{{ project.name }}</h3>
          </div>

          <!-- Hover overlay -->
          <div class="absolute inset-0 bg-[rgba(37,37,37,0.39)] border-[3px] border-accent rounded-[14px] opacity-0 group-hover:opacity-100 transition-opacity flex items-center justify-center">
            <img :src="openIcon" alt="Open" class="w-[61px] h-[61px]" />
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<style scoped>
.scrollbar-hide::-webkit-scrollbar {
  display: none;
}
.scrollbar-hide {
  -ms-overflow-style: none;
  scrollbar-width: none;
}
</style>
