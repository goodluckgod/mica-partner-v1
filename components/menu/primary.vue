<template>
  <ul class="flex items-center">
    <li
      v-for="(item, index) in items"
      :key="index"
      @click="!item.sub ? go(item.path) : null"
      class="relative cursor-pointer"
      v-on:mouseover="
        !item.sub ? (active = index) : (active = index);
        $emit('menu');
      "
      v-on:mouseleave="
        active = null;
        $emit('unmenu');
      "
    >
      <div>
        <div
          :class="{
            '!bg-primary-50/30 !text-primary-600 stroke-primary-600':
              $route.path == item.path ||
              '/' + $route.path.split('/')[1] == item.path,
            'bg-primary-50/30 text-primary-600 stroke-primary-600':
              active == index,
          }"
          class="h-10 gap-2.5 rounded-xl px-5 flex items-center text-sm stroke-gray-500 text-gray-500 cursor-pointer hover:bg-primary-50/30 hover:text-primary-600 transition-all duration-300"
        >
          <span>{{ item.title }}</span>
          <icon-dropdown v-if="item.sub" />
        </div>
        <transition name="menu-fade" mode="out-in">
          <div
            v-if="active == index && item.sub"
            class="absolute top-full left-0 pt-4 z-30"
          >
            <ul
              class="bg-white p-2.5 border border-gray-100 min-w-[250px] rounded-xl"
            >
              <li
                v-for="(sub, bindex) in item.sub"
                :key="bindex"
                @click="go(sub.path)"
                class="h-10 flex items-center hover:bg-primary-50/30 text-gray-500 hover:text-primary-600 px-3 text-sm cursor-pointer"
              >
                <div>
                  <span>{{ sub.title }}</span>
                </div>
              </li>
            </ul>
          </div>
        </transition>
      </div>
    </li>
  </ul>
</template>

<script>
import items from "@/assets/json/menu.json";

export default {
  data() {
    return {
      items: items,
      active: null,
    };
  },
  methods: {
    go(path) {
      this.$router.push(path);
    },
  },
};
</script>