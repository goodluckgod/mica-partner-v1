<template>
  <div class="flex flex-col gap-3">
    <table class="table-auto w-full">
      <thead>
        <tr>
          <th
            class="p-2 font-light text-gray-500 border-[0.5px] border-gray-100 text-sm !rounded-tl-xl"
          >
            Ürün Fotoğrafı
          </th>
          <th
            class="p-2 font-light text-gray-500 border-[0.5px] border-gray-100 text-sm"
            v-for="(slice, index) in slicer"
            :key="`slice-${index}`"
          >
            {{ slice.label }}
          </th>
          <th
            class="p-2 font-light text-gray-500 border-[0.5px] border-gray-100 text-sm"
            v-for="(variant, index) in varianter"
            :key="`variant-${index}`"
          >
            {{ variant.label }}
          </th>
          <th
            class="p-2 font-light text-gray-500 border-[0.5px] border-gray-100 text-sm"
          >
            Barkod
          </th>
          <th
            class="p-2 font-light text-gray-500 border-[0.5px] border-gray-100 text-sm"
          >
            Piyasa Satış Fiyatı
          </th>
          <th
            class="p-2 font-light text-gray-500 border-[0.5px] border-gray-100 text-sm"
          >
            Dinazors Satış Fiyatı
          </th>
          <th
            class="p-2 font-light text-gray-500 border-[0.5px] border-gray-100 text-sm"
          >
            Stok
          </th>
          <th
            class="p-2 font-light text-gray-500 border-[0.5px] border-gray-100 text-sm"
          >
            KDV
          </th>
          <th
            class="p-2 font-light text-gray-500 border-[0.5px] border-gray-100 text-sm !rounded-tr-xl"
          >
            Stok Kodu
          </th>
        </tr>
      </thead>
      <tbody>
        <template v-for="(products, color) in groupedProducts">
          <tr :key="`color-${color}`">
            <td
              :rowspan="products.length + 1"
              class="p-2 border-[0.5px] border-gray-100"
            >
              <div
                @click="dropzone = color"
                :style="{ height: 58 * products.length + 'px' }"
                class="border-2 border-dotted border-primary-500 rounded-xl p-1 flex items-center justify-center relative cursor-pointer min-w-[180px]"
              >
                <div
                  v-if="products[0].images.length > 0"
                  class="h-full w-full bg-opacity-50 bg-cover bg-center rounded-xl relative max-h-[200px]"
                  :style="{
                    'background-image':
                      'url(' +
                      $axios.defaults.baseURL +
                      '/images/product/' +
                      products[0].images[0].url +
                      ')',
                  }"
                >
                  <div
                    v-if="products[0].images.length - 1 > 0"
                    class="bg-gray-50 text-sm px-3 py-1 rounded-xl absolute top-3 right-3"
                  >
                    +{{ products[0].images.length - 1 }} Fotoğraf
                  </div>
                  <div>
                    <icon-image-add class="fill-primary-600" />
                  </div>
                </div>
                <icon-image-add v-else class="fill-primary-600" />
              </div>
              <dropzone-product-image-variant
                v-if="dropzone == color"
                @images="(data) => images(data, products)"
                @close="dropzone = null"
                :dropzoner="dropzone"
              />
            </td>
          </tr>
          <ValidationObserver
            v-for="(product, index) in products"
            :ref="setProductRef(product.id)"
            tag="tr"
            :key="`product-${product.id}-${index}`"
          >
            <td
              v-for="(slice, bindex) in slicer"
              :key="`product-${product.id}-slice-${bindex}`"
              class="p-1 border-[0.5px] border-gray-100 text-sm font-light text-gray-500"
            >
              {{ getAttributeValue(product, slice) }}
            </td>
            <td
              v-for="(variant, vindex) in varianter"
              :key="`product-${product.id}-variant-${vindex}`"
              class="p-1 border-[0.5px] border-gray-100 text-sm font-light text-gray-500"
            >
              {{ getAttributeValue(product, variant) }}
            </td>
            <td class="p-2 border-[0.5px] border-gray-100">
              <ValidationProvider
                name="Barkod"
                rules="required"
                v-slot="{ errors }"
              >
                <div class="border border-gray-100 rounded-xl">
                  <input-primary
                    :error="errors[0]"
                    label="Barkod"
                    v-model="product.barcode"
                  />
                </div>
              </ValidationProvider>
            </td>
            <td class="p-2 border-[0.5px] border-gray-100">
              <ValidationProvider
                name="Piyasa Satış Fiyatı"
                rules="required"
                v-slot="{ errors }"
              >
                <div class="border border-gray-100 rounded-xl">
                  <input-primary
                    :error="errors[0]"
                    label="Piyasa Satış Fiyatı"
                    v-model="product.listprice"
                  />
                </div>
              </ValidationProvider>
            </td>
            <td class="p-2 border-[0.5px] border-gray-100">
              <ValidationProvider
                name="Dinazors Satış Fiyatı"
                rules="required"
                v-slot="{ errors }"
              >
                <div class="border border-gray-100 rounded-xl">
                  <input-primary
                    :error="errors[0]"
                    label="Dinazors Satış Fiyatı"
                    v-model="product.saleprice"
                  />
                </div>
              </ValidationProvider>
            </td>
            <td class="p-2 border-[0.5px] border-gray-100">
              <ValidationProvider
                name="Stok"
                rules="required"
                v-slot="{ errors }"
              >
                <div class="border border-gray-100 rounded-xl">
                  <input-primary
                    :error="errors[0]"
                    label="Stok"
                    v-model="product.stock"
                    type="number"
                  />
                </div>
              </ValidationProvider>
            </td>
            <td class="p-2 border-[0.5px] border-gray-100">
              <ValidationProvider
                name="KDV"
                rules="required"
                v-slot="{ errors }"
              >
                <div class="border border-gray-100 rounded-xl">
                  <input-primary
                    :error="errors[0]"
                    label="KDV"
                    v-model="product.vatrate"
                    type="number"
                  />
                </div>
              </ValidationProvider>
            </td>
            <td class="p-2 border-[0.5px] border-gray-100">
              <ValidationProvider
                name="Stok Kodu"
                rules="required"
                v-slot="{ errors }"
              >
                <div class="border border-gray-100 rounded-xl">
                  <input-primary
                    :error="errors[0]"
                    label="Stok Kodu"
                    v-model="product.stockcode"
                  />
                </div>
              </ValidationProvider>
            </td>
          </ValidationObserver>
        </template>
      </tbody>
    </table>
    <div
      class="fixed bottom-0 left-0 w-full bg-white p-3 z-10 border-t border-gray-100"
    >
      <div class="container mx-auto flex items-center justify-between px-5">
        <div @click="$emit('prev')">
          <button-primary class="px-5" title="Önceki Adım" />
        </div>
        <div @click="send()">
          <button-primary class="px-5" title="Sonraki Adım" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ValidationObserver, ValidationProvider } from "vee-validate";

