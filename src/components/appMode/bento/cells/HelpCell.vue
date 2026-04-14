<script setup lang="ts">
/**
 * HelpCell — system-pinned cell. Will eventually open help / docs.
 * Prototype: static label.
 */
import { useI18n } from 'vue-i18n'

const { t } = useI18n()
</script>

<template>
  <button
    type="button"
    class="help-cell"
    :aria-label="t('g.help')"
    data-testid="bento-help-cell"
  >
    <!-- eslint-disable-next-line @intlify/vue-i18n/no-raw-text -->
    ?
  </button>
</template>

<style scoped>
.help-cell {
  display: flex;
  width: 100%;
  height: 100%;
  align-items: center;
  justify-content: center;
  border: none;
  background: transparent;
  color: var(--p-text-color, #fafafa);
  font-size: 16px;
  font-weight: 500;
  cursor: pointer;
}

.help-cell:hover {
  color: #ffffff;
}
</style>
