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
    /** Total grid rows. Defaults to 8. The grid always fits this many
     *  rows in the visible viewport — never scrolls. */
    rows?: number
    /** Cell placements. Each cell's <slot> name is its id. */
    cells: BentoCellPlacement[]
    /** Outer margin around the grid (px). Defaults to 16. */
    outerPadding?: number
    /** Gutter between cells (px). Defaults to 8. */
    gutter?: number
    /** Maximum 1×1 cell side length (px). On large viewports the cell
     *  would otherwise grow past button-scale; this caps it. */
    maxCellSize?: number
  }>(),
  {
    cols: 12,
    rows: 8,
    outerPadding: 16,
    gutter: 8,
    maxCellSize: 48
  }
)

const gridStyle = computed(() => {
  // Square 1×1 cells: side length = the smaller of (fit-width, fit-height, cap).
  // cqi/cqb are container query units so the grid always fits the viewport;
  // the cap keeps cells near button-scale on large screens rather than
  // inflating them to fill space. Whichever dimension has slack becomes
  // letterbox margin.
  const widthFit = `calc((100cqi - ${props.outerPadding * 2}px - ${
    (props.cols - 1) * props.gutter
  }px) / ${props.cols})`
  const heightFit = `calc((100cqb - ${props.outerPadding * 2}px - ${
    (props.rows - 1) * props.gutter
  }px) / ${props.rows})`
  const cellSize = `min(${widthFit}, ${heightFit}, ${props.maxCellSize}px)`

  return {
    padding: `${props.outerPadding}px`,
    gap: `${props.gutter}px`,
    gridTemplateColumns: `repeat(${props.cols}, ${cellSize})`,
    gridAutoRows: cellSize,
    // Center the grid in the container — letterbox on the slack axis.
    justifyContent: 'center',
    alignContent: 'center'
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
  /* size (vs inline-size) gives us cqb units for height-aware sizing */
  container-type: size;
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
