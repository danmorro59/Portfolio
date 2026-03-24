<script setup>
import { ref, nextTick } from "vue";
import gsap from "gsap";

const activeVideo = ref(null);
const galleryImages = ref([]);
const galleryOpen = ref(false);
const galleryLoading = ref(false);
const carouselRadius = ref(0);
const carouselPerspective = ref(2000);

const ringRef = ref(null);
const skelRingRef = ref(null);
let currentAngle = 0;
let autoRotateTween = null;
let skelRotateTween = null;
let isDragging = false;
let dragStartX = 0;
let dragStartAngle = 0;
const skelCount = 12;
const visibleCount = 8;
const skelRadius = 700;
const skelPerspective = 1800;

// Swap system: cycle all assets through limited visible slots
let allMedia = [];
let mediaQueue = [];
let slotSwapped = []; // tracks if each slot was already swapped this pass through the back

function openVideo(url) {
  activeVideo.value = url;
}

function closeVideo() {
  activeVideo.value = null;
}

const videoExts = [".mp4", ".webm", ".mov", ".ogg"];

function isVideo(src) {
  return videoExts.some((ext) => src.toLowerCase().endsWith(ext));
}

function preloadMedia(items) {
  return Promise.all(
    items.map((item) => {
      return new Promise((resolve) => {
        if (isVideo(item)) {
          const video = document.createElement("video");
          video.preload = "auto";
          video.oncanplaythrough = () => resolve();
          video.onerror = () => resolve();
          video.src = item;
        } else {
          const img = new Image();
          img.onload = () => resolve();
          img.onerror = () => resolve();
          img.src = item;
        }
      });
    }),
  );
}

const tiltX = -15; // degrees of downward tilt for 3D orbit look

const baseCardW = 400;
const maxCardW = 520; // front-facing cards widen to this

function setRingRotation(angle) {
  if (!ringRef.value) return;
  ringRef.value.style.transform = `rotateX(${tiltX}deg) rotateY(${angle}deg)`;
  scaleCardWidths(angle);
  swapBackCards(angle);
}

function swapBackCards(angle) {
  if (
    !galleryImages.value.length ||
    (!mediaQueue.length && allMedia.length <= visibleCount)
  )
    return;
  const count = galleryImages.value.length;

  for (let i = 0; i < count; i++) {
    const cardAngle = (360 / count) * i;
    const facing = Math.cos(((cardAngle + angle) * Math.PI) / 180);

    // Card is at the back (facing away from viewer)
    if (facing < -0.9) {
      if (!slotSwapped[i]) {
        slotSwapped[i] = true;
        const next = mediaQueue.shift();
        if (next) {
          galleryImages.value[i] = {
            src: next,
            id: Math.random(),
            type: isVideo(next) ? "video" : "image",
          };
          // Refill queue when it runs out
          if (mediaQueue.length === 0) {
            mediaQueue = [...allMedia];
          }
        }
      }
    } else {
      // Reset swap flag when card leaves the back
      slotSwapped[i] = false;
    }
  }
}

function scaleCardWidths(angle) {
  if (!ringRef.value) return;
  const cards = ringRef.value.querySelectorAll(".gallery-card");
  const count = cards.length;
  if (!count) return;

  cards.forEach((card, i) => {
    const cardAngle = (360 / count) * i;
    // How much this card faces the viewer (1 = front, -1 = back)
    const facing = Math.cos(((cardAngle + angle) * Math.PI) / 180);
    // Only widen cards in the front half (facing > 0)
    const t = Math.max(0, facing);
    const w = baseCardW + (maxCardW - baseCardW) * t * t; // ease-in curve
    card.style.width = w + "px";
  });
}

function startAutoRotate() {
  stopAutoRotate();
  const obj = { angle: currentAngle };
  autoRotateTween = gsap.to(obj, {
    angle: currentAngle - 360,
    duration: 20,
    ease: "none",
    repeat: -1,
    onUpdate: () => {
      currentAngle = obj.angle;
      setRingRotation(currentAngle);
    },
  });
}

function stopAutoRotate() {
  if (autoRotateTween) {
    autoRotateTween.kill();
    autoRotateTween = null;
  }
}

