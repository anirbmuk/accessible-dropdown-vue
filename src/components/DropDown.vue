<template>
  <div class="select-container" ref="selectContainer">
    <div select @click="toggle" @keydown.space="toggle" tabindex="1" role="list" ref="list">
      <span>{{ modelValue || 'Select Month' }}</span>
      <span>&#x25BC;</span>
    </div>
    <div v-show="open" options role="listbox" tabindex="-1" :id="listboxId">
      <button
        v-if="allowEmpty"
        option
        role="listitem"
        :class="{ selected: !modelValue }"
        @click="select('')"
        @keydown.esc="open = false"
        @keydown="keyboardNavigationWithinOptions"
      >
        {{ '' }}
      </button>
      <template v-for="option of options" :key="option">
        <button
          option
          role="listitem"
          :class="{ selected: modelValue === option }"
          @click="select(option)"
          @keydown.esc="open = false"
          @keydown="keyboardNavigationWithinOptions"
        >
          {{ option }}
        </button>
      </template>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, ref, watch, type PropType } from 'vue'
import { onClickOutside } from '@vueuse/core'

const KEYCODES = {
  ARROW_UP: 'ArrowUp',
  ARROW_DOWN: 'ArrowDown',
  ENTER: 'Enter',
  SPACE: 'Space'
} satisfies Record<string, string>

const BG_GRAY = 'bg-gray'

defineProps({
  modelValue: {
    type: String,
    default: ''
  },
  allowEmpty: {
    type: Boolean,
    default: false
  },
  options: {
    type: Array as PropType<string[]>,
    required: true
  }
})
const emit = defineEmits<{
  (e: 'update:model-value', value: string): void
}>()

const selectContainer = ref<HTMLDivElement | undefined>()
const list = ref<HTMLDivElement | undefined>()
const listboxId = computed(() => `${Math.floor(Math.random() * 100_000)}_listboxid`)

const open = ref(false)

onClickOutside(selectContainer, () => (open.value = false))

const toggle = (event: Event) => {
  event.preventDefault()
  open.value = !open.value
  if (open.value) {
    const options = document.getElementById(listboxId.value)?.children
    const button = options?.item(0) as HTMLButtonElement
    button.classList.add(BG_GRAY)
    setTimeout(() => button.focus())
  }
}

const keyboardNavigationWithinOptions = (event: KeyboardEvent) => {
  if (![KEYCODES.ARROW_UP, KEYCODES.ARROW_DOWN].includes(event.code)) {
    return
  }
  event.preventDefault()
  const options = document.getElementById(listboxId.value)?.children
  const first = options?.item(0)
  const last = options?.item(options.length - 1)
  const target = event.target as HTMLButtonElement
  target.classList.remove(BG_GRAY)

  if (event.code === KEYCODES.ARROW_DOWN) {
    let next = target.nextElementSibling as HTMLButtonElement
    if (!next) {
      next = first as HTMLButtonElement
    }
    next?.focus()
    next?.classList.add(BG_GRAY)
  } else {
    let previous = target.previousElementSibling as HTMLButtonElement
    if (!previous) {
      previous = last as HTMLButtonElement
    }
    previous?.focus()
    previous?.classList.add(BG_GRAY)
  }
}

watch(open, (value) => {
  if (!value) {
    clearFocus()
    setTimeout(() => list.value?.focus())
  }
})

const clearFocus = () => {
  const options = document.getElementById(listboxId.value)?.children
  if (options) {
    for (const node of options) {
      node?.classList.remove(BG_GRAY)
    }
  }
}

const select = (option: string) => {
  emit('update:model-value', option)
  open.value = false
}

defineOptions({
  name: 'DropDown'
})
</script>

<style scoped>
.rotate-down {
  transform: rotate(180deg);
}
.bg-gray {
  background-color: lightgray;
}
.selected {
  background-color: rgb(18, 18, 18);
  color: rgb(255, 255, 255);
}
</style>
