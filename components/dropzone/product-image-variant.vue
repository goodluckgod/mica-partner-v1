<template>
  <div
    class="fixed top-0 left-0 h-screen w-screen bg-black/5 z-30 backdrop-blur-xl flex items-center justify-center"
    @click="$emit('close')"
  >
    <div class="container mx-auto flex items-center justify-center">
      <div
        @click.stop
        class="w-4/12 border border-gray-100 rounded-xl bg-white p-5"
      >
        <div class="flex flex-col gap-5">
          <p>Ürün Görseli Ekle</p>
          <form
            ref="dropzone"
            class="bg-gray-50 border h-[230px] max-h-[230px] relative p-5 flex items-center justify-center cursor-pointer border-gray-100 rounded-xl overflow-hidden"
            id="dropzone"
          >
            <div
              class="flex flex-col gap-5 absolute p-5 h-full w-full z-10"
              @click="$refs.dropzone.click()"
            >
              <div class="flex items-center justify-center">
                <icon-image-add class="fill-primary-500 h-10 w-10" />
              </div>
              <div class="flex flex-col gap-1 text-center text-sm">
                <p>
                  Görsellerinizi yüklemek için seçin veya sürükleyip bırakın.
                </p>
                <p class="text-xs font-light text-gray-500">
                  Görsel formatının PNG, JPG veya JPEG ve boyutunun minimum
                  860x574 olması gerekir.
                </p>
              </div>
              <div class="flex items-center justify-center">
                <div
                  class="h-10 bg-primary-500 text-black px-5 rounded-xl text-sm flex items-center"
                >
                  Görsel Seç
                </div>
              </div>
            </div>
          </form>
          <div
            v-if="images.length > 0"
            class="max-h-[350px] overflow-auto scrollbar pr-3"
          >
            <ul class="flex flex-col gap-3">
              <li
                v-for="(file, index) in images"
                :key="index"
                class="flex gap-2.5"
              >
                <img
                  :src="file.url"
                  alt="Yüklenen görsel"
                  class="uploaded-image border border-gray-100 rounded-lg"
                />
                <div class="flex flex-col justify-between">
                  <p class="text-sm font-medium leading-tight">
                    {{ file.filename }}
                  </p>
                  <div
                    class="flex flex-col gap-0.5"
                    v-if="file.status === 'uploaded'"
                  >
                    <p class="text-xs text-green-500">Yüklendi</p>
                    <div class="h-1 w-full bg-green-500 rounded-full"></div>
                  </div>
                  <div
                    class="flex flex-col gap-0.5"
                    v-else-if="file.status === 'unuploaded'"
                  >
                    <p class="text-xs text-red-500">
                      Yüklenemedi {{ file.errorMessage }}
                    </p>
                    <div class="h-1 w-full bg-red-500 rounded-full"></div>
                  </div>
                  <div class="flex flex-col gap-0.5" v-else>
                    <div class="h-1 w-full bg-gray-200 rounded-full"></div>
                  </div>
                </div>
              </li>
            </ul>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Dropzone from "dropzone";
import "dropzone/dist/dropzone.css";

export default {
  props: ["dropzoner"],
  data() {
    return {
      images: [],
    };
  },
  mounted() {
    this.initializeDropzone();
  },
  beforeDestroy() {
    if (this.dropzone) {
      this.dropzone.destroy();
    }
  },
  methods: {
    initializeDropzone() {
      const token = this.$auth.strategy.token.get();
      const component = this;
      this.dropzone = new Dropzone(this.$refs.dropzone, {
        url: this.$axios.defaults.baseURL + "/v1/products/image/upload",
        acceptedFiles: "image/jpeg,image/png",
        headers: {
          Authorization: token,
        },
        paramName: "photo",
        clickable: this.$refs.dropzone,
        init() {
          this.on("success", function (file, response) {
            component.images.push({
              filename: file.name,
              name: response.file.name,
              url: URL.createObjectURL(file),
              status: "uploaded",
            });

            let newimages = [];

            component.images.forEach((image) => {
              if (image.name) {
                newimages.push({
                  url: image.name,
                });
              }
            });

            component.$emit("images", newimages);
          });
          this.on("error", function (file, errorMessage, xhr) {
            component.images.push({
              filename: file.name,
              url: URL.createObjectURL(file),
              status: "unuploaded",
              errorMessage: errorMessage,
            });
          });
        },
      });
    },
  },
};
</script>

<style scoped>
.uploaded-image {
  max-height: 75px;
  margin-right: 10px;
}
.text-red-500 {
  color: #f56565;
}
</style>
