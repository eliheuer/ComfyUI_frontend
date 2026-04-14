<script setup lang="ts">
/**
 * RunCell — system-pinned cell containing the Run action.
 * Prototype: minimal styling, accent fill, sentence-case label.
 */
import { useI18n } from 'vue-i18n'

const props = defineProps<{
  onClick?: (e: Event) => void | Promise<void>
}>()

const { t } = useI18n()

function handleClick(e: Event) {
  void props.onClick?.(e)
}
</script>

<template>
  <button
    type="button"
    class="run-cell"
    data-testid="bento-run-cell"
    @click="handleClick"
  >
    {{ t('menu.run') }}
  </button>
</template>

<style scoped>
.run-cell {
  width: 100%;
  height: 100%;
  border: none;
  border-radius: 4px;
  background-color: #2eb27c;
  color: #0a0a0a;
  font-size: 14px;
  font-weight: 500;
  cursor: pointer;
  transition: background-color 150ms cubic-bezier(0.32, 0.72, 0, 1);
}

.run-cell:hover {
  background-color: #3fc28d;
}

.run-cell:active {
  background-color: #28a06f;
}
</style>
