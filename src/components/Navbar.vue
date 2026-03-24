<script setup>
import { ref } from 'vue'
import gsap from 'gsap'
import { ScrollToPlugin } from 'gsap/ScrollToPlugin'
import githubIcon from '../assets/images/github-icon.svg'
import linkedinIcon from '../assets/images/linkedin-icon.svg'
import emailIcon from '../assets/images/email-icon.svg'

gsap.registerPlugin(ScrollToPlugin)

defineProps({
  visible: { type: Boolean, default: true },
})

const mobileOpen = ref(false)

function scrollTo(e) {
  e.preventDefault()
  const target = e.currentTarget.getAttribute('href')
  if (target && target.startsWith('#')) {
    gsap.to(window, { scrollTo: target, duration: 1, ease: 'power2.inOut' })
    mobileOpen.value = false
  }
}
</script>

<template>
  <nav
    class="fixed top-0 left-0 right-0 z-50 bg-black/60 backdrop-blur-md transition-all duration-700"
    :class="visible ? 'opacity-100 translate-y-0' : 'opacity-0 -translate-y-full'"
  >
    <div class="max-w-[1540px] mx-auto flex items-center justify-between px-8 py-5">
      <!-- Logo -->
      <a href="#" class="font-family-roboto text-[30px] md:text-[40px] font-bold text-accent uppercase leading-none">
        Dan Morro
      </a>

      <!-- Desktop nav -->
      <div class="hidden lg:flex items-center gap-10">
        <a href="#about" @click="scrollTo" class="font-family-roboto text-[22px] font-bold text-white hover:text-accent transition-colors">About</a>
        <a href="#skills" @click="scrollTo" class="font-family-roboto text-[22px] font-bold text-white hover:text-accent transition-colors">Skills</a>
        <a href="#projects" @click="scrollTo" class="font-family-roboto text-[22px] font-bold text-white hover:text-accent transition-colors">Projects</a>


        <a href="https://github.com/danmorro59" target="_blank" class="flex items-center gap-2 font-family-roboto text-[22px] font-bold text-white hover:text-accent transition-colors">
          <img :src="githubIcon" alt="GitHub" class="w-[30px] h-[30px]" />
          Github
        </a>

        <!-- Contact button -->
        <a
          href="mailto:dmorro800@gmail.com"
          class="btn-gradient flex items-center gap-2 px-4 py-2 rounded-[8px] group transition-all"
        >
          <img :src="emailIcon" alt="Email" class="w-[30px] h-[30px]" />
          <span class="font-roboto text-[21px] font-bold text-white group-hover:text-[#70FF00] transition-colors">Contact me</span>
        </a>
      </div>

      <!-- Mobile toggle -->
      <button class="lg:hidden text-white" @click="mobileOpen = !mobileOpen">
        <svg xmlns="http://www.w3.org/2000/svg" class="w-8 h-8" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16M4 18h16" />
        </svg>
      </button>
    </div>

    <!-- Mobile menu -->
    <div v-if="mobileOpen" class="lg:hidden bg-black/90 border-t border-white/10 px-8 pb-6 flex flex-col gap-4">
      <a href="#about" class="font-roboto text-xl font-bold text-white py-2" @click="scrollTo">About</a>
      <a href="#skills" class="font-roboto text-xl font-bold text-white py-2" @click="scrollTo">Skills</a>
      <a href="#projects" class="font-roboto text-xl font-bold text-white py-2" @click="scrollTo">Projects</a>
      <a href="#" class="flex items-center gap-2 font-roboto text-xl font-bold text-white py-2">
        <img :src="linkedinIcon" alt="LinkedIn" class="w-6 h-6" /> Linkedin
      </a>
      <a href="#" class="flex items-center gap-2 font-roboto text-xl font-bold text-white py-2">
        <img :src="githubIcon" alt="GitHub" class="w-6 h-6" /> Github
      </a>
    </div>
  </nav>
</template>
