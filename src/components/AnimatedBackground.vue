<template>
  <div class="background-container fixed top-0 left-0 w-full h-full -z-10 overflow-hidden">
    <div class="shape circle c1"></div>
    <div class="shape circle c2"></div>
    <div class="shape square s1"></div>
    <div class="shape circle c3"></div>
    <div class="shape square s2"></div>
    <div class="shape circle c4"></div>
  </div>
</template>

<script setup>
import { onMounted } from 'vue';
import gsap from 'gsap';

onMounted(() => {
  const motionMatch = window.matchMedia("(prefers-reduced-motion: reduce)");

  if (motionMatch.matches) {
    // If reduced motion is preferred, don't run the background animations.
    // The shapes will just be static decorative elements.
    return;
  }

  gsap.utils.toArray('.shape').forEach(shape => {
    gsap.to(shape, {
      x: () => `random(-20vw, 20vw)`,
      y: () => `random(-20vh, 20vh)`,
      rotation: () => `random(0, 180)`,
      scale: () => `random(0.8, 1.5)`,
      duration: () => `random(15, 30)`,
      ease: 'sine.inOut',
      repeat: -1,
      yoyo: true,
    });
  });
});
</script>

<style scoped>
.shape {
  position: absolute;
  opacity: 0.1;
  will-change: transform;
}
.circle {
  border-radius: 50%;
}
.square {
  border-radius: 15%;
}
.c1 {
  width: 20vw;
  height: 20vw;
  top: 10vh;
  left: 5vw;
  background: radial-gradient(circle, #8A2BE2, transparent 60%);
}
.c2 {
  width: 15vw;
  height: 15vw;
  top: 60vh;
  left: 20vw;
  background: radial-gradient(circle, #FF1493, transparent 60%);
}
.s1 {
  width: 25vw;
  height: 25vw;
  top: 25vh;
  left: 70vw;
  background: linear-gradient(45deg, #8A2BE2, #FF1493);
}
.c3 {
  width: 10vw;
  height: 10vw;
  top: 80vh;
  left: 85vw;
  background: radial-gradient(circle, #DC143C, transparent 60%);
}
.s2 {
  width: 18vw;
  height: 18vw;
  top: -10vh;
  left: 40vw;
  background: linear-gradient(135deg, #FF1493, #DC143C);
}
.c4 {
  width: 12vw;
  height: 12vw;
  top: 50vh;
  left: 50vw;
  opacity: 0.05;
  background: radial-gradient(circle, #8A2BE2, transparent 70%);
}
</style>
