<template>
  <header></header>

  <main>
    <div class="container my-5">
      <div class="p-5 text-center bg-body-tertiary rounded-3">
        <svg class="bi mt-4 mb-3" style="color: var(--bs-indigo);" width="100" height="100">
          <use xlink:href="#bootstrap"></use>
        </svg>
        <h1 class="text-body-emphasis">Upload an image and let the AI describe its contents</h1>
        <div class="d-inline-flex gap-2 mb-5">
          <input @change="handleImage" class="form-control" type="file" accept="image/*" />
        </div>
        <p class="col-lg-8 mx-auto fs-5 text-muted"><font-awesome-icon v-show="loading" icon="camera-rotate" flip
            size="4x" />{{ description }}</p>
        <div class="d-flex justify-content-center">
          <img class="img-fluid" :src="image" alt="" />
        </div>
      </div>
    </div>
  </main>
</template>

<style>

</style>

<script>
import ollama from 'ollama'

async function askOllama(imageCode) {
    const response = await ollama.chat({
        model: 'llava',
        messages: [{ role: 'user', content: 'What is in this picture?', images: [imageCode] }],
    });
    return response.message.content;
}
export default {
    name: "App",
    data() {
        return {
            image: null,
            description: '',
            loading: false,
        }
    },
    methods: {
        handleImage(e) {
            const selectedImage = e.target.files[0];
            this.createBase64Image(selectedImage);
        },
        createBase64Image(fileObject) {
            const reader = new FileReader();
            reader.onload = (e) => {
                this.loading = true;
                this.image = "";
                this.description = "";
                let base64Code = e.target.result.replace(/^data:image\/(png|jpeg|jpg);base64,/, '');
                askOllama(base64Code).then((response) => {
                    this.description = response;
                    this.image = e.target.result;
                    this.loading = false;
                });
            };
            reader.readAsDataURL(fileObject);
        },
    },
};
</script>
