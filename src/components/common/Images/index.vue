<script setup lang='ts'>
import { computed, ref } from 'vue'
import { NButton, NInput, NModal, NSpin, useMessage } from 'naive-ui'
import axios from 'axios'
import { SvgIcon } from '@/components/common'
const props = defineProps<Props>()
const emit = defineEmits<Emit>()
const ms = useMessage()
interface Props {
  visible: boolean
}

interface Emit {
  (e: 'update:visible', visible: boolean): void
}

const show = computed({
  get() {
    return props.visible
  },
  set(visible: boolean) {
    emit('update:visible', visible)
  },
})

const initText = ref('')
const loading = ref(false)

function handleImportButtonClick(): void {
  const fileInput = document.getElementById('fileInput') as HTMLElement
  if (fileInput)
    fileInput.click()
}

function importData(event: Event): void {
  loading.value = true
  const target = event.target as HTMLInputElement
  if (!target || !target.files)
    return

  const file: File = target.files[0]
  if (!file)
    return

  const formData = new FormData()
  formData.append('file', file)
  axios({
    url: 'http://localhost:3003',
    method: 'post',
    headers: {
      'Content-Type': 'multipart/form-data',
    },
    data: formData,
  }).then((resp) => {
    initText.value = resp.data
    loading.value = false
  })
}
</script>

<template>
  <NModal v-model:show="show" :auto-focus="false" preset="card" style="width: 95%; max-width: 640px">
    <div>
      <div>
        <input id="fileInput" type="file" style="display:none" multiple @change="importData">
        <NButton size="small" @click="handleImportButtonClick">
          <template #icon>
            <SvgIcon icon="ri:upload-2-fill" />
          </template>
          {{ $t('common.import') }}
        </NButton>
      </div>
      <NSpin :show="loading">
        <div style="margin-top: 15px">
          <div class="flex-1">
            <NInput v-model:value="initText" type="textarea" :autosize="{ minRows: 15, maxRows: 30 }" />
          </div>
        </div>
      </Nspin>
    </div>
  </NModal>
</template>
