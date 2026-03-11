<script setup>
import { onMounted, ref } from 'vue'
import gsap from 'gsap'
import heroAvatar from '../assets/images/meandCudi.png'

const orbitContainer = ref(null)

// Dynamically import all orbit icons
const orbitIconModules = import.meta.glob('../assets/images/orbit-icons/*', { eager: true })
const techIcons = Object.entries(orbitIconModules).map(([path, module]) => ({
  name: path.split('/').pop().replace(/\.(png|jpg|jpeg|svg)$/, ''),
  src: module.default
}))

onMounted(() => {
  const icons = orbitContainer.value.querySelectorAll('.orbit-icon')
  const radiusX = 300 // Horizontal orbit radius
  const radiusY = 150 // Vertical orbit radius (smaller for elliptical orbit)

  icons.forEach((icon, index) => {
    const startAngle = (360 / icons.length) * index

    // Create orbital animation with depth (z-axis simulation)
    gsap.to(icon, {
      duration: 20,
      repeat: -1,
      ease: 'none',
      onUpdate: function() {
        const progress = this.progress()
        const angle = startAngle + (progress * 360)
        const radian = (angle * Math.PI) / 180

        // Calculate x, y position for elliptical orbit
        const x = Math.cos(radian) * radiusX
        const y = Math.sin(radian) * radiusY

        // Calculate z-depth (negative z means behind the image)
        const z = Math.sin(radian)

        // Scale and opacity based on depth
        const scale = z > 0 ? 1 + (z * 0.3) : 0.7 + (z * 0.3) // Larger when in front
        const opacity = z > 0 ? 1 : 0 // Hidden when behind

        gsap.set(icon, {
          x,
          y,
          scale,
          opacity,
          zIndex: z > 0 ? 30 : 5 // In front or behind the image
        })
      }
    })
  })
})
</script>

<template>
  <section class="pt-40 md:pt-52 pb-20 px-8">
    <div class="max-w-[1540px] mx-auto flex flex-col lg:flex-row items-center justify-between gap-12">
      <!-- Left content -->
      <div class="flex-1">
        <p class="font-roboto text-3xl md:text-[39px] font-bold text-gray-5 mb-4">Hello, i am</p>
        <h1 class="font-roboto text-5xl md:text-[78px] font-bold text-accent leading-[1.17] tracking-[0.1em] mb-8">
          &lt; Dan<br />Morro /&gt;
        </h1>
        <p class="font-roboto text-3xl md:text-[57px] font-bold text-gray-5 mb-12">Fullstack Developer</p>

        <!-- Stats -->
        <div class="flex items-start gap-12">
          <div class="flex items-start gap-4">
            <span class="font-roboto text-6xl md:text-[86px] font-bold text-gray-2 leading-[1.17]">3</span>
            <span class="font-roboto text-lg md:text-[27px] font-normal text-gray-3 uppercase leading-[1.17] mt-3">years of<br />experience</span>
          </div>
          <div class="flex items-start gap-4">
            <span class="font-roboto text-6xl md:text-[86px] font-bold text-gray-2 leading-[1.17]">13</span>
            <span class="font-roboto text-lg md:text-[25px] font-medium text-gray-3 uppercase leading-[1.17] mt-3">Projects Completed<br />around the world</span>
          </div>
        </div>
      </div>

      <!-- Right avatar with orbiting icons -->
      <div class="flex-shrink-0 relative" ref="orbitContainer">
        <img :src="heroAvatar" alt="Developer avatar" class="w-[350px] md:w-[500px] lg:w-[569px] h-[1000px] object-cover object-top relative z-20" />

        <!-- Orbiting tech icons -->
        <div
          v-for="(tech, index) in techIcons"
          :key="tech.name"
          class="orbit-icon absolute top-1/2 left-1/2 w-16 h-16 flex items-center justify-center bg-gray-800/80 backdrop-blur-sm rounded-full border-2 border-accent/30 overflow-hidden z-20"
          :style="{ transform: 'translate(-50%, -50%)' }"
        >
          <img :src="tech.src" :alt="tech.name" class="w-full h-full object-cover" />
        </div>
      </div>
    </div>
  </section>
</template>
