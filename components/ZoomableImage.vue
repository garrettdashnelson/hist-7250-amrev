<template>
  <div>
    <div
      class="relative group cursor-pointer"
      @click="lightboxActive = true"
    >
      <nuxt-img class="absolute top-0 right-0 h-4 w-auto transition-opacity duration-300 ease-in-out m-2 xl:h-8 xl:m-4 xl:opacity-0 xl:group-hover:opacity-100" src="/icon-magnify.svg" alt="Maginfy icon" />
      <img
        v-if="type === 'static-external'"
        :src="src"
        :alt="alt"
        class="h-24 w-auto sm:h-[30vh]"
      >
      <nuxt-img
        v-if="type === 'static-local'"
        :src="src"
        :alt="alt"
        class="h-24 w-auto sm:h-[30vh]"
      />
      <img
        v-if="type === 'iiif'"
        :src="src + '/full/1920,/0/default.jpg'"
        :alt="alt"
        class="h-24 w-auto sm:h-[50vh]"
      >
    </div>
    <client-only>
      <div
        class="relative z-50"
        :class="lightboxActive ? 'block' : 'hidden'"
        role="dialog"
        aria-modal="true"
      >
        <div class="fixed inset-0 bg-white bg-opacity-95 transition-opacity" />

        <div class="fixed inset-0 z-10">
          <div class="relative transform transition-all w-full h-full">
            <button
              class="group absolute z-10 top-0 right-0 m-5"
              @click="lightboxActive = false"
            >
              <span class="flex justify-center items-center w-8 h-8 rounded-full overflow-hidden bg-cobalt text-4xl text-white font-thin bg-opacity-80 mx-auto mb-4 transition duration-150 group-hover:bg-opacity-100">
                &times;
              </span>
              <span class="sr-only">
                Close
              </span>
            </button>
            <div class="p-4 flex flex-col justify-center items-stretch w-full h-full max-h-screen">
              <div
                class="flex-grow relative w-full flex justify-center items-stretch"
              >
                <img
                  v-if="type === 'static-external'"
                  :src="src"
                  :alt="alt"
                  class="flex-grow relative w-full max-h-[80vh] object-contain object-center self-center"
                >
                <nuxt-img
                  v-if="type === 'static-local'"
                  :src="src"
                  :alt="alt"
                  class="flex-grow relative w-full max-h-[90vh] object-contain object-center self-center"
                />
                <div
                  v-if="type === 'iiif'"
                  class="flex-grow relative w-full"
                >
                  <open-seadragon
                    :id="viewerId"
                    :options="{
                      tileSources: [src],
                      prefixUrl: '/openseadragon/',
                      preserveViewport: false,
                      visibilityRatio: 1,
                      // minZoomLevel: 1,
                      defaultZoomLevel: 0,
                      sequenceMode: true,
                    }"
                  />
                </div>
              </div>
              <div
                v-if="caption"
                class="relative flex-shrink-0 w-full mt-auto prose prose-midnight pt-8"
                v-html="compiledCaption"
              ></div>
            </div>
          </div>
        </div>
      </div>
    </client-only>
  </div>
</template>

<script>
import { marked } from 'marked'
import { v4 as uuidv4 } from 'uuid'

export default {
  name: 'ZoomableImage',
  props: {
    type: {
      type: String
    },
    src: {
      type: String
    },
    alt: {
      type: String
    },
    caption: {
      type: String
    }
  },
  data () {
    return {
      viewerId: null,
      lightboxActive: false
    }
  },
  computed: {
    compiledCaption () {
      return this.caption ? marked(this.caption) : ''
    }
  },
  mounted () {
    this.viewerId = uuidv4()
  }
}
</script>
