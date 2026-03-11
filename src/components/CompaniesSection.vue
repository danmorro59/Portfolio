<script setup>
const companies = [
  {
    title: 'lorem ipsum',
    description: 'Eum earum possimus qui sunt possimus aut tempora aliquid ut veritatis architecto et voluptate enim id sint possimus. Aut nobis est fuga officia et nemo nulla et quod quia ex dolor placeat rem repudiandae voluptas.',
    side: 'right',
  },
  {
    title: 'lorem ipsum',
    description: 'Eum earum possimus qui sunt possimus aut tempora aliquid ut veritatis architecto et voluptate enim id sint possimus. Aut nobis est fuga officia et nemo nulla et quod quia ex dolor placeat rem repudiandae voluptas.',
    side: 'left',
  },
  {
    title: 'lorem ipsum',
    description: 'Eum earum possimus qui sunt possimus aut tempora aliquid ut veritatis architecto et voluptate enim id sint possimus. Aut nobis est fuga officia et nemo nulla et quod quia ex dolor placeat rem repudiandae voluptas.',
    side: 'right',
  },
  {
    title: 'lorem ipsum',
    description: 'Eum earum possimus qui sunt possimus aut tempora aliquid ut veritatis architecto et voluptate enim id sint possimus. Aut nobis est fuga officia et nemo nulla et quod quia ex dolor placeat rem repudiandae voluptas.',
    side: 'left',
  },
  {
    title: 'lorem ipsum',
    description: 'Eum earum possimus qui sunt possimus aut tempora aliquid ut veritatis architecto et voluptate enim id sint possimus. Aut nobis est fuga officia et nemo nulla et quod quia ex dolor placeat rem repudiandae voluptas.',
    side: 'right',
  },
]
</script>

<template>
  <section class="bg-dark-panel py-20 px-8">
    <div class="max-w-[1540px] mx-auto">
      <h2 class="font-roboto text-3xl md:text-[49px] font-bold text-gray-5 mb-16">companies( )</h2>

      <!-- Timeline -->
      <div class="relative">
        <!-- Vertical line -->
        <div class="absolute left-4 md:left-1/2 md:-translate-x-1/2 top-0 bottom-0 w-[5px] bg-accent"></div>

        <!-- Timeline items -->
        <div class="flex flex-col gap-16">
          <div
            v-for="(company, i) in companies"
            :key="i"
            class="relative flex"
            :class="company.side === 'right' ? 'md:justify-end' : 'md:justify-start'"
          >
            <!-- Dot -->
            <div class="absolute left-4 md:left-1/2 -translate-x-1/2 top-2 w-[39px] h-[39px] rounded-full bg-[#070707] border-4 border-accent z-10"></div>

            <!-- Card -->
            <div
              class="ml-14 md:ml-0 w-full md:w-[473px]"
              :class="company.side === 'right' ? 'md:ml-[calc(50%+60px)]' : 'md:mr-[calc(50%+60px)]'"
            >
              <h3 class="font-roboto text-2xl md:text-[39px] font-bold text-accent mb-3">{{ company.title }}</h3>
              <p class="font-roboto text-base md:text-[20px] font-light text-white leading-[1.5] mb-6">
                {{ company.description }}
              </p>
              <a
                href="#"
                class="inline-block px-8 py-4 rounded-[12px] font-roboto text-lg md:text-[23px] font-bold text-white hover:text-accent transition-colors"
                style="border: 4px solid transparent; border-image: linear-gradient(180deg, #70FF00 0%, #FFFFFF 100%) 1;"
              >
                Go to website
              </a>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>