export default {
  props: ["slicer", "varianter", "products"],
  data() {
    return {
      dropzone: null,
      groupedProducts: {},
      productRefs: {},
    };
  },
  watch: {
    products: {
      handler: "updateGroupedProducts",
      deep: true,
      immediate: true,
    },
  },
  methods: {
    setProductRef(productId) {
      const uniqueRef = `product-${productId}-${Math.random()
        .toString(36)
        .substr(2, 9)}`;
      this.$set(this.productRefs, productId, uniqueRef);
      return uniqueRef;
    },
    images(data, products) {
      products.forEach((product) => {
        if (!product.images) {
          product.images = [];
        }

        data.forEach((newImage) => {
          if (!product.images.some((image) => image.url === newImage.url)) {
            product.images.push(newImage);
          }
        });
      });

      Object.keys(this.groupedProducts).forEach((color) => {
        this.groupedProducts[color].forEach((groupedProduct) => {
          const matchedProduct = products.find(
            (product) => product.id === groupedProduct.id
          );
          if (matchedProduct) {
            groupedProduct.images = matchedProduct.images;
          }
        });
      });
    },
    async send() {
      const refs = this.$refs;
      const keys = Object.keys(this.productRefs);
      let isValid = true;

      for (const key of keys) {
        const refKey = this.productRefs[key];
        const observer = refs[refKey][0]; // Access the first element in the array of refs
        if (observer && typeof observer.validate === "function") {
          const valid = await observer.validate();
          if (!valid) {
            isValid = false;
          }
        } else {
          console.error(`ValidationObserver not found for key: ${refKey}`);
          isValid = false;
        }
      }

      if (isValid) {
        let productList = [];
        for (const color in this.groupedProducts) {
          if (this.groupedProducts.hasOwnProperty(color)) {
            this.groupedProducts[color].forEach((product) => {
              productList.push(product);
            });
          }
        }
        this.$emit("products", productList);
        this.$emit("next");
      }
    },
    updateGroupedProducts() {
      let groupedProducts = {};

      this.slicer.forEach((slice) => {
        if (slice.id === 47) {
          slice.data.forEach((color) => {
            this.products.forEach((product) => {
              const hasColor = product.attributes.some((attribute) => {
                return (
                  attribute.attributeid === 47 &&
                  attribute.customattributevalue === color
                );
              });
              if (hasColor) {
                if (!groupedProducts[color]) {
                  groupedProducts[color] = [];
                }
                groupedProducts[color].push(product);
              }
            });
          });
        }
      });

      let noColorProducts = this.products.filter(
        (product) =>
          !product.attributes.some((attribute) => attribute.attributeid === 47)
      );

      if (noColorProducts.length > 0) {
        groupedProducts["RenkYok"] = noColorProducts;
      }

      this.groupedProducts = groupedProducts;
    },
    getAttributeValue(product, slice) {
      const attribute = product.attributes.find(
        (attr) => attr.attributeid == slice.id
      );
      if (attribute) {
        return (
          attribute.customattributevalue ||
          slice.data.find((s) => s.id == attribute.attributevalueid)?.name ||
          ""
        );
      }
      return "";
    },
  },
  mounted() {
    this.updateGroupedProducts();
  },
};
</script>

<style scoped>
/* custom styles */
</style>
