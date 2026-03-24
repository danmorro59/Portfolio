<script setup>
import { watch, ref } from 'vue'
import gsap from 'gsap'
import heroAvatar from '../assets/images/meandCudi.png'

const props = defineProps({
  animate: { type: Boolean, default: false },
})

const heroContent = ref(null)
let hasAnimated = false

const scrambleChars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789'

function scrambleIn(element, finalText, { duration = 2.5, stagger = 0.06 } = {}) {
  return new Promise((resolve) => {
    // Split final text into characters, preserving spaces
    const chars = finalText.split('')
    const totalChars = chars.length

    // Build spans for each character
    element.innerHTML = chars
      .map((ch, i) =>
        ch === ' '
          ? '<span class="scramble-char">&nbsp;</span>'
          : `<span class="scramble-char" data-final="${ch}" data-index="${i}" style="opacity:0">${ch}</span>`
      )
      .join('')

    element.style.opacity = '1'

    const spans = element.querySelectorAll('.scramble-char[data-final]')
    const tl = gsap.timeline({ onComplete: resolve })

    spans.forEach((span, i) => {
      const final = span.dataset.final
      const delay = i * stagger

      // Each character: scramble for a bit, then lock in
      const charTl = gsap.timeline()

      // Fade the character in
      charTl.to(span, { opacity: 1, duration: 0.05 })

      // Scramble phase: cycle through random chars
      const scrambleDuration = 0.8
      const interval = 0.07 // how often the random char changes
      const steps = Math.floor(scrambleDuration / interval)

      for (let s = 0; s < steps; s++) {
        charTl.call(
          () => {
            span.textContent = scrambleChars[Math.floor(Math.random() * scrambleChars.length)]
            span.classList.add('scrambling')
            span.classList.remove('revealed')
          },
          null,
          `<+${interval}`
        )
      }

      // Lock in final character
      charTl.call(() => {
        span.textContent = final
        span.classList.remove('scrambling')
        span.classList.add('revealed')
      })

      tl.add(charTl, delay)
    })
  })
}

watch(() => props.animate, async (ready) => {
  if (!ready || hasAnimated) return
  hasAnimated = true

  const nameEl = heroContent.value.querySelector('.hero-name')
  const greetingEl = heroContent.value.querySelector('.hero-greeting')
  const subtitleEl = heroContent.value.querySelector('.hero-subtitle')
  const stat1 = heroContent.value.querySelector('.hero-stat-1')
  const stat2 = heroContent.value.querySelector('.hero-stat-2')

  // Show greeting with a simple fade
  gsap.to(greetingEl, { opacity: 1, duration: 0.6, ease: 'power2.out' })

  // Phase 1: "< Dan Morro />" scrambles in
  await scrambleIn(nameEl, nameEl.dataset.text, { duration: 2.5, stagger: 0.08 })

  // Fade in subtitle
  gsap.to(subtitleEl, { opacity: 1, duration: 0.6, ease: 'power2.out' })

  // Phase 2: both stats scramble in together (with a slight offset)
  await Promise.all([
    scrambleIn(stat1, stat1.dataset.text, { duration: 2, stagger: 0.05 }),
    new Promise((resolve) =>
      setTimeout(async () => {
        await scrambleIn(stat2, stat2.dataset.text, { duration: 2, stagger: 0.05 })
        resolve()
      }, 300)
    ),
  ])
})
</script>

<template>
  <section class="pt-40 md:pt-52 pb-20 px-8">
    <div class="max-w-[1540px] mx-auto flex flex-col lg:flex-row items-center justify-between gap-12">
      <!-- Left content -->
      <div class="flex-1" ref="heroContent">
        <p class="hero-greeting font-roboto text-3xl md:text-[39px] font-bold text-gray-5 mb-4 opacity-0">Hello, I am</p>
        <h1
          class="hero-name font-roboto text-5xl md:text-[78px] font-bold text-accent leading-[1.17] tracking-[0.1em] mb-8 opacity-0"
          data-text="< Dan Morro />"
        >&lt; Dan Morro /&gt;</h1>
        <p class="hero-subtitle font-roboto text-3xl md:text-[57px] font-bold text-gray-5 mb-12 opacity-0">Fullstack Developer</p>

        <!-- Stats -->
        <div class="flex items-start gap-12">
          <div class="flex items-start gap-4">
            <span
              class="hero-stat-1 font-roboto text-6xl md:text-[86px] font-bold text-gray-2 leading-[1.17] opacity-0"
              data-text="3"
            >3</span>
            <span class="font-roboto text-lg md:text-[27px] font-normal text-gray-3 uppercase leading-[1.17] mt-3">years of<br />experience</span>
          </div>
          <div class="flex items-start gap-4">
            <span
              class="hero-stat-2 font-roboto text-6xl md:text-[86px] font-bold text-gray-2 leading-[1.17] opacity-0"
              data-text="13"
            >13</span>
            <span class="font-roboto text-lg md:text-[25px] font-medium text-gray-3 uppercase leading-[1.17] mt-3">Projects Completed<br />around the world</span>
          </div>
        </div>
      </div>

      <!-- Right avatar -->
      <div class="hero-avatar-wrap flex-shrink-0 relative">
        <img :src="heroAvatar" alt="Developer avatar" class="w-[350px] md:w-[500px] lg:w-[569px] max-h-[600px] object-contain relative z-10" />
        <div class="hero-platform"></div>
        <div class="hero-platform-glow"></div>
      </div>
    </div>
  </section>
</template>

<style scoped>
.scramble-char {
  display: inline-block;
}

.scrambling {
  color: #4a5568;
}

.revealed {
  color: inherit;
}

.hero-avatar-wrap {
  position: relative;
  display: flex;
  flex-direction: column;
  align-items: center;
}

/* Elliptical platform under the feet */
.hero-platform {
  position: absolute;
  bottom: -10px;
  left: 50%;
  transform: translateX(-50%);
  width: 85%;
  height: 50px;
  border-radius: 50%;
  background: radial-gradient(
    ellipse at center,
    rgba(112, 255, 0, 0.25) 0%,
    rgba(112, 255, 0, 0.1) 40%,
    transparent 70%
  );
  z-index: 5;
}

/* Soft glow ring around the platform */
.hero-platform-glow {
  position: absolute;
  bottom: -20px;
  left: 50%;
  transform: translateX(-50%);
  width: 100%;
  height: 60px;
  border-radius: 50%;
  background: radial-gradient(
    ellipse at center,
    rgba(112, 255, 0, 0.12) 0%,
    rgba(112, 255, 0, 0.04) 50%,
    transparent 75%
  );
  filter: blur(8px);
  z-index: 4;
}
</style>