function onDragStart(e) {
  isDragging = true;
  dragStartX = e.clientX ?? e.touches?.[0]?.clientX ?? 0;
  dragStartAngle = currentAngle;
  stopAutoRotate();
}

function onDragMove(e) {
  if (!isDragging) return;
  const clientX = e.clientX ?? e.touches?.[0]?.clientX ?? 0;
  const delta = clientX - dragStartX;
  currentAngle = dragStartAngle + delta * 0.3;
  setRingRotation(currentAngle);
}

function onDragEnd() {
  if (!isDragging) return;
  isDragging = false;
  startAutoRotate();
}

function startSkelRotate() {
  stopSkelRotate();
  const obj = { angle: 0 };
  skelRotateTween = gsap.to(obj, {
    angle: -360,
    duration: 20,
    ease: "none",
    repeat: -1,
    onUpdate: () => {
      if (skelRingRef.value) {
        skelRingRef.value.style.transform = `rotateX(${tiltX}deg) rotateY(${obj.angle}deg)`;
      }
    },
  });
}

function stopSkelRotate() {
  if (skelRotateTween) {
    skelRotateTween.kill();
    skelRotateTween = null;
  }
}

async function openGallery(media) {
  galleryOpen.value = true;
  galleryLoading.value = true;
  currentAngle = 0;
  await nextTick();

  // Start skeleton orbit
  startSkelRotate();

  // Preload with minimum display time
  const minDelay = new Promise((r) => setTimeout(r, 1200));
  await Promise.all([preloadMedia(media), minDelay]);

  // Stop skeleton orbit
  stopSkelRotate();

  // Use same radius and perspective as skeleton for matching look
  carouselRadius.value = skelRadius;
  carouselPerspective.value = skelPerspective;

  // Shuffle media for variety, then split into visible slots + queue
  allMedia = [...media].sort(() => Math.random() - 0.5);
  const visible = allMedia.slice(0, visibleCount);
  mediaQueue = [...allMedia.slice(visibleCount)];
  slotSwapped = new Array(visibleCount).fill(false);

  // If we have more than visible, queue the rest (refill when empty)
  if (mediaQueue.length === 0 && allMedia.length > visibleCount) {
    mediaQueue = [...allMedia];
  }

  // Switch to real content
  galleryLoading.value = false;
  galleryImages.value = visible.map((item) => ({
    src: item,
    id: Math.random(),
    type: isVideo(item) ? "video" : "image",
  }));
  await nextTick();

  startAutoRotate();
}

function closeGallery() {
  stopAutoRotate();
  galleryOpen.value = false;
  galleryImages.value = [];
  galleryLoading.value = false;
  carouselRadius.value = 0;
  allMedia = [];
  mediaQueue = [];
  slotSwapped = [];
}

// Dynamically import gallery images for the USMC experience
const galleryModules = import.meta.glob("../assets/images/journey-gallery/*", {
  eager: true,
});
const usmcGallery = Object.values(galleryModules).map((mod) => mod.default);

const companies = [
  {
    title: "United States Marine Corps",
    year: "2019-2023",
    description:
      "I graduated high school early to join the Marine Corps at 17. My positions in the Marines consisted of a wide range of responsibilities, from maintaining unit readiness, being an aerial observer, to being a marksmanship coach and combat instructor. I wore many hats throughout my Marine Corps career, but my favorite was the amount of travel these positions brought me. I have been almost everywhere in Asia, check out some memories from my travels below.",
    side: "right",
    gallery: usmcGallery,
  },
  {
    title: "Hack Reactor",
    year: "2023",
    description:
      "After leaving the Marine Corps, I focused on advancing my development skills by enrolling in a 14-week, full-time immersive full-stack program, where I earned a certificate upon completion. Through consistent effort and hands-on experience, I was able to grow quickly as a developer—ultimately receiving a job offer before even finishing 80% of the course.",
    side: "left",
  },
  {
    title: "Junior Frontend Developer",
    year: "2023-2024",
    description:
      "I began my role at iostudio as a junior developer, where I quickly adapted to a legacy codebase and established team workflows. Over time, I became the subject matter expert on our largest project—progressing from resolving bugs to leading the migration from Vue 2 to Vue 3.",
    side: "right",
  },
  {
    title: "Mid-Level Developer",
    year: "2024-Present",
    description:
      "As a mid-level developer, I took on more complex projects and responsibilities, contributing to both frontend and backend development. I played a key role in designing and implementing new features, optimizing performance, and mentoring junior developers.",
    side: "left",
  },

];
</script>

