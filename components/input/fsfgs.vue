<template>
  <div
    class="relative w-full"
    @mouseover="active = true"
    @mouseleave="active = false"
  >
    <div
      :class="[
        'relative flex items-center h-12 bg-white border border-gray-100 rounded-xl',
        { 'rounded-b-none': active },
      ]"
    >
      <div
        class="h-12 w-full bg-transparent outline-2 rounded-xl cursor-pointer px-3 text-sm pt-5"
      >
        <ul v-if="completed" class="flex items-center gap-3">
          <li
            v-for="(level, index) in selected"
            :key="index"
            class="flex items-center gap-1.5"
          >
            <icon-dropdown
              v-if="index > 0"
              class="-rotate-90 h-3 w-3 stroke-gray-500"
            />
            {{ getcategoryname(index) }}
          </li>
        </ul>
      </div>
      <label
        :class="{ 'scale-75 !left-0 mb-3.5 top-1 ': completed }"
        class="text-sm font-light text-gray-500 absolute left-3 cursor-pointer transition-all duration-300"
      >
        <span class="text-red-600" v-if="error">{{ error }}</span>
        <span v-else>{{ label }}</span>
      </label>
    </div>
    <transition name="menu-fade" mode="out-in">
      <div v-if="active" class="absolute top-full left-0 w-full z-20">
        <div
          class="bg-white rounded-b-xl w-full grid grid-cols-6 border border-t-0 border-gray-100"
        >
          <div v-for="(level, index) in levels" :key="index">
            <ul
              v-if="islevelactive(index)"
              class="h-[250px] overflow-auto p-3 text-gray-500 flex flex-col gap-2 scrollbar"
            >
              <li
                v-for="(category, catIndex) in getsubcategories(index)"
                :key="catIndex"
                @click="selectcategory(index, catIndex)"
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
export default {
  props: ["label", "categories", "error"],
  data() {
    return {
      active: false,
      completed: false,
      selected: [],
    };
  },
  computed: {
    levels() {
      return Array.from({ length: this.selected.length + 1 }, (_, i) => i);
    },
  },
  methods: {
    getcategoryname(level) {
      let category = this.categories.categories;
      for (let i = 0; i <= level; i++) {
        category = category[this.selected[i]];
        if (i === level) return category.name;
        category = category.subCategories;
      }
    },
    getcategoryid(level) {
      let category = this.categories.categories;
      for (let i = 0; i <= level; i++) {
        category = category[this.selected[i]];
        if (i === level) return category.id;
        category = category.subCategories;
      }
    },
    islevelactive(level) {
      return level === 0 || this.selected[level - 1] !== undefined;
    },
    getsubcategories(level) {
      return this.selected
        .slice(0, level)
        .reduce(
          (cat, idx) => cat[idx]?.subCategories || [],
          this.categories.categories
        );
    },
    selectcategory(level, index) {
      this.selected = [...this.selected.slice(0, level), index];
      this.completed = !this.getsubcategories(level + 1).length;
      if (this.completed) this.active = false;
      if (!this.completed) this.$emit("remove");
      if (this.completed) this.$emit("input", this.getcategoryid(level));
    },
  },
};
</script>
