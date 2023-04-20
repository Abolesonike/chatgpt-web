<script setup lang='ts'>
import { computed } from 'vue'
import {NButton, NModal, useMessage } from 'naive-ui'
import { SvgIcon } from '@/components/common'
import { t } from '@/locales'
const ms = useMessage()
interface Props {
  visible: boolean
}

interface Emit {
  (e: 'update:visible', visible: boolean): void
}

const props = defineProps<Props>()

const emit = defineEmits<Emit>()

const show = computed({
  get() {
    return props.visible
  },
  set(visible: boolean) {
    emit('update:visible', visible)
  },
})

function handleImportButtonClick(): void {
  const fileInput = document.getElementById('fileInput') as HTMLElement
  if (fileInput)
    fileInput.click()
}

function importData(event: Event): void {
  const target = event.target as HTMLInputElement
  if (!target || !target.files)
    return

  const file: File = target.files[0]
  if (!file)
    return

  const reader: FileReader = new FileReader()
  reader.onload = () => {
    try {
      const data = JSON.parse(reader.result as string)
      localStorage.setItem('chatStorage', JSON.stringify(data))
      ms.success(t('common.success'))
      location.reload()
    }
    catch (error) {
      ms.error(t('common.invalidFileFormat'))
    }
  }
  reader.readAsText(file)
}
</script>

<template>
  <NModal v-model:show="show" :auto-focus="false" preset="card" style="width: 95%; max-width: 640px">
    <div>
      <div>
        <input id="fileInput" type="file" style="display:none" @change="importData">
        <NButton size="small" @click="handleImportButtonClick">
          <template #icon>
            <SvgIcon icon="ri:upload-2-fill" />
          </template>
          {{ $t('common.import') }}
        </NButton>
      </div>
      <div style="margin-top: 15px">
        <textarea rows="15" style="width: 100%" @input="handleinput" />
      </div>
    </div>
  </NModal>
</template>