<template>
  <section class="bg-dark-panel py-20 px-8">
    <div class="max-w-[1540px] mx-auto">
      <h2
        class="font-roboto text-3xl md:text-[49px] font-bold text-gray-5 mb-16"
      >
        myJourney( )
      </h2>

      <!-- Timeline -->
      <div class="relative">
        <!-- Vertical line -->
        <div
          class="absolute left-4 md:left-1/2 md:-translate-x-1/2 top-0 bottom-0 w-[5px] bg-accent"
        ></div>

        <!-- Timeline items -->
        <div class="flex flex-col gap-16">
          <div
            v-for="(company, i) in companies"
            :key="i"
            class="relative flex"
            :class="
              company.side === 'right' ? 'md:justify-end' : 'md:justify-start'
            "
          >
            <!-- Dot -->
            <div
              class="absolute left-4 md:left-1/2 -translate-x-1/2 top-2 w-[39px] h-[39px] rounded-full bg-[#070707] border-4 border-accent z-10"
            ></div>

            <!-- Year (desktop — opposite side of card) -->
            <div
              v-if="company.year"
              class="hidden md:flex absolute top-0 h-full items-center pointer-events-none select-none"
              :class="
                company.side === 'right'
                  ? 'left-0 w-[calc(50%-30px)] justify-center'
                  : 'left-[calc(50%+30px)] w-[calc(50%-30px)] justify-center'
              "
            >
              <span class="year-label font-roboto text-[80px] font-black text-accent/15">
                {{ company.year }}
              </span>
            </div>

            <!-- Card -->
            <div
              class="ml-14 md:ml-0 w-full md:w-[473px]"
              :class="
                company.side === 'right'
                  ? 'md:ml-[calc(50%+60px)]'
                  : 'md:mr-[calc(50%+60px)]'
              "
            >
              <!-- Year (mobile — above title) -->
              <span
                v-if="company.year"
                class="md:hidden font-roboto text-sm font-bold text-accent mb-1 block"
              >
                {{ company.year }}
              </span>
              <h3
                class="font-roboto text-2xl md:text-[39px] font-bold text-accent mb-3"
              >
                {{ company.title }}
              </h3>
              <p
                class="font-roboto text-base md:text-[20px] font-light text-white leading-[1.5] mb-6"
              >
                {{ company.description }}
              </p>
              <div class="flex flex-wrap gap-3">
                <!-- Gallery button -->
                <button
                  v-if="company.gallery && company.gallery.length"
                  @click="openGallery(company.gallery)"
                  class="btn-gradient inline-block px-5 py-2.5 rounded-[8px] font-roboto text-base md:text-lg font-bold text-white hover:text-accent transition-colors cursor-pointer"
                >
                  View memories
                </button>
                <!-- Video button -->
                <button
                  v-if="company.video"
                  @click="openVideo(company.video)"
                  class="btn-gradient inline-block px-5 py-2.5 rounded-[8px] font-roboto text-base md:text-lg font-bold text-white hover:text-accent transition-colors cursor-pointer"
                >
                  My experience
                </button>
                <!-- Website link -->
                <a
                  v-if="company.website"
                  :href="company.website"
                  class="btn-gradient inline-block px-5 py-2.5 rounded-[8px] font-roboto text-base md:text-lg font-bold text-white hover:text-accent transition-colors"
                >
                  Go to website
                </a>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>

  <!-- Video Modal -->
  <Teleport to="body">
    <div
      v-if="activeVideo"
      class="fixed inset-0 z-50 flex items-center justify-center bg-black/80"
      @click.self="closeVideo"
    >
      <div class="relative w-[90vw] max-w-4xl aspect-video">
        <button
          @click="closeVideo"
          class="absolute -top-10 right-0 font-roboto text-lg font-bold text-white hover:text-accent transition-colors cursor-pointer"
        >
          Close
        </button>
        <iframe
          :src="activeVideo"
          class="w-full h-full rounded-lg"
          frameborder="0"
          allow="
            accelerometer;
            autoplay;
            clipboard-write;
            encrypted-media;
            gyroscope;
            picture-in-picture;
          "
          allowfullscreen
        ></iframe>
      </div>
    </div>
  </Teleport>

  <!-- Gallery Modal -->
  <Teleport to="body">
    <div
      v-if="galleryOpen"
      class="gallery-modal fixed inset-0 z-50 bg-black/90 select-none"
      @click.self="closeGallery"
      @mousedown="onDragStart"
      @mousemove="onDragMove"
      @mouseup="onDragEnd"
      @mouseleave="onDragEnd"
      @touchstart="onDragStart"
      @touchmove="onDragMove"
      @touchend="onDragEnd"
    >
      <!-- Close button -->
      <button
        @click="closeGallery"
        class="absolute top-6 right-8 z-[300] font-roboto text-lg font-bold text-white hover:text-accent transition-colors cursor-pointer"
      >
        Close
      </button>

      <!-- Loading skeleton orbit -->
      <div
        v-if="galleryLoading"
        class="carousel-scene"
        :style="{ perspective: skelPerspective + 'px' }"
      >
        <div ref="skelRingRef" class="carousel-ring skel-ring">
          <div
            v-for="n in skelCount"
            :key="n"
            class="skeleton-card"
            :style="{
              transform: `rotateY(${(360 / skelCount) * n}deg) translateZ(${skelRadius}px)`,
            }"
          >
            <div class="skeleton-shimmer"></div>
          </div>
        </div>
        <p class="absolute bottom-[20%] font-roboto text-white/40 text-sm">
          Loading media...
        </p>
      </div>

      <!-- 3D Carousel -->
      <div
        v-if="!galleryLoading && galleryImages.length"
        class="carousel-scene"
        :style="{ perspective: carouselPerspective + 'px' }"
      >
        <div ref="ringRef" class="carousel-ring">
          <div
            v-for="(img, idx) in galleryImages"
            :key="img.id"
            class="gallery-card"
            :style="{
              transform: `rotateY(${(360 / galleryImages.length) * idx}deg) translateZ(${carouselRadius}px)`,
            }"
          >
            <video
              v-if="img.type === 'video'"
              :src="img.src"
              autoplay
              loop
              muted
              playsinline
              class="w-full h-full object-cover pointer-events-none"
            />
            <img
              v-else
              :src="img.src"
              :alt="'Gallery image ' + (idx + 1)"
              class="w-full h-full object-cover pointer-events-none"
            />
          </div>
        </div>
      </div>
    </div>
  </Teleport>
