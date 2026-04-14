<script setup lang="ts">
 /**
 * AppCell — system-pinned cell. Will eventually surface app picker /
 * current app name. Prototype: static label.
 */
</script>

<template>
  <!-- eslint-disable-next-line @intlify/vue-i18n/no-raw-text -->
  <div class="app-cell" data-testid="bento-app-cell">App</div>
</template>

<style scoped>
.app-cell {
  display: flex;
  width: 100%;
  height: 100%;
  align-items: center;
  padding: 0 12px;
  color: var(--p-text-color, #fafafa);
  font-size: 14px;
  font-weight: 500;
}
</style>
