<script setup>
import { ref, computed, nextTick } from "vue";
import gsap from "gsap";
import projectThumb from "../assets/images/project-thumb.png";
import robinImg from "../assets/images/Xnip2026-03-11_23-47-07.jpg";

const allProjects = [
  {
    name: "RobinUNDERtheHOOD",
    image: robinImg,
    description:
      "RobinUNDERtheHOOD is an application I built to utilize AI to drive my investment portfolio. Using multiple APIs, it aggregates financial news, analyzes my portfolio, and feeds it in a machine readable doc passed to an LLM, which then generates actionable insights to help me make informed investment decisions. It's a personal project that combines my passion for finance and technology, and it's been invaluable in navigating the markets. Currently only on my local machine, but may host it publicly for non-tech friends if I end the year in the green, LOL.",
    link: "https://github.com/danmorro59/RobinUNDERtheHOOD",
  },
  {
    name: "Project 2",
    image: projectThumb,
    description:
      "A brief description of this project. What it does, the tech used, and the problem it solves.",
    link: "https://example.com",
  },
  {
    name: "Project 3",
    image: projectThumb,
    description:
      "A brief description of this project. What it does, the tech used, and the problem it solves.",
    link: "https://example.com",
  },
  {
    name: "Project 4",
    image: projectThumb,
    description:
      "A brief description of this project. What it does, the tech used, and the problem it solves.",
    link: "https://example.com",
  },
  {
    name: "Project 5",
    image: projectThumb,
    description:
      "A brief description of this project. What it does, the tech used, and the problem it solves.",
    link: "https://example.com",
  },
  {
    name: "Project 6",
    image: projectThumb,
    description:
      "A brief description of this project. What it does, the tech used, and the problem it solves.",
    link: "https://example.com",
  },
  {
    name: "Project 7",
    image: projectThumb,
    description:
      "A brief description of this project. What it does, the tech used, and the problem it solves.",
    link: "https://example.com",
  },
  {
    name: "Project 8",
    image: projectThumb,
    description:
      "A brief description of this project. What it does, the tech used, and the problem it solves.",
    link: "https://example.com",
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

  // Phase 1: scatter cards out with random rotations and directions
  const tl = gsap.timeline();

  cards.forEach((card, i) => {
    // Reset content opacity immediately
    gsap.set(contents[i], { opacity: 0 });

    const direction = i % 2 === 0 ? -1 : 1;
    tl.to(
      card,
      {
        x: direction * (200 + Math.random() * 300),
        y: (Math.random() - 0.5) * 150,
        rotation: direction * (10 + Math.random() * 20),
        scale: 0.8,
        opacity: 0,
        duration: 0.4,
        ease: "power2.in",
      },
      0,
    );
  });

  await tl.then();

  // Phase 2: switch page
  pageIndex.value = (pageIndex.value + 1) % totalPages.value;
  await nextTick();

  // Phase 3: new cards fly in from scattered positions
  const newCards = getCards();
  const newContents = getContents();
  const tl2 = gsap.timeline();

  newCards.forEach((card, i) => {
    gsap.set(newContents[i], { opacity: 0 });

    const direction = i % 2 === 0 ? 1 : -1;
    gsap.set(card, {
      x: direction * (200 + Math.random() * 300),
      y: (Math.random() - 0.5) * 150,
      rotation: direction * (10 + Math.random() * 20),
      scale: 0.8,
      opacity: 0,
      width: cardWidth,
    });

    tl2.to(
      card,
      {
        x: 0,
        y: 0,
        rotation: 0,
        scale: 1,
        opacity: 1,
        width: cardWidth,
        duration: 0.5,
        ease: "back.out(1.2)",
      },
      0.05 * i,
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
          class="flex items-center gap-2 px-6 py-3 rounded-[12px] font-roboto text-lg font-bold text-white hover:text-accent transition-colors cursor-pointer disabled:opacity-50 disabled:cursor-not-allowed"
          style="
            border: 3px solid transparent;
            border-image: linear-gradient(180deg, #70ff00 0%, #ffffff 100%) 1;
          "
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
        :style="{ gap: gap + 'px' }"
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
              class="w-full h-full object-cover"
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
              class="inline-block px-6 py-3 rounded-[12px] font-roboto text-lg font-bold text-white hover:text-accent transition-colors self-start"
              style="
                border: 3px solid transparent;
                border-image: linear-gradient(180deg, #70ff00 0%, #ffffff 100%)
                  1;
              "
            >
              View project
            </a>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>
