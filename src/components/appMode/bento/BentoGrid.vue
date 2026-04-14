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
    /** 1×1 cell side length (px). Fixed — grid auto-fills viewport at this size. */
    cellSize?: number
    /** Cell placements. Each cell's <slot> name is its id.
     *  col/row accept negative values as CSS Grid end-anchored indices
     *  (-1 = last track, -2 = second-to-last, etc.). */
    cells: BentoCellPlacement[]
    /** Outer margin around the grid (px). Defaults to gutter value. */
    outerPadding?: number
    /** Gutter between cells (px). */
    gutter?: number
  }>(),
  {
    cellSize: 48,
    gutter: 8,
    outerPadding: 8
  }
)

const gridStyle = computed(() => {
  // auto-fill creates as many tracks as fit the container at cellSize,
  // so the grid grows/shrinks with the viewport. Cells placed at negative
  // indices (col: -2, row: -1 etc.) anchor to the right/bottom.
  return {
    padding: `${props.outerPadding}px`,
    gap: `${props.gutter}px`,
    gridTemplateColumns: `repeat(auto-fill, ${props.cellSize}px)`,
    gridTemplateRows: `repeat(auto-fill, ${props.cellSize}px)`
  }
})

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
  display: grid;
  width: 100%;
  height: 100%;
  box-sizing: border-box;
  background-color: var(--p-content-background, #1a1a1a);
  overflow: hidden;
}

.bento-cell {
  /* Subtle dark-on-darker fill matches mockup. Cells with no slot content
     stay empty (no background) so the canvas reads as composed, not gridded. */
  display: flex;
  min-width: 0;
  min-height: 0;
}

/* Any cell with rendered slot content gets the subtle fill.
   Truly empty cells (no slot content) have no children → no background,
   keeping the runtime canvas composed rather than gridded. */
.bento-cell:has(> *) {
  background-color: var(--p-surface-800, #2a2a2a);
  border-radius: 4px;
}
</style>
