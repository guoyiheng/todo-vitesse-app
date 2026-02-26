<template>
  <nav class="fixed bottom-6 left-1/2 -translate-x-1/2 flex gap-2 px-4 py-2 rounded-full bg-white dark:bg-gray-800 border border-gray-200 dark:border-gray-700 shadow-md">
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
    <span class="text-[10px] px-2 py-0.5 rounded-full bg-gray-100 dark:bg-gray-700 text-gray-500 dark:text-gray-300">
      v0.0.1
    </span>
  </nav>
</template>
