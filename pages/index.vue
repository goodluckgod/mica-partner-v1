<template>
  <div class="container mx-auto p-5">Hoşgeldiniz, {{ $auth.user.firstname }}</div>
</template>

<script>
export default {
  methods: {
    logout() {
      this.$auth.logout();
    },
  },
};
</script>
