<template>
  <div>
    <Header @menu="menu = true" @unmenu="menu = false" />
    <Nuxt
      :class="{ 'opacity-10  blur-sm': menu }"
      class="transition-all duration-300"
    />
  </div>
</template>

<script>
export default {
  middleware: "auth",
  data() {
    return {
      menu: false,
    };
  },
};
</script>