<template>
  <div class="relative h-12 border border-gray-100 rounded-xl bg-white">
    <div
      class="flex items-center h-full border-2 rounded-xl border-transparent"
      :class="{ '!border-black': focused }"
    >
      <ul
        class="flex text-xs font-light pt-3.5 pl-2.5 gap-1.5"
        v-if="values.length > 0"
      >
        <li
          class="bg-gray-100 h-[22px] py-0.5 pl-2 rounded-md flex items-center gap-1.5"
          v-for="(value, index) in values"
          :key="index"
        >
          <span>{{ value }}</span>
          <span
            @click="remove(index)"
            class="h-6 w-6 bg-primary-500 text-black rounded-md flex items-center justify-center cursor-pointer"
            >x</span
          >
        </li>
      </ul>
      <input
        :id="random"
        ref="input"
        @focus="focused = true"
        @blur="focused = false"
        autocomplete="off"
        class="block h-12 px-2.5 pb-2.5 pt-6 w-full text-sm text-gray-900 bg-transparent rounded-lg border-1 border-gray-300 appearance-none focus:ring-0 peer outline-none"
        placeholder=" "
        :type="type ? type : 'text'"
        @keyup="keyup"
        :autofocus="autofocus ? autofocus : false"
        :disabled="disabled"
      />
    </div>
    <label
      :for="random"
      class="absolute text-sm font-light peer-focus:text-primary-600 text-gray-500 duration-300 transform -translate-y-1 scale-75 top-1 z-10 origin-[0] px-2 peer-focus:px-2 peer-placeholder-shown:scale-100 peer-placeholder-shown:-translate-y-1/2 peer-placeholder-shown:top-1/2 peer-focus:top-2 peer-focus:scale-75 peer-focus:-translate-y-0.5 rtl:peer-focus:translate-x-1/4 rtl:peer-focus:left-auto start-1"
    >
      <span class="text-red-600" v-if="error">{{ error }}</span>
      <span v-else>{{ label }}</span>
    </label>
  </div>
</template>

<script>
export default {
  props: ["label", "type", "autofocus", "mask", "error", "disabled"],
  data() {
    return {
      random: "",
      values: [],
      focused: false,
    };
  },
  methods: {
    keyup(key) {
      if (key.code == "Backslash" || key.code == "Enter") {
        let tests = this.values;
        if (this.$refs.input.value.split(",")[0] != "")
          tests.push(this.$refs.input.value.split(",")[0]);
        this.values = tests;
        this.$refs.input.value = "";
      }
    },
    remove(index) {
      this.values.splice(index, 1);
    },
  },
  watch: {
    values: function (newdata, olddata) {
      this.$emit("input", newdata);
    },
  },
  mounted() {
    if (this.autofocus) {
      this.$refs.input.focus();
    }
    this.random = Math.floor(Math.random() * 100) + 1;
  },
};
</script>
