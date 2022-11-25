<template>
  <div>
    <div class="w-full">
      <div
        class="
          w-11/12
          md:w-1/3
          mx-auto
          border border-gray-700
          rounded-lg
          p-5
          mt-20
        "
      >
        <h2 class="font-bold text-xl text-center mb-6">Upload image</h2>
        <div>
          <input
            @change="onChange"
            name="image"
            type="file"
            class="w-full border rounded border-gray-700"
            accept="image/*"
          />
        </div>
        <div class="mt-5 text-center">
          <button
            class="
              w-full
              p-2
              text-white
              px-6
              border
              rounded
              bg-green-500
              border-gray-700
              max-w-[10rem]
            "
            @click="formSubmit"
          >
            Submit
          </button>
        </div>
      </div>
      <div
        v-if="this.message"
        class="w-1/3 mx-auto rounded-lg p-5 mt-5 text-green-500"
      >
        {{ this.message }}
      </div>
      <div
        v-if="this.apiError.length > 0"
        class="w-1/3 mx-auto rounded-lg p-5 mt-5 text-red-500"
      >
        <ul class="list-disc">
          <li v-for="(errors, index) in apiError" :key="`Error${index}`">
            {{ errors }}
          </li>
        </ul>
      </div>
      <div
        v-if="images.length > 0"
        class="p-5 mt-5 grid grid-cols-2 md:grid-cols-3 gap-3"
      >
        <div v-for="(data, index) in images" :key="index" class="w-15 m-auto">
          <img :src="data.image" :alt="`Image ${index}`" class="rounded-lg" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
const API_URL = "/api/image";

export default {
  name: "IndexPage",
  data() {
    return {
      uploadFile: "",
      apiError: [],
      message: "",
      images: [],
    };
  },
  async mounted() {
    this.getImages();
  },
  methods: {
    onChange(e) {
      const Files = e.target.files;
      this.uploadFile = Files[0];
    },
    async formSubmit() {
      this.apiError = [];
      this.message = '';
      const formData = new FormData();
      formData.append("image", this.uploadFile);

      const response = await this.$axios
        .$post(API_URL, formData, {
          Accept: "application/json",
          "Content-Type": "multipart/form-data",
        })
        .catch((errors) => {
          this.apiError = errors.response.data.errors.image;
        })
        .then((response) => {
          if (!this.apiError.length) {
            this.message = "Image Uploaded Successfull.";
          }
        });
      this.getImages();
    },
    async getImages() {
      const data = await this.$axios.$get(API_URL);
      this.images = data.reverse();
    },
  },
};
</script>
