<template>
  <ul>
    <li>
      <div class="relative">
        <div
          v-on:mousedown="
            active = !active;
            active ? $emit('menu') : $emit('unmenu');
          "
          v-if="this.$auth.user.stores"
          class="h-10 px-3 flex items-center gap-1.5 font-light text-gray-500 hover:bg-gray-100 cursor-pointer transition-all duration-300 rounded-xl"
        >
          <icon-store class="stroke-gray-500" />
          {{ this.$auth.user.stores[0].name }}
          <icon-dropdown
            :class="{ '-rotate-180': active }"
            class="transition-all duration-300 stroke-gray-500"
          />
        </div>
        <transition name="menu-fade" mode="out-in">
          <div class="absolute top-full right-0 pt-4 z-10" v-if="active">
            <ul
              class="p-2.5 w-[250px] bg-white border border-gray-100 rounded-xl"
            >
              <li
                @click="logout()"
                class="h-10 px-3 font-light text-sm justify-between rounded-lg cursor-pointer transition-all duration-300 flex items-center hover:bg-red-50 hover:text-red-600 stroke-gray-500 hover:stroke-red-600"
              >
                <span>Çıkış Yap</span>
                <icon-logout />
              </li>
            </ul>
          </div>
        </transition>
      </div>
    </li>
  </ul>
</template>

<script>
export default {
  data() {
    return {
      active: false,
    };
  },
  methods: {
    logout() {
      this.$auth.logout().then(() => {
        this.$router.push("/login");
      });
    },
  },
  mounted() {
    this.$auth.fetchUser();
  },
};
</script>
