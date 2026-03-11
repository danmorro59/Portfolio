<script setup>
import { ref } from 'vue'
import githubIcon from '../assets/images/github-icon.svg'
import linkedinIcon from '../assets/images/linkedin-icon.svg'
import emailIcon from '../assets/images/email-icon.svg'

const mobileOpen = ref(false)
</script>

<template>
  <nav class="fixed top-0 left-0 right-0 z-50 bg-black/60 backdrop-blur-md">
    <div class="max-w-[1540px] mx-auto flex items-center justify-between px-8 py-5">
      <!-- Logo -->
      <a href="#" class="font-family-roboto text-[30px] md:text-[40px] font-bold text-accent uppercase leading-none">
        Dan Morro
      </a>

      <!-- Desktop nav -->
      <div class="hidden lg:flex items-center gap-10">
        <a href="#about" class="font-family-roboto text-[22px] font-bold text-white hover:text-accent transition-colors">About</a>
        <a href="#skills" class="font-family-roboto text-[22px] font-bold text-white hover:text-accent transition-colors">Skills</a>
        <a href="#projects" class="font-family-roboto text-[22px] font-bold text-white hover:text-accent transition-colors">Projects</a>

        <a href="#" class="flex items-center gap-2 font-family-roboto text-[22px] font-bold text-white hover:text-accent transition-colors">
          <img :src="linkedinIcon" alt="LinkedIn" class="w-[30px] h-[30px]" />
          Linkedin
        </a>
        <a href="#" class="flex items-center gap-2 font-family-roboto text-[22px] font-bold text-white hover:text-accent transition-colors">
          <img :src="githubIcon" alt="GitHub" class="w-[30px] h-[30px]" />
          Github
        </a>

        <!-- Contact button -->
        <a
          href="#"
          class="flex items-center gap-3 px-6 py-3 rounded-[13px] border-[3px] border-transparent bg-clip-padding
                 [border-image:linear-gradient(180deg,rgba(112,255,0,0.55),#fff)_1]
                 hover:border-[#70FF00] group transition-all"
          style="border-image: linear-gradient(180deg, rgba(112,255,0,0.55) 0%, #ffffff 100%) 1;"
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
      <a href="#about" class="font-roboto text-xl font-bold text-white py-2" @click="mobileOpen = false">About</a>
      <a href="#skills" class="font-roboto text-xl font-bold text-white py-2" @click="mobileOpen = false">Skills</a>
      <a href="#projects" class="font-roboto text-xl font-bold text-white py-2" @click="mobileOpen = false">Projects</a>
      <a href="#" class="flex items-center gap-2 font-roboto text-xl font-bold text-white py-2">
        <img :src="linkedinIcon" alt="LinkedIn" class="w-6 h-6" /> Linkedin
      </a>
      <a href="#" class="flex items-center gap-2 font-roboto text-xl font-bold text-white py-2">
        <img :src="githubIcon" alt="GitHub" class="w-6 h-6" /> Github
      </a>
    </div>
  </nav>
</template>
