<template>
  <div>
    <div id="main-content" class="pl-5 pr-5 pt-3 pb-3">
      <div class="flex">
        <h1>
          Places of Residence Visualised using ArcGIS Online
          <div
            class="inline-flex items-center justify-center align-middle w-6 h-6 hover:w-7 hover:h-7 mb-1.5 ml-1 cursor-pointer"
          >
            <img
              src="/images/infoIcon.svg"
              class="w-full h-full pt-0.5"
              aria-label="Page Information Popup"
              @click="showPageInfo()"
            />
            <span class="visually-hidden">Page Information Pop Up</span>
          </div>
        </h1>
      </div>
    </div>
    <div>
      <div class="embed-container-gismap">
        <iframe
          width="500"
          height="400"
          frameborder="0"
          scrolling="no"
          marginheight="0"
          marginwidth="0"
          title="Residence Location of Accused Scottish Witches (1563-1736)"
          src="//edinuniv.maps.arcgis.com/apps/Embed/index.html?webmap=59369467c3a24fbe8c9fbe1a31bd707f&extent=-12.3301,51.9848,3.8967,57.4271&zoom=true&previewImage=false&scale=true&search=true&searchextent=true&legend=true&disable_scroll=false&theme=light"
        ></iframe>
      </div>
    </div>
  </div>
</template>

<script>
import Swal from "sweetalert2";

definePageMeta({
  layout: "default",
});

export default {
  data: () => ({}),
  methods: {
    showPageInfo() {
      Swal.fire({
        title: "Places of Residence",
        html: '<div>This map shows the geographical residence location for each accused witch in Scotland taken from the Survey of Scottish Witchcraft Database. Out of the <b class="font-bold">3212</b> accused witches whose names are known, the residence for <b class="font-bold">3141</b> witches has been located. The majority of the residences are accurately located down to the precise settlement, while others range from parish to county depending on the records surviving for each accused witch. There is a total of 821 different locations recorded in the database; all but 25 of these have been identified. The remaining unidentified place-names are currently recorded as \‘County of’\ on the map.</div>',
        footer: "witches.is.ed.ac.uk",
        confirmButtonText: "Close",
        type: "info",
        showCloseButton: true,
      });
    },
  },
  computed: {},
  mounted() {},
  layout: "basic",
};
</script>

<style>
.embed-container-gismap {
  position: relative;
  padding-bottom: 80%;
  height: 0;
  max-width: 100%;
}

.embed-container-gismap iframe,
.embed-container-gismap object,
.embed-container-gismap iframe {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}

small {
  position: absolute;
  z-index: 40;
  bottom: 0;
  margin-bottom: -15px;
}
</style>
