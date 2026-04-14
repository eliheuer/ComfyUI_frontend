<script setup lang="ts">
/**
 * ModeToggleCell — wraps the existing WorkflowActionsDropdown that
 * provides the App ↔ Graph mode toggle, telemetered as the bento source.
 */
import WorkflowActionsDropdown from '@/components/common/WorkflowActionsDropdown.vue'
</script>

<template>
  <div class="mode-toggle-cell">
    <WorkflowActionsDropdown source="app_mode_toolbar" />
  </div>
</template>

<style scoped>
.mode-toggle-cell {
  display: flex;
  width: 100%;
  height: 100%;
  align-items: center;
  padding: 0 8px;
}

.mode-toggle-cell :deep(*) {
  /* Inherit cell sizing — the dropdown's own background is fine */
  pointer-events: auto;
}
</style>
