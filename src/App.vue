<script setup>
import { onMounted, ref } from 'vue';
import gsap from 'gsap';
import ShowcaseSection from './components/ShowcaseSection.vue'

const cursor = ref(null);
const follower = ref(null);

onMounted(() => {
  // Respects user's motion preference
  const motionMatch = window.matchMedia("(prefers-reduced-motion: reduce)");

  if (!motionMatch.matches) {
    // Animate the cursor follower
    window.addEventListener('mousemove', e => {
      gsap.to(cursor.value, { duration: 0.2, x: e.clientX, y: e.clientY });
      gsap.to(follower.value, { duration: 0.6, x: e.clientX, y: e.clientY, ease: 'power2.out' });
    });

    // Add hover effects for interactive elements
    const interactiveElements = 'a, button, .project-card';
    document.querySelectorAll(interactiveElements).forEach(el => {
      el.addEventListener('mouseenter', () => {
        gsap.to(follower.value, {
          scale: 2.5,
          duration: 0.3,
          backgroundColor: 'rgba(168, 85, 247, 0.4)'
        });
      });
      el.addEventListener('mouseleave', () => {
        gsap.to(follower.value, {
          scale: 1,
          duration: 0.3,
          backgroundColor: 'rgba(255, 255, 255, 0.2)'
        });
      });
    });
  } else {
    // If reduced motion is preferred, hide the custom cursor elements
    if(cursor.value) cursor.value.style.display = 'none';
    if(follower.value) follower.value.style.display = 'none';
  }
});
</script>

<template>
  <div ref="cursor" class="custom-cursor__dot"></div>
  <div ref="follower" class="custom-cursor__follower"></div>
  <main>
    <ShowcaseSection />
  </main>
</template>

<style>
body {
  background-color: #0a0a0a;
  color: #f0f0f0;
  /* Hide cursor only if motion is not reduced */
  cursor: default;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
}

@media (prefers-reduced-motion: no-preference) {
  body {
    cursor: none;
  }
}

.custom-cursor__dot {
  position: fixed;
  top: -10px;
  left: -10px;
  width: 6px;
  height: 6px;
  background-color: #f0f0f0;
  border-radius: 50%;
  pointer-events: none;
  z-index: 9999;
  mix-blend-mode: difference;
}

.custom-cursor__follower {
  position: fixed;
  top: -20px;
  left: -20px;
  width: 30px;
  height: 30px;
  background-color: rgba(255, 255, 255, 0.2);
  border-radius: 50%;
  pointer-events: none;
  z-index: 9998;
  transition: background-color 0.3s ease;
}
</style>
