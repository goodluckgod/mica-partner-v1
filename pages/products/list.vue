<template>
  <div class="container mx-auto py-5">
    <div class="p-3 bg-white border border-gray-100 rounded-xl">
      <table class="table-auto w-full">
        <thead>
          <td class="p-2">Ürün Bilgisi</td>
          <td class="p-2">Varyant</td>
          <td class="p-2">Durum</td>
          <td class="p-2">Kategori</td>
          <td class="p-2">Marka</td>
          <td class="p-2">Komisyon</td>
          <td class="p-2">Piyasa Satış Fiyatı</td>
          <td class="p-2">Dinazors Satış Fiyatı</td>
          <td class="p-2">Stok</td>
        </thead>
        <tbody>
          <tr v-for="(product, index) in products" :key="index" class="text-sm">
            <td class="p-2">
              <div class="flex gap-2">
                <div
                  class="w-[50px] h-[75px] bg-cover bg-center border border-gray-100 rounded-lg"
                  :style="{
                    'background-image':
                      'url(' +
                      $axios.defaults.baseURL +
                      '/images/product/' +
                      product.images[0].url +
                      ')',
                  }"
                ></div>
                <div class="font-light text-sm">
                  <p class="font-normal">{{ product.title }}</p>
                  <p>
                    <span class="text-gray-500">Model Kodu:</span>
                    <span>
                      {{ product.mainid }}
                    </span>
                  </p>
                  <p
                    v-for="(attribute, index) in product.attributes.filter(
                      (attribute) => attribute.slicer == true
                    )"
                    :key="index"
                  >
                    <span class="text-gray-500"
                      >{{ attribute.attributename }}:</span
                    >
                    <span v-if="attribute.customattributevalue">{{
                      attribute.customattributevalue
                    }}</span>
                  </p>
                </div>
              </div>
            </td>
            <td class="p-2">{{ product.productcount }} Varyant</td>
            <td class="p-2">
              <span v-if="product.status == 'UNDER_REVIEW'">Onay Bekliyor</span>
              <span v-if="product.status == 'ACTIVE'">Satışta</span>
              <span v-if="product.status == 'DENIED'">Pasif</span>
            </td>
            <td class="p-2">
              {{ product.categoryname }}
            </td>
            <td class="p-2">
              {{ product.brand.name }}
            </td>
            <td class="p-2">Komisyon</td>
            <td class="p-2">{{ product.listprice }}</td>
            <td class="p-2">{{ product.saleprice }}</td>
            <td class="p-2">{{ product.stock }}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      products: [],
    };
  },
  mounted() {
    this.$axios.get("/v1/products/partner").then((response) => {
      if (response.data.status == "success") {
        this.products = response.data.products;
      }
    });
  },
};
</script>

<style scoped>
.table-auto td,
th {
  border: 1px solid #f3f4f6;
}
</style>
