<template>
  <div class="container mx-auto flex flex-col gap-10 p-5">
    <product-information :categories="categories" @completed="completed" />
    <product-variants
      v-if="step > 0"
      :products="products"
      :slicer="slicer"
      :varianter="varianter"
      @remove-product="removeproduct"
      @completed="updateproducts"
    />
  </div>
</template>

<script>
import categories from "@/assets/json/all-categories.json";

export default {
  data() {
    return {
      step: 0,
      categories: categories,
      products: [],
      slicer: [],
      varianter: [],
    };
  },
  watch: {
    products(newdata) {
      console.log(newdata);
    },
  },
  methods: {
    completed(data) {
      this.generateproducts(data.slicer, data.varianter);
      this.slicer = data.slicer;
      this.varianter = data.varianter;
      this.step++;
    },
    generateproducts(slicer, varianter) {
      const items = [];
      const combinations = [];

      const getcombinations = (arr, prefix = []) => {
        if (arr.length === 0) {
          combinations.push(prefix);
          return;
        }

        const [first, ...rest] = arr;
        for (const item of first) {
          getcombinations(rest, [...prefix, item]);
        }
      };

      const combineddata = [...slicer, ...varianter];
      const combinedslicervarianterdata = combineddata.map((s) =>
        s.data.map((d) => ({
          id: s.id,
          value: d.name || d,
          valueId: d.id || null,
        }))
      );

      getcombinations(combinedslicervarianterdata);

      combinations.forEach((combination) => {
        const attributes = combination.map((item, idx) => {
          if (item.valueId) {
            return {
              attributeId: combineddata[idx].id,
              attributeValueId: item.valueId,
            };
          } else {
            return {
              attributeId: combineddata[idx].id,
              customAttributeValue: item.value,
            };
          }
        });

        items.push({
          id: Math.floor(Math.random() * (999999 - 100000 + 1)) + 100000,
          barcode: null,
          title: "",
          productMainId: "",
          brandId: null,
          categoryId: null,
          quantity: null,
          stockCode: null,
          dimensionalWeight: 2,
          description: null,
          currencyType: "TRY",
          listPrice: null,
          salePrice: null,
          vatRate: null,
          cargoCompanyId: 10,
          shipmentAddressId: 0,
          returningAddressId: 0,
          deliveryOption: {
            deliveryDuration: 1,
            fastDeliveryType: "SAME_DAY_SHIPPING|FAST_DELIVERY",
          },
          images: [],
          attributes: attributes,
        });
      });

      this.products = items;
    },
    removeproduct(index) {
      this.products.splice(index, 1);
    },
    updateproducts(data) {
      const productIndex = this.products.findIndex(
        (product) => product.id === data.id
      );
      if (productIndex !== -1) {
        const updatedProducts = [...this.products];
        updatedProducts[productIndex] = {
          ...this.products[productIndex],
          ...data,
        };
        this.products = updatedProducts;
      }
    },
  },
};
</script>
