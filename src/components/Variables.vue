<template>
  <div class="variables-block flex flex-col">
    <div
      class="flex justify-between flex-shrink-0 h-10 items-center px-3 group cursor-pointer border-t border-gray-200 dark:border-gray-750"
      @click="toggle"
    >
      <div
        class="flex items-center flex-shrink-0 text-gray-550 dark:text-gray-600 group-hover:text-gray-800 dark:group-hover:text-gray-400"
      >
        <span class="font-bold uppercase">
          {{ entry.type === 'GQL' ? 'Variables' : 'Body' }}
        </span>
        <ChevronDown v-if="show" class="h-5" />
        <ChevronUp v-else class="h-5" />
      </div>
      <div class="flex items-center overflow-hidden space-x-1">
        <div
          v-if="entry.type !== 'GQL'"
          class="whitespace-nowrap overflow-auto hide-scrollbar ml-2 text-gray-500 dark:text-gray-600"
        >
          {{ entry.request.mimeType }}
        </div>
        <CopyButton :value="params || variables || data" />
      </div>
    </div>
    <div v-if="entry" class="relative flex-grow overflow-hidden">
      <Scroll v-if="params">
        <Params :items="params" class="px-3 pb-3" />
      </Scroll>
      <codemirror v-else :value="variables || body" class="ml-1 h-full" />
    </div>
  </div>
</template>

<script>
import Params from '@/components/Params'
import ChevronUp from '@/assets/chevron-up.svg'
import ChevronDown from '@/assets/chevron-down.svg'

const HEIGHT_COLLAPSED = 2.5 * 16 // 2.5rem

export default {
  components: {
    Params,
    ChevronUp,
    ChevronDown,
  },
  props: {
    entry: {
      type: Object,
    },
  },
  computed: {
    params() {
      return this.entry.request.params
    },
    body() {
      return JSON.stringify(this.entry.request.body, null, 2)
    },
    variables() {
      return JSON.stringify(this.entry.request.variables, null, 2)
    },
  },
  data() {
    return {
      show: false,
      toggled: false,
      resizeObserver: null,
    }
  },
  mounted() {
    this.observeHeight()
  },
  methods: {
    toggle() {
      this.show = !this.show
      this.$emit('toggled', this.show)
    },
    observeHeight() {
      this.resizeObserver = new ResizeObserver(([entry]) => {
        this.show = entry.contentRect.height !== HEIGHT_COLLAPSED
      })
      this.resizeObserver.observe(this.$el)
    },
  },
}
</script>
