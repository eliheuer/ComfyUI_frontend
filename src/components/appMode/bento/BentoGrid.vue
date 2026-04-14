<script setup lang="ts">
/**
 * BentoGrid — grid layout primitive for App Mode.
 *
 * Renders cells on a 12-column responsive grid where 1×1 cells are square
 * (row height = column width). Empty cells are invisible at runtime; only
 * cells with content render as visible containers.
 *
 * Cells snap to integer column/row positions and integer span values.
 */
import { computed } from 'vue'

export interface BentoCellPlacement {
  /** Stable identifier for the cell. */
  id: string
  /** 1-indexed grid column. */
  col: number
  /** 1-indexed grid row. */
  row: number
  /** Number of columns the cell spans. Default 1. */
  colSpan?: number
  /** Number of rows the cell spans. Default 1. */
  rowSpan?: number
  /** Optional semantic kind, surfaced as a data attribute for testing. */
  kind?: string
}

const props = withDefaults(
  defineProps<{
    /** Total grid columns. Defaults to 12. */
    cols?: number
    /** Cell placements. Each cell's <slot> name is its id. */
    cells: BentoCellPlacement[]
    /** Outer margin around the grid (px). Defaults to 16. */
    outerPadding?: number
    /** Gutter between cells (px). Defaults to 8. */
    gutter?: number
  }>(),
  {
    cols: 12,
    outerPadding: 16,
    gutter: 8
  }
)

const gridStyle = computed(() => ({
  padding: `${props.outerPadding}px`,
  gap: `${props.gutter}px`,
  gridTemplateColumns: `repeat(${props.cols}, minmax(0, 1fr))`,
  // Square 1×1 cells: row height equals column width.
  // Computed via container query on the inline size.
  gridAutoRows: `calc((100cqi - ${props.outerPadding * 2}px - ${
    (props.cols - 1) * props.gutter
  }px) / ${props.cols})`
}))

function cellStyle(cell: BentoCellPlacement) {
  const colSpan = cell.colSpan ?? 1
  const rowSpan = cell.rowSpan ?? 1
  return {
    gridColumn: `${cell.col} / span ${colSpan}`,
    gridRow: `${cell.row} / span ${rowSpan}`
  }
}
</script>

<template>
  <div class="bento-grid" :style="gridStyle" data-testid="bento-grid">
    <div
      v-for="cell in cells"
      :key="cell.id"
      class="bento-cell"
      :style="cellStyle(cell)"
      :data-cell-id="cell.id"
      :data-cell-kind="cell.kind"
    >
      <slot :name="cell.id" :cell="cell" />
    </div>
  </div>
</template>

<style scoped>
.bento-grid {
  container-type: inline-size;
  display: grid;
  width: 100%;
  height: 100%;
  box-sizing: border-box;
  background-color: var(--p-content-background, #1a1a1a);
}

.bento-cell {
  /* Subtle dark-on-darker fill matches mockup. Cells with no slot content
     stay empty (no background) so the canvas reads as composed, not gridded. */
  display: flex;
  min-width: 0;
  min-height: 0;
}

.bento-cell:has(> :not(:empty)) {
  background-color: var(--p-surface-800, #2a2a2a);
  border-radius: 4px;
}
</style>
