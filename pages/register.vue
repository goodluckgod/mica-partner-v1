<template>
  <div>
    <ValidationObserver
      tag="form"
      ref="form"
      @submit="send"
      v-slot="{ invalid }"
      v-if="form.step == 'user'"
      class="flex flex-col gap-3"
    >
      <div
        v-if="form.error"
        class="bg-red-50 text-red-500 text-xs p-3 rounded-xl"
      >
        <span>{{ form.error }}</span>
      </div>
      <div class="grid grid-cols-2 gap-3">
        <ValidationProvider rules="required" v-slot="{ errors }">
          <div class="border border-gray-100 rounded-xl">
            <input-primary
              :error="errors[0]"
              label="Adınız"
              v-model="form.user.firstname"
            />
          </div>
        </ValidationProvider>
        <ValidationProvider rules="required" v-slot="{ errors }">
          <div class="border border-gray-100 rounded-xl">
            <input-primary
              :error="errors[0]"
              label="Soyadınız"
              v-model="form.user.lastname"
            />
          </div>
        </ValidationProvider>
      </div>
      <ValidationProvider rules="required" v-slot="{ errors }">
        <div class="border border-gray-100 rounded-xl">
          <input-primary
            :error="errors[0]"
            label="Telefon Numaranız"
            mask="+90 (5##) ### ####"
            v-model="form.user.phone"
          />
        </div>
      </ValidationProvider>
      <ValidationProvider rules="required|email" v-slot="{ errors }">
        <div class="border border-gray-100 rounded-xl">
          <input-primary
            :error="errors[0]"
            label="E-posta Adresiniz"
            v-model="form.user.email"
          />
        </div>
      </ValidationProvider>
      <ValidationProvider rules="required" v-slot="{ errors }">
        <div class="border border-gray-100 rounded-xl">
          <input-primary
            :error="errors[0]"
            label="Parolanız"
            type="password"
            v-model="form.user.password"
          />
        </div>
      </ValidationProvider>
      <ValidationProvider rules="required" v-slot="{ errors }">
        <div class="border border-gray-100 rounded-xl">
          <input-primary
            :error="errors[0]"
            label="Mağaza Adınız"
            v-model="form.store.name"
          />
        </div>
      </ValidationProvider>

      <div class="text-xs font-light text-gray-400 leading-tight">
        <p class="text-black">Oluşacak Mağaza Adresi</p>
        <p>
          www.dinazors.com/magaza/<span class="text-primary-600 font-normal">{{
            form.store.slug
          }}</span>
        </p>
      </div>
      <p class="text-xs font-light text-gray-500">
        Girişinizi doğrulamak için sizi arayacağız veya bir kısa mesaj
        göndereceğiz. Standart mesaj ve veri ücretleri uygulanır. Gizlilik
        Politikası
      </p>
      <button-primary
        title="Dinazors'a Katılın"
        :loading="form.loading"
        :disabled="invalid"
      />
    </ValidationObserver>
    <div v-else class="flex flex-col gap-5">
      <div>
        <p class="text-xl">Merhaba, {{form.user.firstname}}!</p>
        <p class="text-xs font-light text-gray-500">
          Dinazors'da satış yapabilmek için bazı önemli bilgilere ihtiyacımız
          var. Bu bilgilerin eksiksiz ve doğru olması hesap onaylanma sürenizi
          hızlandıracaktır.
        </p>
      </div>
      <ValidationObserver
        ref="registerform"
        tag="form"
        @submit="register"
        v-slot="{ invalid }"
        class="flex flex-col gap-3"
      >
        <div
          v-if="form.error"
          class="bg-red-50 text-red-500 text-xs p-3 rounded-xl"
        >
          <span>{{ form.error }}</span>
        </div>
        <ValidationProvider rules="required" v-slot="{ errors }">
          <div class="border border-gray-200 rounded-xl">
            <select-primary
              label="Şirket Türü"
              :error="errors[0]"
              v-model="form.company.type"
            >
              <option
                :key="index"
                v-for="(company, index) in companies"
                :value="company.value"
              >
                {{ company.label }}
              </option>
            </select-primary>
          </div>
        </ValidationProvider>
        <ValidationProvider rules="required" v-slot="{ errors }">
          <div class="border border-gray-200 rounded-xl">
            <input-primary
              :error="errors[0]"
              v-model="form.company.tax.number"
              :label="
                form.company.type == 'PRIVATE'
                  ? 'T.C. Kimlik Numarası'
                  : 'Vergi Numarası'
              "
              :mask="
                form.company.type == 'PRIVATE' ? '###########' : '##########'
              "
            />
          </div>
        </ValidationProvider>
        <ValidationProvider rules="required" v-slot="{ errors }">
          <div class="border border-gray-200 rounded-xl">
            <select-primary
              label="Vergi İli"
              v-model="form.company.tax.city"
              :error="errors[0]"
            >
              <option
                :key="index"
                v-for="(city, index) in cities"
                :value="city.id"
              >
                {{ city.name }}
              </option>
            </select-primary>
          </div>
        </ValidationProvider>
        <ValidationProvider rules="required" v-slot="{ errors }">
          <div class="border border-gray-200 rounded-xl">
            <select-primary
              label="Vergi Dairesi"
              v-model="form.company.tax.office"
              :error="errors[0]"
            >
              <option
                :key="index"
                v-for="(office, index) in offices"
                :value="office.code"
              >
                {{ office.name }}
              </option>
            </select-primary>
          </div>
        </ValidationProvider>
        <ValidationProvider rules="required" v-slot="{ errors }">
          <div class="border border-gray-200 rounded-xl">
            <select-primary
              label="Satış Yapılacak Ürün Kategorisi"
              v-model="form.category"
              :error="errors[0]"
            >
              <option
                :key="index"
                v-for="(category, index) in categories"
                :value="category.id"
              >
                {{ category.name }}
              </option>
            </select-primary>
          </div>
        </ValidationProvider>
        <button-primary
          title="Hesabımı Oluştur"
          :loading="form.loading"
          :disabled="invalid"
        />
      </ValidationObserver>
    </div>
  </div>
