<template>
  <div class="lg:h-screen w-screen grid grid-cols-1 lg:grid-cols-2">
    <div class="lg:h-screen flex items-center justify-center py-20 lg:py-3 p-3 bg-white">
      <div class="w-full lg:w-5/12 flex flex-col gap-5">
        <div>
          <nuxt-link to="/">
            <logo-dark />
          </nuxt-link>
        </div>
        <div class="flex flex-col gap-2">
          <div>
            <p class="text-xl font-medium">Satıcı Paneli</p>
          </div>
          <ul
            class="flex items-center h-12 px-1 gap-1 bg-gray-50 rounded-xl relative"
          >
            <li
              @click="$router.push('/login')"
              class="h-10 w-full flex items-center justify-center z-10 cursor-pointer"
            >
              Giriş Yapın
            </li>
            <li
              @click="$router.push('/register')"
              class="h-10 w-full flex items-center justify-center z-10 cursor-pointer"
            >
              Yeni Başvuru
            </li>
            <li
              :class="{
                'left-1': $route.path == '/login',
                'left-[49.1%]': $route.path == '/register',
              }"
              class="absolute w-6/12 h-10 bg-white rounded-lg border border-gray-100 transition-all duration-300"
            ></li>
          </ul>
        </div>
        <Nuxt />
      </div>
    </div>
    <div class="h-screen bg-gray-50">
    </div>
  </div>
</template>

<script>
export default {
  beforeMount() {
    this.$auth.user ? this.$router.push("/") : null;
  },
};
</script>