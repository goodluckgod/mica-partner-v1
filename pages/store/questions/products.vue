<template>
  <div class="container mx-auto p-5 gap-5 flex flex-col">
    <p class="text-xl">Ürün Soruları</p>
    <div class="border border-gray-100 bg-white rounded-xl overflow-hidden">
      <div
        class="flex items-center justify-between bg-gray-50 border-b border-gray-100"
      >
        <div class="w-2/12 p-3 text-sm text-gray-500">Oluşturulma Tarihi</div>
        <div class="w-4/12 p-3 text-sm text-gray-500">Ürün Bilgileri</div>
        <div class="w-4/12 p-3 text-sm text-gray-500">Soru Detayı</div>
        <div class="w-2/12 p-3 text-sm text-gray-500">İşlem</div>
      </div>
      <div
        class="flex items-center justify-between font-light"
        :class="{ 'border-t border-gray-100': index != 0 }"
        v-for="(question, index) in questions"
        :key="index"
      >
        <div class="w-2/12 p-3 text-sm text-gray-500">
          {{
            new Date(question.createdat).toLocaleString("tr-TR", {
              day: "2-digit",
              month: "long",
              year: "numeric",
              hour: "2-digit",
              minute: "2-digit",
            })
          }}
        </div>
        <div class="w-4/12 p-3 text-sm text-gray-500">
          <div class="flex gap-3 items-center">
            <div
              class="w-[50px] h-[75px] bg-cover bg-center bg-no-repeat rounded-lg"
              :style="{
                'background-image':
                  'url(' +
                  $axios.defaults.baseURL +
                  '/images/product/' +
                  question.product.images[0].url +
                  ')',
              }"
            ></div>
            <div>
              <p>
                <span>{{ question.product.brand.name }}</span>
                <span>{{ question.product.title }}</span>
              </p>
              <p>{{ question.product.mainid }}</p>
            </div>
          </div>
        </div>
        <div class="w-4/12 p-3 text-sm text-gray-500">
          <p>{{ question.question }}</p>
          <p v-if="question.customer.firstname && question.customer.lastname">
            <span>{{ question.customer.firstname[0] }}**</span>
            <span>{{ question.customer.lastname[0] }}**</span>
          </p>
          <p v-else>*** ***</p>
        </div>
        <div class="w-2/12 p-3 text-sm text-gray-500 flex gap-3">
          <button
            class="h-12 px-5 w-full bg-amber-50 text-amber-500 rounded-xl"
            v-if="question.answers.length == 0"
            @click="questionvalue = question"
          >
            Cevapla
          </button>
          <button
            class="h-12 px-5 w-full bg-green-50 text-green-500 rounded-xl"
            v-else
            disabled
          >
            Cevaplandı
          </button>
          <button class="h-12 px-5 w-full bg-red-50 text-red-500 rounded-xl">
            Bildir
          </button>
        </div>
      </div>
    </div>
    <transition name="fade" mode="out-in">
      <modal-question-answer
        v-if="questionvalue"
        :question="questionvalue"
        @close="
          questionvalue = null;
          getquestions();
        "
      />
    </transition>
  </div>
</template>

<script>
export default {
  data() {
    return {
      questions: [],
      questionvalue: null,
    };
  },
  methods: {
    getquestions() {
      this.$axios
        .get("/v1/partner/questions", {
          params: {
            storeid: this.$auth.user.stores[0].id,
          },
        })
        .then((response) => {
          if ((response.data.status = "success")) {
            this.questions = response.data.data;
          }
        });
    },
  },
  mounted() {
    this.getquestions();
  },
};
</script>
