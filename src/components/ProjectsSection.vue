<script setup>
import { ref, computed, nextTick } from "vue";
import gsap from "gsap";
import projectHeartImg from "../assets/images/projheart-img.png";
import robinImg from "../assets/images/Xnip2026-03-11_23-47-07.jpg";
import ngImg from "../assets/images/ngImg2.jpg";
import CmsImage from "../assets/images/cmsImg.png";
const allProjects = [
  {
    name: "National Guard",
    image: ngImg,
    description:
      "Lead developer for the Army National Guard's official website. Built with Vue 3, Tailwind CSS, and Laravel, the site serves as a key recruitment and information platform, attracting millions of visitors annually. I was responsible for implementing new features, optimizing performance, and ensuring a seamless user experience across devices. (Also spearheaded the vue2 to vue3 upgrade, which was a beast LOL).",
    link: "https://nationalguard.com",
  },
  {
    name: "National Guard CMS",
    image: CmsImage,
    description:
      "Using Laravel, Vue, and Strapi, I built a custom content management system for the Army National Guard's website. This CMS allows non-technical staff to easily create and manage content, including news articles, events, and recruitment materials. It features a user-friendly interface, role-based permissions, and seamless integration with the front-end, enabling the National Guard team to keep their site up-to-date with fresh content that engages visitors and supports their recruitment efforts.",
    link: "https://example.com",
  },
  {
    name: "RobinUNDERtheHOOD",
    image: robinImg,
    description:
      "RobinUNDERtheHOOD is an application I built to utilize AI to drive my investment portfolio. Using multiple APIs, it aggregates financial news, analyzes my portfolio, and feeds it in a machine readable doc passed to an LLM, which then generates actionable insights to help me make informed investment decisions. It's a personal project that combines my passion for finance and technology, and it's been invaluable in navigating the markets. Currently only on my local machine, but may host it publicly for non-tech friends if I end the year in the green, LOL.",
    link: "https://github.com/danmorro59/RobinUNDERtheHOOD",
  },
  {
    name: "Project Heart",
    image: projectHeartImg,
    description:
      "This was a small commercial website I built for a nonprofit finding a cure for CHD. This was a really cool experience. I used wordpress/elementor to provide easy access for the client, but also built a custom plugin to automate email campaigns. This application also has a custom stripe integration (that was pretty complex to do in wordpress) to allow users to donate to the cause.",
    link: "https://projectheart.org/",
  },
];

const perPage = 4;
const pageIndex = ref(0);
const isShuffling = ref(false);

const totalPages = computed(() => Math.ceil(allProjects.length / perPage));
const visibleProjects = computed(() => {
  const start = pageIndex.value * perPage;
  return allProjects.slice(start, start + perPage);
});

const containerRef = ref(null);
const activeIndex = ref(null);
const cardWidth = 486;
const gap = 40;

function getCards() {
  return containerRef.value.querySelectorAll(".project-card");
}

function getContents() {
  return containerRef.value.querySelectorAll(".project-content");
}

function expandCard(index) {
  if (activeIndex.value === index || isShuffling.value) return;
  activeIndex.value = index;

  const containerWidth = containerRef.value.offsetWidth;
  const cards = getCards();
  const contents = getContents();

  cards.forEach((card, i) => {
    const content = contents[i];
    if (i === index) {
      gsap.to(card, {
        width: containerWidth,
        duration: 0.5,
        ease: "power2.inOut",
      });
      gsap.to(content, {
        opacity: 1,
        duration: 0.4,
        delay: 0.2,
        ease: "power2.out",
      });
    } else {
      gsap.to(card, {
        width: 0,
        duration: 0.5,
        ease: "power2.inOut",
      });
      gsap.to(content, {
        opacity: 0,
        duration: 0.15,
        ease: "power2.in",
      });
    }
  });
}

function collapseAll() {
  if (isShuffling.value) return;
  activeIndex.value = null;

  const cards = getCards();
  const contents = getContents();

  cards.forEach((card, i) => {
    gsap.to(card, {
      width: cardWidth,
      duration: 0.5,
      ease: "power2.inOut",
    });
    gsap.to(contents[i], {
      opacity: 0,
      duration: 0.15,
      ease: "power2.in",
    });
  });
}

