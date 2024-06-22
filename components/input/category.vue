<template>
  <div class="relative">
    <div
      class="relative border-2 border-transparent rounded-xl"
      :class="{ '!border-black': focus }"
    >
      <input
        :id="random"
        ref="input"
        v-model="value"
        autocomplete="off"
        :class="{'!border-transparent': focus}"
        class="block h-12 text-transparent border border-gray-100 px-2.5 pb-2.5 pt-6 w-full outline-none text-sm text-gray-900 bg-transparent focus:rounded-b-none rounded-lg appearance-none focus:ring-0 peer"
        placeholder=" "
        @focus="
          focus = true;
          clearSelection();
        "
      />
      <div
        @click="$refs.input.focus()"
        class="h-12 top-0 w-full bg-transparent outline-2 rounded-xl cursor-pointer px-3 text-xs pt-6 absolute"
      >
        <ul v-if="completed" class="flex items-center gap-1">
          <li
            v-for="(level, index) in selected"
            :key="index"
            class="flex items-center gap-1.5"
          >
            <icon-dropdown
              v-if="index > 0"
              class="-rotate-90 h-3 w-3 stroke-gray-500"
            />
            {{ getCategoryName(index) }}
          </li>
        </ul>
      </div>
      <label
        :for="random"
        class="absolute text-sm font-light peer-focus:text-primary-600 text-gray-500 duration-300 transform -translate-y-0.5 scale-75 top-1.5 z-10 origin-[0] px-2 peer-focus:px-2 peer-placeholder-shown:scale-100 peer-placeholder-shown:-translate-y-1/2 peer-placeholder-shown:top-1/2 peer-focus:top-1.5 peer-focus:scale-75 peer-focus:-translate-y-0.5 rtl:peer-focus:translate-x-1/4 rtl:peer-focus:left-auto start-1"
      >
        <span>{{ label }}</span>
      </label>
    </div>
    <transition name="menu-fade" mode="out-in">
      <div
        v-if="focus"
        @mouseleave="
          focus = false;
          $refs.input.blur();
        "
        class="absolute top-full left-0 w-full rounded-xl pt-2 z-30"
      >
        <div class="bg-white rounded-xl w-full border-2 border-black">
          <div v-for="(level, index) in levels" :key="index">
            <ul
              v-if="isLevelActive(index) && activeLevel === index"
              class="max-h-[250px] overflow-auto p-3 text-gray-500 flex flex-col gap-2 scrollbar"
            >
              <li
                v-for="(category, catIndex) in getSubcategories(index)"
                :key="catIndex"
                @click="selectCategory(index, catIndex)"
                class="hover:text-black transition duration-300 cursor-pointer leading-none flex items-center justify-between"
              >
                <span
                  :class="{ 'text-primary-600': selected[index] === catIndex }"
                  class="text-xs font-light"
                  >{{ category.name }}</span
                >
                <icon-dropdown
                  v-if="category.subCategories?.length"
                  :class="{
                    'stroke-primary-600': selected[index] === catIndex,
                  }"
                  class="-rotate-90 h-3 w-3 stroke-gray-500"
                />
              </li>
            </ul>
          </div>
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
import categories from "@/assets/json/all-categories.json";

export default {
  props: ["label"],
  data() {
    return {
      completed: false,
      random: Math.random().toString(36).substring(2, 15),
      focus: false,
      categories: categories.categories,
      selected: [],
      value: "",
    };
  },
  computed: {
    levels() {
      return Array.from({ length: this.selected.length + 1 }, (_, i) => i);
    },
    activeLevel() {
      return this.selected.length;
    },
  },
  methods: {
    clearSelection() {
      this.selected = [];
      this.completed = false;
      this.value = "";
      this.$emit("input", null);
    },
    getSubcategories(level) {
      return this.selected
        .slice(0, level)
        .reduce((cat, idx) => cat[idx]?.subCategories || [], this.categories);
    },
    selectCategory(level, index) {
      this.selected = [...this.selected.slice(0, level), index];
      this.completed = !this.getSubcategories(level + 1).length;
      this.$emit("input", this.getCategoryId(level));
      if (this.completed) {
        this.value = this.getCategoryName(level);
        this.focus = false;
      }
    },
    getCategoryId(level) {
      let category = this.categories;
      for (let i = 0; i <= level; i++) {
        category = category[this.selected[i]];
        if (i === level) return category.id;
        category = category.subCategories;
      }
    },
    isLevelActive(level) {
      return level === 0 || this.selected[level - 1] !== undefined;
    },
    getCategoryName(level) {
      let category = this.categories;
      for (let i = 0; i <= level; i++) {
        category = category[this.selected[i]];
        if (i === level) return category.name;
        category = category.subCategories;
      }
    },
  },
};
</script>
