<template>
  <div class="relative" @mouseover="active = true" @mouseleave="active = false">
    <div
      class="flex h-12 items-center bg-white border border-gray-100 rounded-xl"
      :class="{ 'rounded-b-none': active }"
    >
      <label
        :for="random"
        class="absolute text-sm font-light peer-focus:text-primary-600 text-gray-500 duration-300 transform -translate-y-1 scale-75 top-1.5 z-10 origin-[0] px-2 peer-focus:px-2 peer-placeholder-shown:scale-100 peer-placeholder-shown:-translate-y-1/2 peer-placeholder-shown:top-1/2 peer-focus:top-1.5 peer-focus:scale-75 peer-focus:-translate-y-0.5 rtl:peer-focus:translate-x-1/4 rtl:peer-focus:left-auto start-1"
      >
        <span class="text-red-600" v-if="error">{{ error }}</span>
        <span v-else>{{ label }}</span>
      </label>
      <ul
        class="w-full flex text-xs font-light pt-3.5 pl-2.5 gap-1.5"
        v-if="selected.length > 0"
      >
        <li
          class="bg-gray-100 h-[22px] py-0.5 pl-2 rounded-md flex items-center gap-1.5"
          v-for="(value, index) in selected"
          :key="index"
        >
          <span>{{ value.name }}</span>
          <span
            @click="select(value)"
            class="h-6 w-6 bg-primary-500 text-black rounded-md flex items-center justify-center cursor-pointer"
            >x</span
          >
        </li>
      </ul>
      <!-- <input
        ref="input"
        autocomplete="off"
        class="block h-12 px-2.5 pb-2.5 pt-6 w-full text-sm text-gray-900 bg-transparent rounded-lg border-1 border-gray-300 appearance-none focus:ring-0 peer outline-none"
        placeholder=" "
      /> -->
    </div>
    <transition name="menu-fade" mode="out-in">
      <div class="absolute top-full w-full left-0 z-20" v-if="active">
        <div
          class="w-full bg-white rounded-b-xl border-t-0 border border-gray-100"
        >
          <ul
            class="w-full h-[250px] grid grid-cols-2 overflow-auto scrollbar text-sm"
          >
            <li
              v-for="(item, index) in values"
              :key="index"
              class="h-10 px-2.5 flex items-center"
              @click="select(item)"
            >
              <span :class="{ 'text-primary-600': isselected(item) }">{{
                item.name
              }}</span>
            </li>
          </ul>
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
export default {
  props: ["label", "values", "error"],
  data() {
    return {
      active: false,
      selected: [],
      random: "",
    };
  },
  watch: {
    selected(newdata, olddata) {
      this.$emit("input", newdata);
    },
  },
  methods: {
    select(item) {
      const index = this.selected.findIndex((value) => value.id === item.id);
      if (index > -1) {
        this.selected.splice(index, 1);
      } else {
        this.selected.push(item);
      }
    },
    isselected(item) {
      return this.selected.some((value) => value.id === item.id);
    },
  },
  mounted() {
    this.random = Math.floor(Math.random() * 100) + 1;
  },
};
</script>
