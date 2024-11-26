<script setup lang="ts">
import { useFocusTrap } from '@vueuse/integrations/useFocusTrap'
import { nextTick, ref, watch } from 'vue'

const props = defineProps<{
  isOpen: boolean
}>()

const emits = defineEmits<{
  close: []
}>()

const titleInput = ref<HTMLInputElement | null>(null)

watch(
  () => props.isOpen,
  async (isOpen) => {
    if (isOpen) {
      await nextTick()
      activate()
      titleInput.value?.focus()
    } else {
      deactivate()
    }
  },
)

const modalElement = ref<HTMLDivElement | null>(null)

const { activate, deactivate } = useFocusTrap(modalElement)
</script>

<template>
  <Teleport to="body">
    <div
      class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center"
      role="dialog"
      aria-modal="true"
      ref="modalElement"
      v-if="isOpen"
      @keydown.esc="emits('close')"
      @click.self="emits('close')"
    >
      <div class="bg-white p-5 rounded mx-2 max-w-md w-full">
        <h2 class="text-xl font-bold mb-4">Add New Card</h2>
        <form>
          <input
            placeholder="Card Title"
            aria-label="Card Title"
            class="w-full p-2 mb-4 border rounded focus:ring-2 focus:outline-none"
            ref="titleInput"
          />

          <textarea
            :rows="3"
            aria-label="Card Description"
            placeholder="Description"
            class="w-full p-2 mb-4 border rounded resize-none focus:ring-2 focus:outline-none"
          ></textarea>

          <div class="flex justify-end gap-2">
            <button
              class="bg-gray-100 hover:bg-gray-200 text-black px-4 py-2 rounded"
              type="button"
              @click="emits('close')"
            >
              Cancel
            </button>

            <button
              class="bg-blue-500 hover:bg-blue-600 text-white px-4 py-2 rounded"
              type="button"
              @click="emits('close')"
            >
              Save
            </button>
          </div>
        </form>
      </div>
    </div>
  </Teleport>
</template>
