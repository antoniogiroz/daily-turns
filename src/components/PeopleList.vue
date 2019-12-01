<template>
  <div class="list mt-4">
    <div
      class="list__item py-3 px-3 flex items-center"
      :class="{
        'line-through text-gray-700': !!person.totalTime,
        'bg-gray-900 text-gray-100': index === selected
      }"
      v-for="(person, index) in items"
      :key="person.name"
    >
      <img
        class="w-12 h-12 rounded-full"
        :src="person.avatar"
        :alt="person.name"
      />
      <div class="ml-4 text-xl flex-grow">
        {{ person.name }}
      </div>
      <div class="ml-4 text-lg">{{ person.totalTime | time }}</div>
      <button
        v-if="!person.totalTime && actionEnabled"
        class="list__action ml-4"
        @click="person.available = !person.available"
      >
        <IconViewShow
          v-if="person.available"
          class="w-6 h-6 fill-current text-gray-600 hover:text-gray-400"
        />
        <IconViewHide
          v-else
          class="w-6 h-6 fill-current text-gray-600 hover:text-gray-400"
        />
      </button>
    </div>
  </div>
</template>

<script>
import IconViewHide from '@/components/icons/IconViewHide.vue';
import IconViewShow from '@/components/icons/IconViewShow.vue';

export default {
  components: {
    IconViewHide,
    IconViewShow
  },

  props: {
    items: {
      type: Array,
      required: true
    },
    selected: {
      type: Number,
      required: false
    },
    actionEnabled: {
      type: Boolean,
      default: true
    }
  }
};
</script>
