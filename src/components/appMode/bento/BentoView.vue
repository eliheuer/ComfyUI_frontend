<script setup lang="ts">
/**
 * BentoView — App Mode runtime view rebuilt around the bento grid.
 *
 * Prototype scope: hard-coded cell layout matching design/mockups/grid-system-001.png.
 * System-pinned cells (App, Help, Run) plus stub input/output cells. Real
 * widget/output rendering will swap into these cells in a follow-up milestone.
 */
import { computed } from 'vue'

import BentoGrid from './BentoGrid.vue'
import type { BentoCellPlacement } from './BentoGrid.vue'
import RunCell from './cells/RunCell.vue'
import AppCell from './cells/AppCell.vue'
import HelpCell from './cells/HelpCell.vue'

const props = defineProps<{
  /** Click handler for the Run cell. Provided by LinearView for now. */
  onRunClick?: (e: Event) => void | Promise<void>
}>()

// Hard-coded layout matching the mockup. Will become data-driven once
// the schema fields (col/row/colSpan/rowSpan from the schema MVP) are
// wired through the workflow's linearData on a per-card basis.
const cells = computed<BentoCellPlacement[]>(() => [
  // System-pinned utility cells
  { id: 'app', col: 2, row: 1, colSpan: 2, rowSpan: 1, kind: 'system-app' },
  { id: 'help', col: 1, row: 8, colSpan: 1, rowSpan: 1, kind: 'system-help' },
  { id: 'run', col: 11, row: 8, colSpan: 2, rowSpan: 1, kind: 'system-run' },

  // Stub input cells (placeholder — will be widget cells)
  { id: 'input-1', col: 1, row: 1, colSpan: 1, rowSpan: 1, kind: 'input' },
  { id: 'input-2', col: 1, row: 2, colSpan: 1, rowSpan: 1, kind: 'input' },
  { id: 'input-3', col: 1, row: 3, colSpan: 1, rowSpan: 1, kind: 'input' },
  { id: 'input-4', col: 1, row: 4, colSpan: 1, rowSpan: 1, kind: 'input' },
  { id: 'input-5', col: 1, row: 5, colSpan: 1, rowSpan: 2, kind: 'input' },
  { id: 'input-6', col: 2, row: 2, colSpan: 2, rowSpan: 3, kind: 'input' },
  { id: 'input-7', col: 4, row: 1, colSpan: 4, rowSpan: 1, kind: 'input' },
  { id: 'input-8', col: 4, row: 2, colSpan: 1, rowSpan: 1, kind: 'input' },
  { id: 'input-9', col: 5, row: 2, colSpan: 1, rowSpan: 1, kind: 'input' },
  { id: 'input-10', col: 6, row: 2, colSpan: 1, rowSpan: 1, kind: 'input' },
  { id: 'input-11', col: 7, row: 2, colSpan: 1, rowSpan: 1, kind: 'input' },
  { id: 'input-12', col: 4, row: 3, colSpan: 4, rowSpan: 3, kind: 'input' },
  { id: 'input-13', col: 2, row: 6, colSpan: 1, rowSpan: 1, kind: 'input' },
  { id: 'input-14', col: 3, row: 6, colSpan: 1, rowSpan: 1, kind: 'input' },
  { id: 'input-15', col: 2, row: 7, colSpan: 6, rowSpan: 2, kind: 'input' },

  // Stub output cells (placeholder — will be media output cells)
  { id: 'output-hero', col: 8, row: 1, colSpan: 5, rowSpan: 5, kind: 'output' },
  { id: 'output-2', col: 8, row: 6, colSpan: 5, rowSpan: 1, kind: 'output' },
  { id: 'output-3', col: 8, row: 7, colSpan: 5, rowSpan: 1, kind: 'output' },
  { id: 'output-4', col: 8, row: 8, colSpan: 3, rowSpan: 1, kind: 'output' }
])
</script>

<template>
  <div class="bento-view">
    <BentoGrid :cells="cells">
      <template v-for="cell in cells" :key="cell.id" #[cell.id]>
        <AppCell v-if="cell.kind === 'system-app'" />
        <HelpCell v-else-if="cell.kind === 'system-help'" />
        <RunCell
          v-else-if="cell.kind === 'system-run'"
          :on-click="props.onRunClick"
        />
        <div v-else class="bento-stub" :data-stub-kind="cell.kind" />
      </template>
    </BentoGrid>
  </div>
</template>

<style scoped>
.bento-view {
  width: 100%;
  height: 100%;
  background-color: var(--p-content-background, #1a1a1a);
}

.bento-stub {
  width: 100%;
  height: 100%;
}
</style>
