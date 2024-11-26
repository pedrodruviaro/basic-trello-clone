<script setup lang="ts">
import { useFocusTrap } from '@vueuse/integrations/useFocusTrap'
import { nextTick, ref, watch } from 'vue'
import type { Card } from '@/types'

const props = defineProps<{
  isOpen: boolean
  card: Card | null
  mode: 'add' | 'edit'
}>()

const emits = defineEmits<{
  close: []
  save: [card: Card]
}>()

const titleInput = ref<HTMLInputElement | null>(null)
const modalElement = ref<HTMLDivElement | null>(null)
const { activate, deactivate } = useFocusTrap(modalElement)

const localCard = ref<Card>({ id: 0, title: '', description: '' })

watch(
  () => props.card,
  (newCard) => {
    if (newCard) {
      localCard.value = { ...newCard }
    } else {
      localCard.value = { id: 0, title: '', description: '' }
    }
  },
  { immediate: true },
)

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
        <h2 class="text-xl font-bold mb-4">{{ mode === 'add' ? 'Add New Card' : 'Edit Card' }}</h2>
        <form>
          <input
            placeholder="Card Title"
            aria-label="Card Title"
            class="w-full p-2 mb-4 border rounded focus:ring-2 focus:outline-none"
            ref="titleInput"
            v-model="localCard.title"
          />

          <textarea
            aria-label="Card Description"
            placeholder="Description"
            class="w-full p-2 mb-4 border rounded resize-none focus:ring-2 focus:outline-none"
            :rows="3"
            v-model="localCard.description"
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
              @click="emits('save', localCard)"
            >
              {{ mode === 'add' ? 'Add' : 'Save' }}
            </button>
          </div>
        </form>
      </div>
    </div>
  </Teleport>
</template>
