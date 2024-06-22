<template>
  <div class="flex flex-col gap-5">
    <p class="text-md">Ürün Bilgileri</p>
    <ValidationObserver
      tag="form"
      ref="form"
      @submit="complete"
      class="flex flex-col gap-3"
    >
      <ValidationProvider rules="required" v-slot="{ errors }">
        <div class="border border-gray-100 rounded-xl bg-white">
          <input-primary
            :error="errors[0]"
            label="Ürün Adı"
            v-model="combinator.name"
          />
        </div>
      </ValidationProvider>
      <ValidationProvider rules="required" v-slot="{ errors }">
        <input-category
          :error="errors[0]"
          :categories="categories"
          label="Kategori Seçiniz"
          @remove="
            varianter = [];
            slicer = [];
          "
          v-model="combinator.category"
        />
      </ValidationProvider>
      <div class="grid grid-cols-2 gap-3">
        <ValidationProvider rules="required" v-slot="{ errors }">
          <div class="border border-gray-100 rounded-xl bg-white">
            <input-primary
              :error="errors[0]"
              label="Model Kodu"
              v-model="combinator.modelcode"
            />
          </div>
        </ValidationProvider>
        <ValidationProvider rules="required" v-slot="{ errors }">
          <div class="border border-gray-100 rounded-xl bg-white">
            <input-primary
              :error="errors[0]"
              label="Marka"
              v-model="combinator.brand"
            />
          </div>
        </ValidationProvider>
      </div>
      <div v-if="combinator.slicer.length > 0" class="flex flex-col gap-3">
        <div v-for="(slice, index) in combinator.slicer" :key="index">
          <component
            :is="slice.custom ? 'input-custom' : 'input-select'"
            :label="slice.label"
            :values="
              slicer.filter((s) => s.attribute.id == slice.id)[0]
                .attributeValues
            "
            v-model="combinator.slicer[index].data"
            @input="changed()"
          />
        </div>
      </div>
      <div v-if="combinator.varianter.length > 0" class="flex flex-col gap-3">
        <div v-for="(variant, index) in combinator.varianter" :key="index">
          <component
            :is="variant.custom ? 'input-custom' : 'input-select'"
            :label="variant.label"
            :values="
              varianter.filter((v) => v.attribute.id == variant.id)[0]
                .attributeValues
            "
            v-model="combinator.varianter[index].data"
            @input="changed()"
          />
        </div>
      </div>
    </ValidationObserver>
  </div>
</template>

<script>
export default {
  props: ["categories"],
  data() {
    return {
      error: {},
      combinator: {
        name: "",
        category: "",
        modelcode: "",
        brand: "",
        slicer: [],
        varianter: [],
      },
      attributes: [],
      varianter: [],
      slicer: [],
    };
  },
  watch: {
    "combinator.category": function (category, old) {
      this.$axios
        .get(`/v1/categories/${category}/attributes`)
        .then((response) => {
          if (response.data.status == "success") {
            this.combinator.slicer = [];
            this.combinator.varianter = [];

            this.attributes = response.data.category.attributes;
            this.varianter = response.data.category.attributes.filter(
              (attribute) => attribute.varianter
            );
            this.slicer = response.data.category.attributes.filter(
              (attribute) => attribute.slicer
            );

            response.data.category.attributes
              .filter((attribute) => attribute.slicer)
              .map((attribute) => {
                this.combinator.slicer.push({
                  id: attribute.attribute.id,
                  custom: attribute.allowCustom,
                  label: attribute.attribute.name,
                  data: [],
                });
              });

            response.data.category.attributes
              .filter((attribute) => attribute.varianter)
              .map((attribute) => {
                this.combinator.varianter.push({
                  id: attribute.attribute.id,
                  custom: attribute.allowCustom,
                  label: attribute.attribute.name,
                  data: [],
                });
              });
          }
        });
    },
  },
  methods: {
    changed() {
      if (
        this.combinator.slicer.every((slice) => slice.data.length > 0) &&
        this.combinator.varianter.every((variant) => variant.data.length > 0)
      ) {
        this.$emit("completed", {
          varianter: this.combinator.varianter,
          slicer: this.combinator.slicer,
        });
      }
    },
    complete(event) {
      event.preventDefault();
      this.$refs.form.validate().then((success) => {
        if (success) {
        }
      });
    },
  },
};
</script>
