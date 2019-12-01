<template>
  <div class="container mx-auto">
    <button class="button">
      <IconPreferences class="absolute top-0 right-0 m-4" />
    </button>

    <div class="flex justify-center">
      <Logo class="w-2 text-center" />
    </div>

    <div class="py-8">
      <router-view />
    </div>
  </div>
</template>

<script>
import Logo from './components/Logo.vue';
import IconPreferences from './components/icons/IconPreferences.vue';

export default {
  components: {
    Logo,
    IconPreferences
  }
};
</script>
