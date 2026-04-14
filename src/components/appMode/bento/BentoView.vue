<script setup lang="ts">
/**
 * BentoView — App Mode runtime view rebuilt around the bento grid.
 *
 * Prototype scope: hard-coded cell layout matching design/mockups/grid-system-001.png.
 * System-pinned cells port the existing AppModeToolbar functionality:
 *   - Mode toggle (App ↔ Graph dropdown)
 *   - Builder (hammer)
 *   - Share (cloud + sharing-flag only)
 *   - Assets (sidebar tab toggle)
 *   - Apps (sidebar tab toggle)
 *   - Help (placeholder)
 *   - Run (placeholder)
 *
 * Stub input/output cells are visible boxes for now; real widget/output
 * rendering swaps in next.
 */
import { computed } from 'vue'
import { useI18n } from 'vue-i18n'
import { storeToRefs } from 'pinia'

import BentoGrid from './BentoGrid.vue'
import type { BentoCellPlacement } from './BentoGrid.vue'
import IconCell from './cells/IconCell.vue'
import RunCell from './cells/RunCell.vue'
import HelpCell from './cells/HelpCell.vue'
import ModeToggleCell from './cells/ModeToggleCell.vue'

import { useAppMode } from '@/composables/useAppMode'
import { useAppModeStore } from '@/stores/appModeStore'
import { useCommandStore } from '@/stores/commandStore'
import { useErrorHandling } from '@/composables/useErrorHandling'
import { useFeatureFlags } from '@/composables/useFeatureFlags'
import { useWorkspaceStore } from '@/stores/workspaceStore'
import { isCloud } from '@/platform/distribution/types'
import {
  openShareDialog,
  prefetchShareDialog
} from '@/platform/workflow/sharing/composables/lazyShareDialog'

const props = defineProps<{
  /** Click handler for the Run cell. Provided by LinearView. */
  onRunClick?: (e: Event) => void | Promise<void>
}>()

const { t } = useI18n()
const { enableAppBuilder } = useAppMode()
const appModeStore = useAppModeStore()
const { enterBuilder } = appModeStore
const { hasNodes } = storeToRefs(appModeStore)
const commandStore = useCommandStore()
const workspaceStore = useWorkspaceStore()
const { toastErrorHandler } = useErrorHandling()
const { flags } = useFeatureFlags()

const isAssetsActive = computed(
  () => workspaceStore.sidebarTab.activeSidebarTab?.id === 'assets'
)
const isAppsActive = computed(
  () => workspaceStore.sidebarTab.activeSidebarTab?.id === 'apps'
)

const showShare = computed(() => isCloud && flags.workflowSharingEnabled)

function openAssets() {
  void commandStore.execute('Workspace.ToggleSidebarTab.assets')
}
function showApps() {
  void commandStore.execute('Workspace.ToggleSidebarTab.apps')
}
function openShare() {
  openShareDialog().catch(toastErrorHandler)
}

// Layout matches design/mockups/grid-system-001.png:
// - Col 1 holds a vertical stack of utility icon cells
// - Col 2-3 row 1 hosts the App↔Graph mode toggle
// - Col 1 row 8 is the Help cell
// - Col 11-12 row 8 is the Run cell
// - Stub input/output cells fill the remaining space for visual demo
const cells = computed<BentoCellPlacement[]>(() => {
  const out: BentoCellPlacement[] = []

  // System-pinned utility column (col 1)
  let row = 1
  if (enableAppBuilder.value) {
    out.push({ id: 'builder', col: 1, row: row++, kind: 'system-builder' })
  }
  if (showShare.value) {
    out.push({ id: 'share', col: 1, row: row++, kind: 'system-share' })
  }
  out.push({ id: 'assets', col: 1, row: row++, kind: 'system-assets' })
  out.push({ id: 'apps', col: 1, row: row++, kind: 'system-apps' })

  // Mode toggle (col 2-3, row 1)
  out.push({
    id: 'mode-toggle',
    col: 2,
    row: 1,
    colSpan: 2,
    kind: 'system-mode-toggle'
  })

  // Help (bottom-left) — row: -2 anchors to last row regardless of grid size
  out.push({ id: 'help', col: 1, row: -2, kind: 'system-help' })

  // Run (bottom-right) — both col and row anchor to end
  out.push({ id: 'run', col: -3, row: -2, colSpan: 2, kind: 'system-run' })

  // Stub input/output cells removed — BentoGrid's fillEmpty prop now
  // paints ghost cells across every unoccupied grid position, which
  // visualizes the full grid structure without hardcoded layout.
  // Real input/output cells will replace ghosts in the next milestone.

  return out
})
</script>

<template>
  <div class="bento-view">
    <BentoGrid :cells="cells" fill-empty>
      <template v-for="cell in cells" :key="cell.id" #[cell.id]>
        <IconCell
          v-if="cell.kind === 'system-builder'"
          icon="icon-[lucide--hammer]"
          :label="t('linearMode.appModeToolbar.appBuilder')"
          :disabled="!hasNodes"
          :on-activate="enterBuilder"
        />
        <IconCell
          v-else-if="cell.kind === 'system-share'"
          icon="icon-[lucide--send]"
          :label="t('actionbar.shareTooltip')"
          :on-activate="openShare"
          @pointerenter="prefetchShareDialog"
        />
        <IconCell
          v-else-if="cell.kind === 'system-assets'"
          icon="icon-[comfy--image-ai-edit]"
          :label="t('sideToolbar.mediaAssets.title')"
          :active="isAssetsActive"
          :on-activate="openAssets"
        />
        <IconCell
          v-else-if="cell.kind === 'system-apps'"
          icon="icon-[lucide--panels-top-left]"
          :label="t('linearMode.appModeToolbar.apps')"
          :active="isAppsActive"
          :on-activate="showApps"
        />
        <ModeToggleCell v-else-if="cell.kind === 'system-mode-toggle'" />
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
