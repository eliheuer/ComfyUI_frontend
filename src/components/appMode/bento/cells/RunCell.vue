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
    <i class="run-cell__icon icon-[lucide--play]" />
    {{ t('menu.run') }}
  </button>
</template>

<style scoped>
.run-cell {
  display: flex;
  width: 100%;
  height: 100%;
  align-items: center;
  justify-content: center;
  gap: 6px;
  border: none;
  border-radius: 4px;
  /* GitHub Primer success-button green */
  background-color: #2da44e;
  color: #ffffff;
  font-size: 14px;
  font-weight: 500;
  cursor: pointer;
  transition: background-color 150ms cubic-bezier(0.32, 0.72, 0, 1);
}

.run-cell:hover {
  background-color: #2c974b;
}

.run-cell:active {
  background-color: #298e46;
}

.run-cell__icon {
  width: 16px;
  height: 16px;
}
</style>
