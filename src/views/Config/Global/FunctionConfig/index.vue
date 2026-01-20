<script setup lang='ts'>
import { storeToRefs } from 'pinia'
import { ref, watch } from 'vue'
import { useI18n } from 'vue-i18n'
import GridWaterfall from '@/components/Waterfall/index.vue'
import useStore from '@/store'

const { t } = useI18n()
const globalConfig = useStore().globalConfig
const prizeConfig = useStore().prizeConfig
const personConfig = useStore().personConfig

const {
    getDefiniteTime: definiteTime,
    getWinMusic: isWinMusic,
    getIsTestMode: isTestMode,
} = storeToRefs(globalConfig)

const definiteTimeValue = ref(structuredClone(definiteTime.value))
const isWinMusicValue = ref(structuredClone(isWinMusic.value))
const isTestModeValue = ref(isTestMode.value)
const showTestModeDialog = ref(false)
const pendingTestModeValue = ref(false)

watch(definiteTimeValue, () => {
    globalConfig.setDefiniteTime(definiteTimeValue.value)
})

watch(isWinMusicValue, () => {
    globalConfig.setIsPlayWinMusic(isWinMusicValue.value)
})

function handleTestModeClick() {
    pendingTestModeValue.value = !isTestModeValue.value
    showTestModeDialog.value = true
}

function confirmTestMode() {
    isTestModeValue.value = pendingTestModeValue.value
    globalConfig.setIsTestMode(isTestModeValue.value)
    if (isTestModeValue.value) {
        prizeConfig.startTestMode()
        personConfig.startTestMode()
    }
    else {
        prizeConfig.endTestMode()
        personConfig.endTestMode()
    }
    showTestModeDialog.value = false
}

function cancelTestMode() {
    showTestModeDialog.value = false
}
</script>

<template>
  <div class="w-4/5 flex flex-col gap-4">
    <h2>{{ t('sidebar.functionSetting') }}</h2>
    <GridWaterfall>
      <fieldset class="p-4 border text-setting fieldset bg-base-200 border-base-300 rounded-box w-xs pb-10">
        <legend class="fieldset-legend">
          {{ t('table.functionSetting') }}
        </legend>

        <label class="flex flex-row items-center form-control">
          <div class="">
            <div class="label flex flex-col justify-start items-start">
              <label class="label">
                <span class="label-text text-left">{{ t('table.timedStop') }}</span>
                <div class="tooltip" :data-tip="t('tooltip.timedStop')">
                  <button class="btn btn-circle h-4 hover:bg-base-300">
                    ?
                  </button>
                </div>
              </label>
              <input
                v-model="definiteTimeValue" type="number" :placeholder="t('placeHolder.timedStop')"
                class="w-full max-w-xs input input-bordered"
              >
            </div>
          </div>
        </label>
        <div class="flex items-center justify-between w-full max-w-xs gap-2 mb-3 form-control">
          <div class="label">
            <span class="label-text">{{ t('table.playWinMusic') }}</span>
          </div>
          <input
            v-model="isWinMusicValue"
            type="checkbox" class="border-solid checkbox checkbox-secondary border"
          >
        </div>
        <div class="flex items-center justify-between w-full max-w-xs gap-2 mb-3 form-control">
          <div class="label">
            <span class="label-text">{{ t('table.testMode') }}</span>
            <div class="tooltip" :data-tip="t('tooltip.testMode')">
              <button class="btn btn-circle h-4 hover:bg-base-300">
                ?
              </button>
            </div>
          </div>
          <input
            :checked="isTestModeValue"
            type="checkbox" class="border-solid checkbox checkbox-warning border"
            @click.prevent="handleTestModeClick"
          >
        </div>
      </fieldset>
    </GridWaterfall>

    <!-- Test Mode Confirmation Dialog -->
    <dialog class="modal" :class="{ 'modal-open': showTestModeDialog }">
      <div class="modal-box">
        <h3 class="font-bold text-lg">{{ t('dialog.titleTip') }}</h3>
        <p class="py-4 whitespace-pre-line">
          {{ pendingTestModeValue ? t('dialog.testModeEnable') : t('dialog.testModeDisable') }}
        </p>
        <div class="modal-action">
          <button class="btn" @click="cancelTestMode">
            {{ t('button.cancel') }}
          </button>
          <button class="btn btn-warning" @click="confirmTestMode">
            {{ t('button.confirm') }}
          </button>
        </div>
      </div>
      <form method="dialog" class="modal-backdrop" @click="cancelTestMode">
        <button>close</button>
      </form>
    </dialog>
  </div>
</template>

<style lang='scss' scoped></style>
