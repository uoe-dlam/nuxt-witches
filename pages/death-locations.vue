<template>
  <loading-message v-if="loading" />
  <map-component
    v-else
    :pageInfo="pageInfo"
    :originalMarkers="originalMarkers"
    :filtersGeneralInfo="filtersGeneralInfo"
    :filterProperties="filterProperties"
    iconBehaviour="changing"
    :includeTimeline="false"
    :clustersOnInitial="false"
  >
  </map-component>
</template>

<script>
import { SPARQLQueryDispatcher } from "~/assets/js/SPARQLQueryDispatcher";
import APIDataHandler from "~/assets/js/APIDataHandler";
import json from "../big-query-output.json";
import FilteringMethods from "../assets/js/FilteringMethods";
import MapComponent from "../components/MapComponent.vue";
import LoadingMessage from "../components/LoadingMessage.vue";
import Swal from "sweetalert2";
import filterDescriptions from "../public/filterDescriptions.json";

definePageMeta({
  layout: "default",
});

export default {
  components: { MapComponent, LoadingMessage },
  data: () => ({
    pageInfo: {
      title: "Places of Death Map for Accused Witches",
      html: "",
      footer: "witches.is.ed.ac.uk",
      confirmButtonText: "Close",
      type: "info",
      showCloseButton: true,
    },
    sparqlUrl: "https://query.wikidata.org/sparql",
    wikiPages: [],
    queryOutput: json,
    loading: true,
    originalMarkers: [],
    filtersToFind: [
      ["socialClass", "changing"],
      ["occupation", "changing"],
    ],
    filtersGeneralInfo: {
      title: "Accused witch filters",
      filtersShowing: true,
    },
    filterProperties: {
      sex: {
        label: "Gender",
        description: "",
        descriptionShowing: false,
        filters: {
          male: {
            label: "Male",
            active: true,
            iconUrl: "/images/witch-single-blue.png",
          },
          female: {
            label: "Female",
            active: true,
            iconUrl: "/images/witch-single-orange.png",
          },
          unknown: {
            label: "Unknown",
            active: true,
            iconUrl: "/images/witch-single-BW.png",
          },
        },
        showing: true,
      },
      socialClass: {
        label: "Social Classification",
        description: "",
        descriptionShowing: false,
        filters: {},
        showing: false,
      },
      occupation: {
        label: "Occupations",
        description: "",
        descriptionShowing: false,
        filters: {},
        showing: false,
      },
      hasWikiPage: {
        label: "Wikipedia Page",
        description: "",
        descriptionShowing: false,
        filters: {
          hasWiki: {
            label: "Has wiki",
            active: true,
            iconUrl: "/images/witch-single-blue.png",
          },
          noWiki: {
            label: "No wiki",
            active: true,
            iconUrl: "/images/witch-single-orange.png",
          },
        },
        showing: false,
      },
    },
  }),
  computed: {
    totalWitches() {
      return this.originalMarkers.reduce((total, marker) => {
        return total + (marker.witches.length || 0);
      }, 0);
    },
    icons() {
      const { icons } = useIcons();
      return icons.value;
    },
  },
  methods: {
    loadWikiEntries: function () {
      const sparqlQuery = `SELECT DISTINCT ?item ?LabelEN ?page_title
            WHERE {
              ?item wdt:P4478 ?witch .
              ?article schema:about ?item ; schema:isPartOf <https://en.wikipedia.org/> ;  schema:name ?page_title .
              ?item rdfs:label ?LabelEN filter (lang(?LabelEN) = "en") .
            }`;

      const queryDispatcher = new SPARQLQueryDispatcher(this.sparqlUrl);
      queryDispatcher.query(sparqlQuery).then((result) => {
        for (let i = 0; i < result.results.bindings.length; i++) {
          let item = result.results.bindings[i];

          let wikiPage = {
            id: item.item.value,
            pageTitle: item.page_title.value,
          };

          this.wikiPages.push(wikiPage);
        }
      });
    },
    setMarkersIcons: function () {
      let Filtering = new FilteringMethods(this.filterProperties, "sex");

      for (let i = 0; i < this.originalMarkers.length; i++) {
        let marker = this.originalMarkers[i];
        [marker.markerIcon, marker.active] =
          Filtering.getMarkerStateIconDependant(marker);
      }
    },
    updatePageInfo() {
      this.pageInfo.html = `<div>This map communicates the recorded locations for accused witches’ places of death. These deaths are the result of <strong">execution under the charge of witchcraft</strong>. Although there were <strong>3212</strong> accused witches named, there are recorded places of death for only <stong>${this.totalWitches}<strong> of them. Many of these locations are recorded as precise sites of execution. There are another <strong>119</strong> accused witches who were executed without a recorded geographical location. Many of the accused witches were executed by being strangled and then burned. For most of the accused witches, the surviving documentation does not show their fate. Most of them were probably executed, but the records that would have shown this no longer survive.</div>`;
    },
    hasLocalStorageExpired: function () {
      let hours = 24; // Reset when storage is more than 24hours
      let now = new Date().getTime();
      let setupTime = localStorage.getItem("setupTime");

      return setupTime === null || now - setupTime > hours * 60 * 60 * 1000;
    },
    loadDataFromLocalStorage: function () {
      let allFilters = JSON.parse(localStorage.getItem("allFilters"));
      this.filterProperties.socialClass.filters = allFilters.socialClass;
      this.filterProperties.occupation.filters = allFilters.occupation;
    },
    loadData: async function () {
      this.loadWikiEntries();
      const icons = this.icons;
      const config = useRuntimeConfig();

      try {
        let response = await myFetch("/main.php?type=death");
        this.queryOutput = response;
      } catch (e) {
        Swal.fire({
          title: "Server Error",
          html: `<div>We are unable to connect to the server to pull in map info. Please refresh the page and try again. If this error persists, please contact <a href="mailto:${config.public.supportEmail}">${config.public.supportEmail}</a></div>`,
          footer: "witches.is.ed.ac.uk",
          confirmButtonText: "Close",
          type: "error",
          showCloseButton: true,
        });

        return;
      }

      let getData = new APIDataHandler(
        this.queryOutput,
        this.wikiPages,
        icons,
        null,
      );
      let filtersFound = null;

      [this.originalMarkers, filtersFound] = getData.loadAccussed(
        "placeOfDeath",
        this.filtersToFind,
      );

      this.filterProperties.socialClass.filters = filtersFound.socialClass;
      this.filterProperties.occupation.filters = filtersFound.occupation;

      this.setMarkersIcons();
      this.loading = false;

      // Update the pageInfo with the calculated number of witches
      this.updatePageInfo();
    },
  },
  mounted: function () {
    // Load descriptions from the JSON file
    Object.keys(filterDescriptions).forEach((key) => {
      if (this.filterProperties[key]) {
        this.filterProperties[key].description = filterDescriptions[key];
      }
    });
    this.loadData();
  },
};
</script>

<style></style>
