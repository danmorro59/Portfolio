<script setup>
import vueIcon from '../assets/images/skill-vue.svg'
import jsIcon from '../assets/images/skill-javascript.svg'
import tailwindIcon from '../assets/images/skill-tailwind.svg'
import cssIcon from '../assets/images/skill-css3.svg'
import phpIcon from '../assets/images/skill-php.svg'
import laravelIcon from '../assets/images/skill-laravel.svg'
import mysqlIcon from '../assets/images/skill-mysql.svg'
import dockerIcon from '../assets/images/skill-docker.svg'
import awsIcon from '../assets/images/skill-aws.svg'
import htmlIcon from '../assets/images/skill-html5.svg'
import claudeIcon from '../assets/images/skill-claude.svg'

// Frontend
const row1 = [
  { name: 'HTML', icon: htmlIcon },
  { name: 'CSS', icon: cssIcon },
  { name: 'JavaScript', icon: jsIcon },
  { name: 'Vue', icon: vueIcon },
  { name: 'Tailwind', icon: tailwindIcon },
]

// Backend & Infrastructure
const row2 = [
  { name: 'PHP', icon: phpIcon },
  { name: 'Laravel', icon: laravelIcon },
  { name: 'MySQL', icon: mysqlIcon },
  { name: 'Docker', icon: dockerIcon },
  { name: 'AWS', icon: awsIcon },
  { name: 'Claude', icon: claudeIcon },
]
</script>

<template>
  <section id="skills" class="bg-dark-panel py-20 px-8">
    <div class="max-w-[1540px] mx-auto">
      <h2 class="font-roboto text-3xl md:text-[49px] font-bold text-gray-5 mb-16">skills( )</h2>

      <!-- Row 1 -->
      <div class="flex flex-wrap justify-center lg:justify-start gap-8 md:gap-14 mb-16">
        <div
          v-for="skill in row1"
          :key="skill.name"
          class="flex flex-col items-center gap-3 hover:scale-110 transition-transform"
        >
          <img :src="skill.icon" :alt="skill.name" class="w-[80px] h-[80px] md:w-[128px] md:h-[128px]" />
        </div>
      </div>

      <!-- Row 2 -->
      <div class="flex flex-wrap justify-center lg:justify-start gap-8 md:gap-14">
        <div
          v-for="skill in row2"
          :key="skill.name"
          class="flex flex-col items-center gap-3 hover:scale-110 transition-transform"
        >
          <img :src="skill.icon" :alt="skill.name" class="w-[80px] h-[80px] md:w-[128px] md:h-[128px]" />
        </div>
      </div>
    </div>
  </section>
</template>