</template>

<style scoped>
.carousel-scene {
  position: absolute;
  inset: 0;
  display: flex;
  align-items: center;
  justify-content: center;
}

.carousel-ring {
  position: relative;
  width: 400px;
  height: 280px;
  transform-style: preserve-3d;
  will-change: transform;
  cursor: grab;
}

.carousel-ring:active {
  cursor: grabbing;
}

.gallery-card {
  position: absolute;
  width: 400px;
  height: 280px;
  border-radius: 12px;
  overflow: hidden;
  border: 2px solid rgba(255, 255, 255, 0.2);
  box-shadow: 0 8px 30px rgba(0, 0, 0, 0.6);
  transition:
    box-shadow 0.3s ease,
    width 0.15s ease-out;
}

.gallery-card:hover {
  box-shadow: 0 12px 40px rgba(112, 255, 0, 0.3);
}

.skel-ring {
  width: 280px;
  height: 200px;
}

.skeleton-card {
  position: absolute;
  width: 280px;
  height: 200px;
  border-radius: 12px;
  overflow: hidden;
  border: 2px solid rgba(255, 255, 255, 0.08);
  background: rgba(255, 255, 255, 0.06);
}

.skeleton-shimmer {
  width: 100%;
  height: 100%;
  position: relative;
  overflow: hidden;
}

.skeleton-shimmer::after {
  content: "";
  position: absolute;
  inset: 0;
  background: linear-gradient(
    90deg,
    transparent 0%,
    rgba(255, 255, 255, 0.08) 50%,
    transparent 100%
  );
  animation: shimmer 1.5s infinite;
}

@keyframes shimmer {
  0% {
    transform: translateX(-100%);
  }
  100% {
    transform: translateX(100%);
  }
}
</style>
