<template>
  <ValidationObserver
    tag="form"
    ref="form"
    @submit="send"
    v-slot="{ invalid }"
    class="flex flex-col gap-3"
  >
    <div
      v-if="form.error"
      class="bg-red-50 text-red-500 text-xs p-3 rounded-xl"
    >
      <span>{{ form.error }}</span>
    </div>
    <ValidationProvider rules="required|email" v-slot="{ errors }">
      <div class="border border-gray-100 rounded-xl">
        <input-primary
          :error="errors[0]"
          label="E-posta Adresiniz"
          v-model="form.email"
        />
      </div>
    </ValidationProvider>
    <ValidationProvider rules="required" v-slot="{ errors }">
      <div class="border border-gray-100 rounded-xl">
        <input-primary
          :error="errors[0]"
          label="Parolanız"
          type="password"
          v-model="form.password"
        />
      </div>
    </ValidationProvider>
    <p class="text-xs font-light text-gray-500">
      Girişinizi doğrulamak için sizi arayacağız veya bir kısa mesaj
      göndereceğiz. Standart mesaj ve veri ücretleri uygulanır. Gizlilik
      Politikası
    </p>
    <button-primary
      title="Giriş Yapın"
      :loading="form.loading"
      :disabled="invalid"
    />
  </ValidationObserver>
</template>

<script>
export default {
  layout: "authentication",
  data() {
    return {
      form: {
        loading: false,
        email: "",
        password: "",
      },
    };
  },
  methods: {
    send(event) {
      event.preventDefault();
      this.$refs.form.validate().then((success) => {
        if (success) {
          this.form.loading = true;
          setTimeout(() => {
            this.$axios
              .post("/v1/auth/login/partner", {
                email: this.form.email,
                password: this.form.password,
              })
              .then(async (response) => {
                this.form.loading = false;
                if (response.data.status == "success") {
                  await this.$auth.setUserToken(response.data.access_token);
                  await this.$auth.setUser(response.data.user);
                  this.$router.push("/");
                } else {
                  this.form.error = response.data.message;
                }
              });
          }, 1000);
        }
      });
    },
  },
};
</script>
