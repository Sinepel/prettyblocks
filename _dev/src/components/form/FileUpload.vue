<script setup>
import { defineComponent, onMounted, ref } from "vue"
import Icon from '../Icon.vue';
import Button from './../Button.vue'
import Dropzone from "dropzone"
import axios from 'axios'
// import 'dropzone/dist/basic.css'
// import 'dropzone/dist/dropzone.css'

const props = defineProps({
  id: {
    required: true,
    type: String
  },
  title: String,
  // text to display in dropzone box
  placeholder: {
    type: String,
    default: "Uploader un fichier"
  },
  // upload url
  uploadUrl: {
    type: String,
    default: ajax_urls.upload
  },
  path: {
    type: String,
    default: '$/prettyblocks/views/images'
  },
  default: {
    type: String,
    default: {
      url: 'https://via.placeholder.com/480x453px'
    }
  },
  modelValue: Object
})
defineComponent({
  Icon,
  Button
})
const emit = defineEmits(['update:modelValue', 'saveParent'])
const updateValue = (event) => {
  emit('update:modelValue', event)
  emit('saveParent')
}

onMounted(() => {

  Dropzone.autoDiscover = false;
  let element = document.getElementById(props.id)
  let dropzone = new Dropzone(element, {
    url: props.uploadUrl,
    method: "POST",
    paramName: "file",
    maxFiles: 1,
    uploadMultiple: false,
    sending: function (file, xhr, formData) {
      formData.append("path", props.path);
    }
  });

  dropzone.on('success', function (file, responseText) {
    props.modelValue = updateValue(responseText.imgs)
    file.previewElement.innerHTML = "";
    dropzone.removeAllFiles(true);
  })
})

const removeImg = () => {
  const params = {
    action: 'removeImage',
    state: props.modelValue,
    path: props.path,
    ajax_token: security_app.ajax_token
  }
  axios.post(props.uploadUrl, params).then((reponse) => reponse.data)
    .then((data) => {
      updateValue(props.default)
    })
}
</script>

<template>
  <div>
    <label for="price" class="block mb-2 text-sm font-medium text-gray-700">{{ title }}</label>
    <form ref="zone" class="dropzone p-2 rounded-lg" :id="id">
      <div class="dz-message" data-dz-message>
        <div class="flex flex-col items-center">
          <Icon name="UploadIcon" size="10" class="mb-2" />
          <div v-if="props.modelValue.url">
            <img :src="props.modelValue.url" width="200" alt="">
          </div>
          <div v-else>Upload a file</div>
        </div>
      </div>
    </form>
    <div class="text-center mt-2 outline-none focus:outline-none focus-visible:outline-none"
      v-if="props.modelValue.url && props.modelValue.url !== props.default.url">
      <Button class="focus:outline-none focus-visible:outline-none" type="secondary" @click="removeImg()"
        icon="TrashIcon">Supprimer</Button>
    </div>
  </div>
</template>

<style scoped>
.dropzone {
  @apply w-full relative;
  @apply border-2 border-gray-200 border-dashed
}
</style>
