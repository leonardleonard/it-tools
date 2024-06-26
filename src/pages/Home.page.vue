<script setup lang="ts">
import { Heart } from '@vicons/tabler';
import { useHead } from '@vueuse/head';
import ColoredCard from '../components/ColoredCard.vue';
import ToolCard from '../components/ToolCard.vue';
import { useToolStore } from '@/tools/tools.store';
import { config } from '@/config';

import { onMounted } from 'vue';

import posthog from 'posthog-js'

const toolStore = useToolStore();

useHead({ title: 'IT Tools - Handy online tools for developers' });
const { t } = useI18n();


// add google analytics
declare global {
  interface Window {
    dataLayer: any[];
  }
}

const loadGoogleAnalytics = () => {
  const scriptUrl = 'https://www.googletagmanager.com/gtag/js?id=G-RKY35ZL41L';
  const script = document.createElement('script');
  script.async = true;
  script.src = scriptUrl;

  script.onload = () => {
    window.dataLayer = window.dataLayer || [];

    function gtag(...args: any[]) {
      window.dataLayer.push(...args);
    }

    gtag('js', new Date());
    gtag('config', 'G-RKY35ZL41L');
  };

  script.onerror = () => {
    console.error('Google Analytics script failed to load');
  };

  document.head.appendChild(script);
};

onMounted(() => {
  loadGoogleAnalytics();
});

posthog.init('phc_a2jr16vg3ffDSYqM01vLz0GlZKA09A8EsvaHpxox1Aw', { api_host: 'https://us.i.posthog.com' })
posthog.capture('page_view')
</script>


<template>
  <div class="pt-50px">
    <div class="grid-wrapper">
      <div v-if="config.showBanner" class="grid grid-cols-1 gap-12px lg:grid-cols-3 md:grid-cols-3 sm:grid-cols-2 xl:grid-cols-4">
        <ColoredCard :title="$t('home.follow.title')" :icon="Heart">
          {{ $t('home.follow.p1') }}
          <a
            href="https://github.com/CorentinTh/it-tools"
            rel="noopener"
            target="_blank"
            :aria-label="$t('home.follow.githubRepository')"
          >GitHub</a>
          {{ $t('home.follow.p2') }}
          <a
            href="https://twitter.com/ittoolsdottech"
            rel="noopener"
            target="_blank"
            :aria-label="$t('home.follow.twitterAccount')"
          >Twitter</a>.
          {{ $t('home.follow.thankYou') }}
          <n-icon :component="Heart" />
        </ColoredCard>
      </div>

      <transition name="height">
        <div v-if="toolStore.favoriteTools.length > 0">
          <h3 class="mb-5px mt-25px font-500 text-neutral-400">
            {{ $t('home.categories.favoriteTools') }}
          </h3>
          <div class="grid grid-cols-1 gap-12px lg:grid-cols-3 md:grid-cols-3 sm:grid-cols-2 xl:grid-cols-4">
            <ToolCard v-for="tool in toolStore.favoriteTools" :key="tool.name" :tool="tool" />
          </div>
        </div>
      </transition>

      <div v-if="toolStore.newTools.length > 0">
        <h3 class="mb-5px mt-25px font-500 text-neutral-400">
          {{ t('home.categories.newestTools') }}
        </h3>
        <div class="grid grid-cols-1 gap-12px lg:grid-cols-3 md:grid-cols-3 sm:grid-cols-2 xl:grid-cols-4">
          <ToolCard v-for="tool in toolStore.newTools" :key="tool.name" :tool="tool" />
        </div>
      </div>

      <h3 class="mb-5px mt-25px font-500 text-neutral-400">
        {{ $t('home.categories.allTools') }}
      </h3>
      <div class="grid grid-cols-1 gap-12px lg:grid-cols-3 md:grid-cols-3 sm:grid-cols-2 xl:grid-cols-4">
        <ToolCard v-for="tool in toolStore.tools" :key="tool.name" :tool="tool" />
      </div>
    </div>
  </div>
</template>

<style scoped lang="less">
.height-enter-active,
.height-leave-active {
  transition: all 0.5s ease-in-out;
  overflow: hidden;
  max-height: 500px;
}

.height-enter-from,
.height-leave-to {
  max-height: 42px;
  overflow: hidden;
  opacity: 0;
  margin-bottom: 0;
}
</style>
