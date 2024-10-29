<template>
  <div class="accordion">
    <div
      v-for="(item, index) in items"
      :key="index"
      class="accordion-item border-t"
    >
      <!-- Accordion Header -->
      <button
        class="accordion-header my-6 w-full p-4 bg-gray-200 hover:bg-gray-300 text-left font-bold cursor-pointer"
        @click="toggle(index)"
      >
        {{ item.title }}
      </button>
      
      <!-- Accordion Body with Enhanced Slide Down Animation -->
      <transition
        name="slide-fade"
        @before-enter="beforeEnter"
        @enter="enter"
        @leave="leave"
      >
        <div
          v-show="activeIndex === index"
          class="accordion-body overflow-hidden"
          ref="accordionBodies"
        >
          <div class="p-4 bg-white">
            <div>
              <label> About </label>
              <input class="h-10 border border-gray-300 rounded-md" />
            </div>
          </div>
        </div>
      </transition>
    </div>
  </div>
</template>

<script setup>
import { ref, watch, nextTick, onMounted } from 'vue';
import { useRoute } from 'vue-router';

// Sample data for the accordion items
const items = [
  { title: 'Profile Information' },
  { title: 'Social Media' },
  { title: 'Personal Payee Information' },
  { title: 'Business Payee Information' },
  { title: 'Job Information' },
  { title: 'Secondary Payee' },
];

// Route mapping to accordion index
const routeToIndexMap = {
  'profile-info': 0,
  'social-media': 1,
  'personal-payee': 2,
  'business-payee': 3,
  'job-info': 4,
  'secondary-payee': 5,
};

// State to track the currently active section
const activeIndex = ref(null);
const route = useRoute();

// Set height before enter for smoother animation
const beforeEnter = (el) => {
  el.style.height = '0';
  el.style.opacity = '0';
  el.style.transform = 'scaleY(0.95)';
};

const enter = (el) => {
  nextTick(() => {
    el.style.transition = 'all 0.5s ease';
    el.style.height = el.scrollHeight + 'px';
    el.style.opacity = '1';
    el.style.transform = 'scaleY(1)';
  });
};

const leave = (el) => {
  el.style.transition = 'all 0.5s ease';
  el.style.height = '0';
  el.style.opacity = '0';
  el.style.transform = 'scaleY(0.95)';
};

// Function to toggle accordion based on index
function toggle(index) {
  activeIndex.value = activeIndex.value === index ? null : index;
}

// Watch for route changes to update active accordion section
watch(
  () => route.name,
  (newRouteName) => {
    activeIndex.value = routeToIndexMap[newRouteName] ?? null;
  }
);

// Set initial active accordion section on mount
onMounted(() => {
  activeIndex.value = routeToIndexMap[route.name] ?? null;
});
</script>

<style scoped>
.accordion {
  @apply border border-gray-300 rounded-md max-w-lg mx-auto mt-6;
}

.accordion-body {
  transition-property: height, opacity, transform;
  transition-duration: 0.5s;
  transition-timing-function: ease;
}
</style>
