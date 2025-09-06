<template>
  <section ref="main" class="relative min-h-screen w-full p-4 sm:p-8 md:p-12 lg:p-20 overflow-hidden">
    <!-- Motion Path SVG and Element -->
    <div ref="orb" class="absolute top-0 left-0 w-3 h-3 rounded-full bg-purple-500 shadow-[0_0_15px_5px_rgba(168,85,247,0.7)] opacity-0" style="will-change: transform;"></div>
    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 1000 2000" class="absolute top-0 left-0 w-full h-full pointer-events-none">
      <path ref="path" d="M-10,100 C200,200 300,0 500,100 S700,200 800,100 C900,0 1100,200 1010,400 S800,600 700,700 S500,800 400,900 S100,1100 200,1200 S400,1400 500,1500 S700,1700 900,1900" stroke="none" fill="none"></path>
    </svg>

    <div class="relative max-w-7xl mx-auto">
      <!-- Header -->
      <header ref="header" class="text-center mb-16 md:mb-24">
        <h1 class="text-4xl sm:text-5xl md:text-6xl lg:text-7xl font-bold tracking-tighter mb-4">
          <span class="bg-gradient-to-r from-purple-400 via-pink-500 to-red-500 bg-clip-text text-transparent">
            <span ref="headline" class="inline-block">Engineering</span>
            <span ref="headline" class="inline-block">Digital</span>
            <span ref="headline" class="inline-block">Masterpieces</span>
          </span>
        </h1>
        <p ref="intro" class="text-base sm:text-lg md:text-xl text-gray-400 max-w-3xl mx-auto">
          We are a collective of designers and developers pushing the boundaries of web experiences. We combine cutting-edge technology with artistic vision to create sites that are not just visited, but felt.
        </p>
      </header>

      <!-- Projects Grid -->
      <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8">
        <div
          v-for="(project, index) in projects"
          :key="project.id"
          class="project-card bg-gray-800/40 backdrop-blur-xl border border-gray-700/50 rounded-2xl p-6 transition-all duration-300 hover:border-purple-400/50 hover:shadow-2xl hover:shadow-purple-500/10"
          style="will-change: transform, opacity;"
        >
          <div class="flex justify-between items-start mb-4">
            <h2 class="text-2xl font-bold text-gray-100">{{ project.title }}</h2>
            <span class="text-xs font-mono text-purple-400">/ 0{{ index + 1 }}</span>
          </div>

          <p class="text-gray-400 mb-6 min-h-[72px]">{{ project.description }}</p>

          <!-- Metrics -->
          <div class="flex justify-around items-center mb-6 border-y border-gray-700/50 py-4">
            <div v-for="metric in project.metrics" :key="metric.label" class="text-center">
              <p class="text-3xl font-semibold text-white" :data-metric-value="metric.value">0</p>
              <p class="text-xs text-gray-500 uppercase tracking-widest">{{ metric.label }}</p>
            </div>
          </div>

          <!-- Tech Stack -->
          <div class="mb-6">
            <h4 class="text-sm font-semibold text-gray-300 mb-3 text-center">Technology Stack</h4>
            <div class="flex justify-center gap-4 flex-wrap">
              <span v-for="tech in project.tech" :key="tech" class="tech-tag bg-gray-700/50 text-gray-300 text-xs px-3 py-1 rounded-full">
                {{ tech }}
              </span>
            </div>
          </div>

          <!-- Case Study Button -->
          <button class="case-study-btn w-full py-3 rounded-lg bg-gradient-to-r from-purple-500 to-pink-500 text-white font-semibold transition-transform duration-300 ease-out" style="will-change: transform;">
            View Case Study
          </button>
        </div>
      </div>
    </div>
  </section>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue';
import gsap from 'gsap';
import { ScrollTrigger } from 'gsap/ScrollTrigger';
import { TextPlugin } from 'gsap/TextPlugin';
import { MotionPathPlugin } from 'gsap/MotionPathPlugin';

gsap.registerPlugin(ScrollTrigger, TextPlugin, MotionPathPlugin);

const projects = ref([
  { id: 1, title: 'Project Apex', description: 'An immersive WebGL marketing site for a high-performance electric vehicle, featuring a 3D model configurator.', metrics: [{ value: 210, label: 'Engagement' }, { value: 1.5, label: 'Impressions' }], tech: ['Vue.js', 'Three.js', 'GSAP', 'Figma'] },
  { id: 2, title: 'QuantumLeap AI', description: 'A futuristic landing page for a machine learning startup, showcasing complex data visualizations and animations.', metrics: [{ value: 45, label: 'Conversions' }, { value: 1, label: 'Performance' }], tech: ['React', 'D3.js', 'GSAP', 'Node.js'] },
  { id: 3, title: 'Nova Commerce', description: 'A headless e-commerce platform with a focus on micro-interactions and a seamless, buttery-smooth user experience.', metrics: [{ value: -50, label: 'Load Time' }, { value: 88, label: 'Retention' }], tech: ['SvelteKit', 'Shopify API', 'Tailwind', 'GSAP'] }
]);

