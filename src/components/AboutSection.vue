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
        <div
          class="bg-card-bg rounded-[6px] shadow-[0_4px_10px_5px_rgba(0,0,0,0.25)] p-6 md:p-8 w-full lg:w-[569px]"
        >
          <div class="flex items-start justify-between mb-4">
            <h3
              class="font-courier text-2xl md:text-[33px] font-bold text-white"
            >
              Fullstack Developer
            </h3>
            <img :src="xmlIcon" alt="Code" class="w-[50px] h-[50px]" />
          </div>
          <a
            href="#projects"
            class="font-courier text-xl md:text-[25px] font-normal text-accent hover:underline"
            >Projects</a
          >
        </div>

        <!-- Freelancer Card -->
        <div
          class="bg-card-bg rounded-[6px] shadow-[0_4px_10px_5px_rgba(0,0,0,0.25)] p-6 md:p-8 w-full lg:w-[569px]"
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
          <a
            href="#"
            class="font-courier text-xl md:text-[25px] font-normal text-accent hover:underline"
            >Mid-level</a
          >
        </div>
      </div>
    </div>
  </section>
</template>
