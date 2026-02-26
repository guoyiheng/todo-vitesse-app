<script setup lang="ts">
import { appVersion } from '../constants/version'
</script>

<template>
  <nav class="px-4 py-2 border border-gray-200 rounded-full bg-white flex gap-2 shadow-md bottom-6 left-1/2 fixed dark:border-gray-700 dark:bg-gray-800 -translate-x-1/2">
    <button class="icon-btn" @click="toggleDark()">
      <div i-carbon-sun dark:i-carbon-moon />
    </button>
    <a
      i-carbon-logo-github icon-btn
      rel="noreferrer"
      href="https://github.com/antfu-collective/vitesse-lite"
      target="_blank"
      title="GitHub"
    />
    <span class="text-[10px] text-gray-500 px-2 py-0.5 rounded-full bg-gray-100 dark:text-gray-300 dark:bg-gray-700">
      {{ appVersion }}
    </span>
  </nav>
</template>
