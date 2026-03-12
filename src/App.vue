<script setup>
import { ref } from 'vue'
import Navbar from './components/Navbar.vue'
import GooeyTransition from './components/GooeyTransition.vue'
import HeroSection from './components/HeroSection.vue'
import AboutSection from './components/AboutSection.vue'
import SkillsSection from './components/SkillsSection.vue'
import ProjectsSection from './components/ProjectsSection.vue'
import MyJourneySection from './components/MyJourneySection.vue'

const gooeyTrigger = ref(null)
const heroRef = ref(null)
const navVisible = ref(false)
const goopDone = ref(false)

function onGooeyProgress(progress) {
  navVisible.value = progress > 0.7
  if (progress >= 0.95 && !goopDone.value) {
    goopDone.value = true
  }
}
</script>

<template>
  <!-- Gooey landing overlay + scroll trigger -->
  <div ref="gooeyTrigger" class="h-[200vh] relative">
    <GooeyTransition v-if="gooeyTrigger" :triggerEl="gooeyTrigger" :snapTarget="heroRef" @progress="onGooeyProgress" />
  </div>

  <!-- Site content -->
  <Navbar :visible="navVisible" />
  <div ref="heroRef">
    <HeroSection :animate="goopDone" />
  </div>
  <AboutSection />
  <SkillsSection />
  <ProjectsSection />
  <MyJourneySection />
</template>
