<script setup>
import jsIcon from '../assets/images/skill-javascript.svg'
import tsIcon from '../assets/images/skill-typescript.svg'
import htmlIcon from '../assets/images/skill-html5.svg'
import cssIcon from '../assets/images/skill-css3.svg'
import reactIcon from '../assets/images/skill-react.svg'
import reduxIcon from '../assets/images/skill-redux.svg'
import nodeIcon from '../assets/images/skill-nodejs.svg'
import nextIcon from '../assets/images/skill-nextjs.svg'
import linuxIcon from '../assets/images/skill-linux.svg'
import pythonIcon from '../assets/images/skill-python.svg'
import rubyIcon from '../assets/images/skill-ruby.svg'
import railsIcon from '../assets/images/skill-rails.svg'

const row1 = [
  { name: 'JavaScript', icon: jsIcon },
  { name: 'TypeScript', icon: tsIcon },
  { name: 'HTML5', icon: htmlIcon },
  { name: 'CSS3', icon: cssIcon },
  { name: 'React', icon: reactIcon },
  { name: 'Redux', icon: reduxIcon },
  { name: 'Node.js', icon: nodeIcon },
  { name: 'Next.js', icon: nextIcon },
]

const row2 = [
  { name: 'Linux', icon: linuxIcon },
  { name: 'Python', icon: pythonIcon },
  { name: 'Ruby', icon: rubyIcon },
  { name: 'Rails', icon: railsIcon },
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
