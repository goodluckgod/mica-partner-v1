<template>
  <div>
    <div class="container mx-auto grid grid-cols-1 p-5">
      <div
        v-show="step == 'information'"
        class="p-5 bg-white border border-gray-100 flex flex-col gap-5 rounded-xl h-[calc(100vh-220px)] mb-[75px]"
      >
        <div>
          <p class="text-xl">Ürün Bilgileri</p>
        </div>
        <div class="flex flex-col gap-3 h-full">
          <div class="border border-gray-100 rounded-xl">
            <input-primary label="Ürün Adı" v-model="product.name" />
          </div>
          <div>
            <input-category
              label="Ürün Kategorisi"
              v-model="product.category"
            />
          </div>
          <div class="border border-gray-100 rounded-xl">
            <input-primary label="Model Kodu" v-model="product.mainid" />
          </div>
          <div class="border border-gray-100 rounded-xl">
            <input-brand
              label="Marka"
              v-model="product.brand.name"
              @selected="
                (data) => {
                  product.brand = data;
                }
              "
            />
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
                @input="changed"
              />
            </div>
          </div>
          <div
            v-if="combinator.varianter.length > 0"
            class="flex flex-col gap-3"
          >
            <div v-for="(variant, index) in combinator.varianter" :key="index">
              <component
                :is="variant.custom ? 'input-custom' : 'input-select'"
                :label="variant.label"
                :values="
                  varianter.filter((v) => v.attribute.id == variant.id)[0]
                    .attributeValues
                "
                v-model="combinator.varianter[index].data"
                @input="changed"
              />
            </div>
          </div>
          <div class="border border-gray-100 rounded-xl h-full">
            <input-textarea
              label="Ürün Açıklaması"
              v-model="product.description"
            />
          </div>
          <div
            class="fixed bottom-0 left-0 w-full bg-white p-3 z-10 border-t border-gray-100"
          >
            <div
              class="container mx-auto flex items-center justify-between px-5"
            >
              <div></div>
              <div @click="test()">
                <button-primary class="px-5" title="Sonraki Adım" />
              </div>
            </div>
          </div>
        </div>
      </div>
      <div
        v-if="step == 'varianter'"
        class="bg-white p-5 border border-gray-100 rounded-xl min-h-[calc(100vh-220px)] flex flex-col gap-5"
      >
        <div>
          <p class="text-xl">Satış ve Varyant Bilgileri</p>
        </div>
        <product-variants
          :slicer="combinator.slicer"
          :varianter="combinator.varianter"
          :products="products"
          @prev="step = 'information'"
          @next="step = 'attributes'"
          @products="(data) => (products = data)"
        />
      </div>
      <div
        v-if="step == 'attributes'"
        class="bg-white p-5 border border-gray-100 rounded-xl min-h-[calc(100vh-220px)] flex flex-col gap-5"
      >
        <div>
          <p class="text-xl">Ürün Özellikleri</p>
        </div>
        <div>
          <p>Zorunlu Alanlar</p>
        </div>
        <ValidationObserver
          ref="attributesrequired"
          class="grid grid-cols-4 gap-3"
        >
          <div
            class="border border-gray-100 rounded-xl"
            v-for="(attribute, index) in attributes.required"
            :key="index"
          >
            <ValidationProvider
              :rules="{ required: true }"
              v-slot="{ errors }"
              ref="attributeProviders"
            >
              <select-primary
                :label="attribute.attribute.name"
                :error="errors[0]"
                @input="(data) => updateAttribute(attribute.attribute.id, data)"
              >
                <option selected disabled></option>
                <option
                  v-for="(option, bindex) in attribute.attributeValues"
                  :value="option.id"
                  :key="bindex"
                >
                  {{ option.name }}
                </option>
              </select-primary>
            </ValidationProvider>
          </div>
        </ValidationObserver>
        <div>
          <p>Opsiyonel Alanlar</p>
        </div>
        <div class="grid grid-cols-4 gap-3">
          <div
            class="border border-gray-100 rounded-xl"
            v-for="(attribute, index) in attributes.optional"
            :key="index"
            v-show="attribute.attributeValues.length > 0"
          >
            <select-primary
              :label="attribute.attribute.name"
              @input="(data) => updateAttribute(attribute.attribute.id, data)"
            >
              <option
                v-for="(option, bindex) in attribute.attributeValues"
                :value="option.id"
                :key="bindex"
              >
                {{ option.name }}
              </option>
            </select-primary>
          </div>
        </div>
        <div
          class="fixed bottom-0 left-0 w-full bg-white p-3 z-10 border-t border-gray-100"
        >
          <div class="container mx-auto flex items-center justify-between px-5">
            <div></div>
            <div @click="create()">
              <button-primary
                class="px-5"
                title="Ürün Onaya Gönder"
                :loading="loading"
              />
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      step: "information",
      product: {
        name: null,
        category: null,
        mainid: "",
        description: "",
        brand: {
          id: null,
          name: null,
        },
      },
      loading: false,
      combinator: {
        slicer: [],
        varianter: [],
      },
      products: [],
      slicer: [],
      varianter: [],
      attributes: {
        required: [],
        optional: [],
      },
    };
  },
  watch: {
    "product.category": function (category) {
      this.fetchCategoryAttributes(category);
    },
  },
  methods: {
    updateAttribute(attributeid, event) {
      const attributevalueid = parseInt(event);
      this.products.forEach((product) => {
        const existingAttributeIndex = product.attributes.findIndex(
          (attr) => attr.attributeId === attributeid
        );
        if (existingAttributeIndex !== -1) {
          product.attributes[existingAttributeIndex].attributevalueid =
            attributevalueid;
        } else {
          product.attributes.push({
            attributeid: attributeid,
            attributevalueid: attributevalueid,
          });
        }
      });
    },
    fetchCategoryAttributes(category) {
      this.$axios
        .get(`/v1/category/${category}/attributes`)
        .then((response) => {
          if (response.data.status === "success") {
            this.combinator.slicer = [];
            this.combinator.varianter = [];

            const attributes = response.data.category.attributes;
            this.varianter = attributes.filter(
              (attribute) => attribute.varianter
            );
            this.slicer = attributes.filter((attribute) => attribute.slicer);
            this.attributes.required = attributes.filter(
              (attribute) =>
                attribute.required == true &&
                attribute.slicer == false &&
                attribute.varianter == false
            );
            this.attributes.optional = attributes.filter(
              (attribute) =>
                attribute.required == false &&
                attribute.slicer == false &&
                attribute.varianter == false
            );

            attributes
              .filter((attribute) => attribute.slicer)
              .forEach((attribute) => {
                this.combinator.slicer.push({
                  id: attribute.attribute.id,
                  custom: attribute.allowCustom,
                  label: attribute.attribute.name,
                  data: [],
                });
              });

            attributes
              .filter((attribute) => attribute.varianter)
              .forEach((attribute) => {
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
    changed() {
      if (
        this.combinator.slicer.every((slice) => slice.data.length > 0) &&
        this.combinator.varianter.every((variant) => variant.data.length > 0)
      ) {
        // Emit completed event if needed
      }
    },
    generate() {
      const items = [];
      const combinations = [];

      const getCombinations = (arr, prefix = []) => {
        if (arr.length === 0) {
          combinations.push(prefix);
          return;
        }

        const [first, ...rest] = arr;
        first.forEach((item) => {
          getCombinations(rest, [...prefix, item]);
        });
      };

      const combinedData = [
        ...this.combinator.slicer,
        ...this.combinator.varianter,
      ];
      const combinedSlicerVarianterData = combinedData.map((s) =>
        s.data.map((d) => ({
          id: s.id,
          value: d.name || d,
          valueId: d.id || null,
        }))
      );

      getCombinations(combinedSlicerVarianterData);

      combinations.forEach((combination) => {
        const attributes = combination.map((item, idx) => {
          if (item.valueId) {
            return {
              attributeid: combinedData[idx].id,
              attributevalueid: item.valueId,
            };
          } else {
            return {
              attributeid: combinedData[idx].id,
              customattributevalue: item.value,
            };
          }
        });

        items.push({
          id: this.generateRandomId(),
          barcode: null,
          title: this.product.name,
          mainid: this.product.mainid,
          brandid: this.product.brand.id,
          categoryid: this.product.category,
          stockcode: null,
          description: this.product.description,
          currency: "TRY",
          listprice: null,
          saleprice: null,
          vatrate: null,
          images: [],
          attributes: attributes,
        });
      });

      this.products = items;
      this.step = "varianter";
    },
    test() {
      console.log("Test");
      if (this.step === "information") {
        this.generate();
      }
    },
    generateRandomId() {
      return Math.floor(Math.random() * (999999 - 100000 + 1)) + 100000;
    },
    async create() {
      this.loading = true;
      this.$axios
        .post("/v1/products/create", { products: this.products })
        .then((response) => {
          setTimeout(() => {
            this.loading = false;
            if (response.data.status == "success") {
              this.$router.push("/");
            }
          }, 1000);
        });
    },
  },
};
</script>
