<script setup>
import xmlIcon from "../assets/images/xml-icon.svg";
import codeBracesIcon from "../assets/images/code-braces-icon.svg";
</script>

<template>
  <section id="about" class="py-20 px-8">
    <div
      class="max-w-[1540px] mx-auto flex flex-col lg:flex-row gap-16 items-start"
    >
      <!-- Left: About text -->
      <div class="flex-1">
        <h2
          class="font-roboto text-3xl md:text-[49px] font-bold text-gray-5 mb-8"
        >
          aboutMe( )
        </h2>
        <p
          class="font-roboto text-xl md:text-[29px] font-normal text-gray-6 leading-[1.4] max-w-[763px]"
        >
          I began my journey in the United States Marine Corps at 17, where I
          taught myself how to code and built my first application to help
          Marines report barracks issues. That experience sparked a passion for
          solving real problems with software, eventually leading me to a career
          as a full-stack developer building applications used by millions each
          month.
        </p>
      </div>

      <!-- Right: Role cards -->
      <div class="flex flex-col gap-3 w-full lg:w-auto">
        <!-- Fullstack Developer Card -->
        <a
          href="#projects"
          class="about-card group block bg-card-bg rounded-[6px] shadow-[0_4px_10px_5px_rgba(0,0,0,0.25)] p-6 md:p-8 w-full lg:w-[569px] border border-transparent transition-all duration-300 hover:border-accent/40 hover:shadow-[0_0_25px_rgba(112,255,0,0.15)]"
        >
          <div class="flex items-start justify-between mb-4">
            <h3
              class="font-courier text-2xl md:text-[33px] font-bold text-white"
            >
              Fullstack Developer
            </h3>
            <img :src="xmlIcon" alt="Code" class="w-[50px] h-[50px]" />
          </div>
          <span
            class="font-courier text-xl md:text-[25px] font-normal text-accent"
            >Projects</span
          >
        </a>

        <!-- iostudio Card -->
        <a
          href="https://iostudio.com/"
          target="_blank"
          class="about-card group block bg-card-bg rounded-[6px] shadow-[0_4px_10px_5px_rgba(0,0,0,0.25)] p-6 md:p-8 w-full lg:w-[569px] border border-transparent transition-all duration-300 hover:border-accent/40 hover:shadow-[0_0_25px_rgba(112,255,0,0.15)]"
        >
          <div class="flex items-start justify-between mb-4">
            <h3
              class="font-courier text-2xl md:text-[35px] font-bold text-white"
            >
              iostudio
            </h3>
            <img
              :src="codeBracesIcon"
              alt="Code braces"
              class="w-[50px] h-[50px]"
            />
          </div>
          <span
            class="font-courier text-xl md:text-[25px] font-normal text-accent"
            >Mid-level</span
          >
        </a>
      </div>
    </div>
  </section>
</template>

<style scoped>
.about-card {
  position: relative;
  overflow: hidden;
  cursor: pointer;
}

.about-card::before {
  content: "";
  position: absolute;
  inset: 0;
  background: linear-gradient(
    105deg,
    transparent 40%,
    rgba(112, 255, 0, 0.06) 50%,
    transparent 60%
  );
  transform: translateX(-100%);
  transition: none;
}

.about-card:hover::before {
  animation: card-sweep 0.6s ease-out forwards;
}

@keyframes card-sweep {
  to {
    transform: translateX(100%);
  }
}
</style>