const main = ref(null);
const headline = ref([]);
const intro = ref(null);
const orb = ref(null);
const path = ref(null);
let ctx;

onMounted(() => {
  ctx = gsap.context((self) => {
    const motionMatch = window.matchMedia("(prefers-reduced-motion: reduce)");

    if (motionMatch.matches) {
      // If reduced motion is preferred, just show the counters
      const metricElements = self.selector('[data-metric-value]');
      metricElements.forEach(el => {
        const targetValue = parseFloat(el.dataset.metricValue);
        const isPercentage = ['engagement', 'conversions', 'retention', 'load time'].includes(el.nextElementSibling.textContent.toLowerCase());
        const isMillions = el.nextElementSibling.textContent.toLowerCase() === 'impressions';
        const isTopPercent = el.nextElementSibling.textContent.toLowerCase() === 'performance';
        if (isPercentage) el.textContent = (targetValue > 0 ? '+' : '') + targetValue + '%';
        else if (isMillions) el.textContent = targetValue.toFixed(1) + 'M';
        else if (isTopPercent) el.textContent = 'Top ' + targetValue + '%';
        else el.textContent = targetValue;
      });
      return; // Skip all animation setup
    }

    // --- IF MOTION IS OK, PROCEED WITH ANIMATIONS ---

    // Set initial states for animations
    gsap.set(".project-card", { opacity: 0, y: 100 });

    // --- Animations that run on all screen sizes ---
    gsap.from(headline.value, { delay: 0.2, opacity: 0, y: 50, duration: 1, ease: 'power4.out', stagger: 0.2 });
    gsap.from(intro.value, { delay: 0.8, opacity: 0, y: 30, duration: 1, ease: 'power4.out' });

    ScrollTrigger.batch(".project-card", {
      start: "top 80%",
      onEnter: batch => {
        gsap.to(batch, {
          opacity: 1, y: 0, stagger: 0.15, ease: "power4.out", duration: 1,
          onComplete: () => batch.forEach(card => animateCardContents(card))
        });
      },
      onLeaveBack: batch => gsap.to(batch, { opacity: 0, y: 100, stagger: 0.1, ease: "power2.in" }),
    });

    function animateCardContents(card) {
        const metricElements = card.querySelectorAll('[data-metric-value]');
        metricElements.forEach(el => {
            const targetValue = parseFloat(el.dataset.metricValue);
            const isPercentage = ['engagement', 'conversions', 'retention', 'load time'].includes(el.nextElementSibling.textContent.toLowerCase());
            const isMillions = el.nextElementSibling.textContent.toLowerCase() === 'impressions';
            const isTopPercent = el.nextElementSibling.textContent.toLowerCase() === 'performance';
            let counter = { val: 0 };
            gsap.to(counter, {
                val: targetValue, duration: 2, ease: 'circ.out',
                onUpdate: () => {
                    if (isPercentage) el.textContent = (targetValue > 0 ? '+' : '') + Math.ceil(counter.val) + '%';
                    else if (isMillions) el.textContent = counter.val.toFixed(1) + 'M';
                    else if (isTopPercent) el.textContent = 'Top ' + Math.ceil(counter.val) + '%';
                    else el.textContent = Math.ceil(counter.val);
                }
            });
        });
        gsap.from(card.querySelectorAll('.tech-tag'), { opacity: 0, y: 20, duration: 0.5, ease: 'power2.out', stagger: 0.1, delay: 0.5 });
    }

    // --- Responsive Animations using matchMedia ---
    ScrollTrigger.matchMedia({
      "(min-width: 768px)": function() {
        gsap.set(orb.value, { opacity: 1 });
        gsap.to(orb.value, {
          motionPath: { path: path.value, align: path.value, alignOrigin: [0.5, 0.5], autoRotate: true },
          scrollTrigger: { trigger: main.value, start: 'top top', end: 'bottom bottom', scrub: 1.5 },
          ease: 'power1.inOut'
        });
        const buttons = self.selector('.case-study-btn');
        buttons.forEach(btn => {
            let boundingRect;
            btn.addEventListener('mouseenter', () => { boundingRect = btn.getBoundingClientRect(); });
            btn.addEventListener('mousemove', e => {
                const { clientX, clientY } = e;
                const x = clientX - boundingRect.left - boundingRect.width / 2;
                const y = clientY - boundingRect.top - boundingRect.height / 2;
                gsap.to(btn, { x: x * 0.3, y: y * 0.3, duration: 0.7, ease: 'power3.out' });
            });
            btn.addEventListener('mouseleave', () => {
                gsap.to(btn, { x: 0, y: 0, duration: 0.5, ease: 'elastic.out(1, 0.3)' });
            });
        });
      },
    });

  }, main.value);
});

onUnmounted(() => {
  if (ctx) {
    ctx.revert();
  }
});
</script>

<style>
/* No initial transform needed here anymore, it's set by GSAP */
</style>
