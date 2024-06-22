<template>
  <div class="relative">
    <div class="relative h-12">
      <input
        :id="random"
        ref="input"
        autocomplete="off"
        class="block h-12 px-2.5 pb-2.5 pt-6 w-full focus:outline-2 outline-black text-sm text-gray-900 bg-transparent rounded-lg border-1 border-gray-300 appearance-none focus:ring-0 peer"
        placeholder=" "
        :type="type ? type : 'text'"
        :autofocus="autofocus ? autofocus : false"
        :disabled="disabled"
        v-model="value"
        @focus="focus = true"
      />
      <label
        :for="random"
        class="absolute text-sm font-light peer-focus:text-primary-600 text-gray-500 duration-300 transform -translate-y-0.5 scale-75 top-1.5 z-10 origin-[0] px-2 peer-focus:px-2 peer-placeholder-shown:scale-100 peer-placeholder-shown:-translate-y-1/2 peer-placeholder-shown:top-1/2 peer-focus:top-1.5 peer-focus:scale-75 peer-focus:-translate-y-0.5 rtl:peer-focus:translate-x-1/4 rtl:peer-focus:left-auto start-1"
      >
        <span class="text-red-600" v-if="error">{{ error }}</span>
        <span v-else>{{ label }}</span>
      </label>
    </div>
    <div
      v-if="focus"
      @mouseleave="
        focus = false;
        $refs.input.blur();
      "
      class="absolute top-full left-0 w-full pt-2"
    >
      <div class="border-2 border-black rounded-xl bg-white z-30 relative">
        <ul class="max-h-[300px] overflow-auto scrollbar">
          <li
            v-for="(brand, index) in brands"
            :key="index"
            @click="
              value = brand.name;
              $refs.input.blur();
              focus = false;
              $emit('selected', { id: brand.id, name: brand.name });
            "
            class="h-9 w-full flex items-center px-3 hover:bg-gray-50 transition duration-300 text-sm font-light text-gray-500 cursor-pointer"
          >
            {{ brand.name }}
          </li>
        </ul>
        <div v-if="brands.length == 0" class="p-3 text-xs font-light text-center text-gray-500">
          {{ value }} araması için bir sonuç bulunamadı
        </div>
        <div
          class="p-3 bg-primary-50/20 text-xs font-light flex items-center justify-between border-t-2 border-black"
        >
          <p>Markanızı bulamıyor musunuz?</p>
          <button
            @click="modal = true"
            class="bg-primary-50/50 text-primary px-3 h-9 rounded-xl hover:bg-black hover:text-white transition duration-300"
          >
            Markanızı Oluşturun
          </button>
        </div>
      </div>
    </div>
    <transition name="fade" mode="out-in">
      <modal-brand-create
        v-if="modal"
        @close="
          modal = false;
          getbrands();
        "
      />
    </transition>
  </div>
</template>

<script>
export default {
  props: ["label", "type", "autofocus", "mask", "error", "disabled"],
  data() {
    return {
      random: "",
      value: "",
      focus: false,
      modal: false,
      brands: [],
    };
  },
  methods: {
    getbrands() {
      this.$axios
        .get("/v1/brand/search", { params: { name: this.value } })
        .then((response) => {
          if (response.data.status == "success") {
            this.brands = response.data.brands;
          }
        });
    },
  },
  mounted() {
    this.autofocus ? this.$refs.input.focus() : null;
    this.random = Math.floor(Math.random() * 100) + 1;
    this.getbrands();
  },
  watch: {
    value: function () {
      this.getbrands();
    },
  },
};
</script>
