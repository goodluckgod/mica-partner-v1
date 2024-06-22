<template>
  <div class="container mx-auto p-5 gap-5 flex flex-col">
    <p class="text-xl">Siparişler</p>

    <div class="w-full">
      <div class="grid grid-cols-1 gap-3">
        <table
          v-for="(order, index) in orders"
          :key="index"
          class="border border-gray-100 bg-white !rounded-xl table-auto w-full"
        >
          <tr
            v-for="(item, bindex) in order.items"
            :key="bindex"
            class="hover:bg-gray-50 cursor-pointer transition duration-300"
          >
            <td class="px-3 py-2 flex gap-2">
              <div>
                <div
                  class="h-[105px] min-w-[70px] cursor-pointer max-w-[70px] bg-cover bg-center border border-gray-100 rounded-xl bg-no-repeat"
                  :style="{
                    'background-image':
                      'url(' +
                      $axios.defaults.baseURL +
                      '/images/product/' +
                      item.product.images[0].url +
                      ')',
                  }"
                ></div>
              </div>
              <div class="leading-tight font-light text-gray-500 text-sm">
                <p class="text-black">Ürün Bilgileri</p>
                {{ item.product.title }}
              </div>
            </td>
            <td class="px-4 py-2">
              <div
                class="h-[105px] leading-tight font-light text-gray-500 text-sm flex flex-col gap-2"
              >
                <div>
                  <p class="text-black">Sipariş Numarası</p>
                  {{ order.ordernumber }}
                </div>
                <div v-if="item.status == 'ACCEPTED' && item.shipmentcode">
                  <p class="text-black">Kargo Numarası</p>
                  {{ item.shipmentcode }}
                </div>
              </div>
            </td>
            <td class="px-4 py-2">
              <div
                class="h-[105px] leading-tight font-light text-gray-500 text-sm"
              >
                <p class="text-black">Alıcı</p>
                {{ order.customer.firstname }}
                {{ order.customer.lastname }}
              </div>
            </td>
            <td class="px-4 py-2">
              <div
                class="h-[105px] leading-tight font-light text-gray-500 text-sm"
              >
                <p class="text-black">Miktar</p>
                {{ item.quantity }} Adet
              </div>
            </td>
            <td class="px-4 py-2">
              <div
                class="h-[105px] leading-tight font-light text-gray-500 text-sm"
              >
                <p class="text-black">Birim Fiyat</p>
                <span
                  v-if="
                    item.product.stores.filter(
                      (store) => store.storeid == $auth.user.stores[0].id
                    ).length > 0
                  "
                >
                  {{
                    item.product.stores.filter(
                      (store) => store.storeid == $auth.user.stores[0].id
                    )[0].saleprice
                      ? item.product.stores.filter(
                          (store) => store.storeid == $auth.user.stores[0].id
                        )[0].saleprice
                      : item.product.stores.filter(
                          (store) => store.storeid == $auth.user.stores[0].id
                        )[0].listprice
                  }}
                  TL
                </span>
              </div>
            </td>
            <td class="px-4 py-2 w-2/12">
              <div
                class="h-[105px] leading-tight font-light text-gray-500 text-sm flex flex-col gap-2"
              >
                <p class="text-black">İşlemler</p>
                <div
                  v-if="item.status == 'PENDING'"
                  class="flex flex-col gap-2"
                >
                  <button
                    @click="acceptorder(item)"
                    class="h-9 w-full bg-amber-50 text-amber-500 rounded-xl"
                  >
                    Siparişi Kabul Et
                  </button>
                  <button
                    @click="
                      modal.cancel = !modal.cancel;
                      selected = item;
                    "
                    class="h-9 w-full bg-red-50 text-red-500 rounded-xl"
                  >
                    Siparişi İptal Et
                  </button>
                </div>
                <div v-else-if="item.status == 'CANCELLED'">
                  <p>İptal Edildi</p>
                  <p>İptal Sebebi: {{ item.cancelmessage }}</p>
                </div>
                <div v-else-if="item.status == 'ACCEPTED'">
                  Siparişi Kargoya Veriniz
                </div>
              </div>
            </td>
          </tr>
        </table>
      </div>
    </div>
    <modal-order-cancel
      v-if="modal.cancel"
      :selected="selected"
      @close="
        modal.cancel = false;
        getorders();
      "
    />
  </div>
</template>

<script>
export default {
  data() {
    return {
      orders: [],
      selected: null,
      modal: {
        cancel: false,
      },
    };
  },
  methods: {
    getorders() {
      this.$axios.get("/v1/partner/orders").then((response) => {
        if (response.data.status == "success") {
          this.orders = response.data.data;
        }
      });
    },
    acceptorder(product) {
      this.$axios
        .post("/v1/partner/orders/accept/" + product.id, {
          message: this.reason,
        })
        .then((response) => {
          if (response.data.status == "success") {
            this.getorders();
          } else {
            alert(response.data.message);
          }
        });
    },
  },
  mounted() {
    this.getorders();
  },
};
</script>