</template>

<script>
import cities from "@/assets/json/cities.json";
import offices from "@/assets/json/offices.json";
import companies from "@/assets/json/companies.json";
import categories from "@/assets/json/categories.json";

export default {
  layout: "authentication",
  data() {
    return {
      offices: [],
      cities: cities,
      companies: companies,
      categories: categories,
      form: {
        error: "",
        step: "user",
        loading: false,
        referance: this.$referance(6),
        user: {
          firstname: "",
          lastname: "",
          phone: "",
          email: "",
          password: "",
        },
        store: {
          name: "",
          slug: "",
        },
        category: null,
        company: {
          type: null,
          tax: {
            number: null,
            city: null,
            office: null,
          },
        },
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
            this.form.step = "company";
            this.form.loading = false;
          }, 1000);
        }
      });
    },
    register(event) {
      event.preventDefault();
      this.$refs.registerform.validate().then((success) => {
        if (success) {
          this.form.loading = true;
          setTimeout(() => {
            this.$axios
              .post("/v1/auth/register/partner", {
                ...this.form.user,
                store: this.form.store,
                company: this.form.company,
                category: this.form.category,
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
    tostorename(text) {
      const trMap = {
        ç: "c",
        Ç: "C",
        ğ: "g",
        Ğ: "G",
        ı: "i",
        İ: "I",
        ö: "o",
        Ö: "O",
        ş: "s",
        Ş: "S",
        ü: "u",
        Ü: "U",
      };
      return text
        .replace(/[çÇğĞıİöÖşŞüÜ]/g, (match) => trMap[match])
        .replace(/\s+/g, "-")
        .toLowerCase();
    },
    loadoffices(cid) {
      this.form.company.tax.office = null;
      this.offices = offices.filter((office) => office.cid == cid);
    },
  },
  watch: {
    "form.store.name": function (name) {
      if (name.includes("-") && name.split("-")[1].length === 8) {
        return;
      }
      this.form.store.slug =
        this.tostorename(name) +
        "-" +
        Array(8)
          .fill(0)
          .map(() => Math.random().toString(36).charAt(2))
          .join("");
    },
    "form.company.tax.city": function (city) {
      this.loadoffices(city);
    },
  },
};
</script>
