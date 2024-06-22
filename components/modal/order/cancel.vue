<template>
  <div
    @click="$emit('close')"
    class="fixed top-0 left-0 z-30 bg-black/5 w-screen h-screen backdrop-blur-md flex items-center justify-center"
  >
    <div class="container mx-auto flex items-center justify-center">
      <div
        @click.stop
        class="w-5/12 bg-white border border-gray-100 rounded-xl"
      >
        <div class="border-b border-gray-100 p-5">İptal Sebebi Seçiniz</div>
        <ValidationObserver
          tag="form"
          ref="form"
          @submit="cancel"
          v-slot="{ invalid }"
          class="flex flex-col gap-3 p-5"
        >
          <ValidationProvider rules="required" v-slot="{ errors }">
            <select-primary
              class="border border-gray-100 rounded-xl"
              label="İptal Sebebi"
              :error="errors[0]"
              v-model="reason"
            >
              <option value="Tedarik Edememe">Tedarik Edememe</option>
            </select-primary>
          </ValidationProvider>

          <div class="text-xs font-light text-gray-500">
            Sipariş iptal edildiğinde, daha sonra tedarik edilemez. Lütfen iptal
            edeceğiniz siparişten emin olunuz.
          </div>
          <div>
            <button-primary
              title="Siparişi İptal Et"
              :loading="loading"
              :disabled="invalid"
            />
          </div>
        </ValidationObserver>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ["selected"],
  data() {
    return {
      reason: null,
      loading: false,
    };
  },
  methods: {
    cancel(event) {
      event.preventDefault();
      this.$refs.form.validate().then((success) => {
        if (success) {
          this.loading = true;
          setTimeout(() => {
            this.$axios
              .post("/v1/partner/orders/cancel/" + this.selected.id, {
                message: this.reason,
              })
              .then((response) => {
                if (response.data.status == "success") {
                  this.$emit("close");
                }
              });
          });
        }
      });
    },
  },
};
</script>
