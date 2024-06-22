<template>
  <div
    @click="$emit('close')"
    class="fixed top-0 left-0 z-30 bg-black/5 w-screen h-screen backdrop-blur-md flex items-center justify-center"
  >
    <div class="container mx-auto flex items-center justify-center">
      <ValidationObserver
        tag="form"
        ref="form"
        @click.stop
        @submit="send"
        v-slot="{ invalid }"
        class="w-5/12 bg-white border border-gray-100 rounded-xl"
      >
        <div class="px-5 py-3 border-b border-gray-100">
          <p class="font-medium">
            {{ question.question }}
          </p>
        </div>
        <div class="p-5 flex gap-5">
          <div
            class="min-w-[200px] h-[275px] rounded-xl bg-cover bg-center bg-no-repeat"
            :style="{
              'background-image':
                'url(' +
                $axios.defaults.baseURL +
                '/images/product/' +
                question.product.images[0].url +
                ')',
            }"
          ></div>
          <ValidationProvider
            class="w-full"
            rules="required"
            v-slot="{ errors }"
          >
            <div class="border border-gray-100 rounded-xl h-full">
              <input-textarea
                :error="errors[0]"
                autofocus="true"
                label="Yanıt"
                v-model="answer"
              />
            </div>
          </ValidationProvider>
        </div>
        <div class="p-5 pt-0">
          <button-primary
            title="Soruyu Yanıtla"
            :loading="loading"
            :disabled="invalid"
          />
        </div>
      </ValidationObserver>
    </div>
  </div>
</template>

<script>
export default {
  props: ["question"],
  data() {
    return {
      answer: null,
      loading: false,
    };
  },
  methods: {
    send(event) {
      event.preventDefault();
      this.$refs.form.validate().then((success) => {
        if (success) {
          this.loading = true;
          this.$axios
            .post("/v1/partner/questions/answer", {
              questionid: this.question.id,
              answer: this.answer,
            })
            .then((response) => {
              if (response.data.status == "success") {
                this.$emit("close");
              }
            });
        }
      });
    },
  },
};
</script>
