<template>
  <div
    @click="$emit('close')"
    class="fixed top-0 left-0 z-30 bg-black/5 w-screen h-screen backdrop-blur-md flex items-center justify-center"
  >
    <div class="container mx-auto flex items-center justify-center">
      <div
        @click.stop
        class="w-[30%] bg-white border border-gray-100 rounded-xl"
      >
        <div
          class="flex items-center justify-center py-3 border-b border-gray-100"
        >
          <p>Markanızı Ekleyin</p>
        </div>
        <ValidationObserver
          tag="form"
          ref="form"
          @submit="send"
          v-slot="{ invalid }"
          class="flex flex-col gap-3 p-5"
        >
          <div
            v-if="form.error"
            class="bg-red-50 text-red-500 text-xs p-3 rounded-xl"
          >
            <span>{{ form.error }}</span>
          </div>
          <ValidationProvider rules="required" v-slot="{ errors }">
            <div class="border border-gray-100 rounded-xl">
              <input-primary
                :error="errors[0]"
                label="Marka Adınız"
                v-model="form.name"
              />
            </div>
          </ValidationProvider>
          <p class="text-xs font-light text-gray-500">
            Ürünlerinizi bu marka altında mağazanıza yüklemek istiyorsanız
            tanımlayabilirsiniz. Daha detaylı bilgi almak için tıklayınız.
          </p>
          <button-primary
            title="Markanızı Oluşturun"
            :loading="form.loading"
            :disabled="invalid"
          />
        </ValidationObserver>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      form: {
        error: "",
        loading: false,
        name: "",
      },
    };
  },
  methods: {
    send(event) {
      event.preventDefault();
      this.$refs.form.validate().then((success) => {
        if (success) {
          this.form.loading = true;
          this.$axios
            .post(
              "/v1/brand/create",
              {},
              {
                params: {
                  name: this.form.name,
                },
              }
            )
            .then((response) => {
              if (response.data.status == "success") {
                this.$emit("close");
              } else {
                this.form.error = response.data.message;
              }
            });
        }
      });
    },
  },
};
</script>