async function shuffle() {
  if (isShuffling.value) return;
  isShuffling.value = true;
  activeIndex.value = null;

  const cards = getCards();
  const contents = getContents();

  // Phase 1: cards tumble out in 3D
  const tl = gsap.timeline();

  cards.forEach((card, i) => {
    gsap.set(contents[i], { opacity: 0 });

    const direction = i % 2 === 0 ? -1 : 1;
    tl.to(
      card,
      {
        rotateY: direction * (90 + Math.random() * 90),
        rotateX: (Math.random() - 0.5) * 40,
        x: direction * (150 + Math.random() * 200),
        y: (Math.random() - 0.5) * 80,
        z: -(200 + Math.random() * 300),
        scale: 0.6,
        opacity: 0,
        duration: 0.5,
        ease: "power3.in",
      },
      i * 0.06,
    );
  });

  await tl.then();

  // Phase 2: switch page
  pageIndex.value = (pageIndex.value + 1) % totalPages.value;
  await nextTick();

  // Phase 3: new cards flip in from 3D space
  const newCards = getCards();
  const newContents = getContents();
  const tl2 = gsap.timeline();

  newCards.forEach((card, i) => {
    gsap.set(newContents[i], { opacity: 0 });

    const direction = i % 2 === 0 ? 1 : -1;
    gsap.set(card, {
      rotateY: direction * -(90 + Math.random() * 90),
      rotateX: (Math.random() - 0.5) * 40,
      x: direction * (150 + Math.random() * 200),
      y: (Math.random() - 0.5) * 80,
      z: -(200 + Math.random() * 300),
      scale: 0.6,
      opacity: 0,
      width: cardWidth,
    });

    tl2.to(
      card,
      {
        rotateY: 0,
        rotateX: 0,
        x: 0,
        y: 0,
        z: 0,
        scale: 1,
        opacity: 1,
        width: cardWidth,
        duration: 0.6,
        ease: "back.out(1.4)",
      },
      i * 0.08,
    );
  });

  await tl2.then();
  isShuffling.value = false;
}
</script>

<template>
  <section id="projects" class="py-20 px-8">
    <div class="max-w-[1540px] mx-auto">
      <div class="flex items-center justify-between mb-4">
        <h2 class="font-roboto text-3xl md:text-[49px] font-bold text-gray-5">
          projects( )
        </h2>
        <button
          v-if="totalPages > 1"
          @click="shuffle"
          :disabled="isShuffling"
          class="btn-gradient flex items-center gap-2 px-4 py-2 rounded-[8px] font-roboto text-base font-bold text-white hover:text-accent transition-colors cursor-pointer disabled:opacity-50 disabled:cursor-not-allowed"
        >
          <svg
            xmlns="http://www.w3.org/2000/svg"
            class="w-5 h-5"
            fill="none"
            viewBox="0 0 24 24"
            stroke="currentColor"
            stroke-width="2"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15"
            />
          </svg>
          Shuffle
        </button>
      </div>
      <p class="font-roboto text-base md:text-lg text-gray-3 max-w-2xl mb-16">
        Most of my larger applications have been built for the
        <span class="text-accent">Department of Defense</span> and can't be
        shared publicly. Here are some smaller commercial and personal projects
        I've built on over the years.
      </p>

      <!-- Projects row -->
      <div
        ref="containerRef"
        class="flex overflow-hidden"
        :style="{ gap: gap + 'px', perspective: '1200px' }"
        @mouseleave="collapseAll"
      >
        <div
          v-for="(project, i) in visibleProjects"
          :key="pageIndex + '-' + i"
          class="project-card flex-shrink-0 h-[280px] md:h-[361px] rounded-[14px] overflow-hidden border-2 border-white shadow-[0_4px_10px_5px_rgba(0,0,0,0.25)] cursor-pointer flex"
          :style="{ width: cardWidth + 'px' }"
          @mouseenter="expandCard(i)"
        >
          <!-- Image (fixed size) -->
          <div class="relative flex-shrink-0 w-[360px] md:w-[486px] h-full">
            <img
              :src="project.image"
              :alt="project.name"
              class="w-full h-full object-contain bg-black"
            />
            <!-- Title bar -->
            <div
              class="absolute bottom-0 left-0 right-0 h-[79px] bg-card-overlay rounded-bl-[14px] flex items-center px-6"
            >
              <h3
                class="font-roboto text-2xl md:text-[35px] font-bold text-white"
              >
                {{ project.name }}
              </h3>
            </div>
          </div>

          <!-- Expanded content -->
          <div
            class="project-content flex-1 min-w-0 p-8 flex flex-col justify-center opacity-0"
          >
            <h3
              class="font-roboto text-2xl md:text-[35px] font-bold text-accent mb-4 whitespace-nowrap"
            >
              {{ project.name }}
            </h3>
            <p
              class="font-roboto text-base md:text-lg text-gray-3 leading-relaxed mb-6"
            >
              {{ project.description }}
            </p>
            <a
              v-if="project.link"
              :href="project.link"
              target="_blank"
              rel="noopener noreferrer"
              class="btn-gradient inline-block px-4 py-2 rounded-[8px] font-roboto text-base font-bold text-white hover:text-accent transition-colors self-start"
            >
              View project
            </a>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>
