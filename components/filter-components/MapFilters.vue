<template>
  <div
    class="xs:w-11/12 sm:w-1/2 lg:w-2/5 xl:w-1/3 z-20 left-0"
    :style="[
      timelineOn ? { height: '89%' } : { height: '100%' },
      filtersBox ? {} : { 'pointer-events': 'none' },
    ]"
  >
    <transition>
      <div class="w-full h-full flex" v-if="filtersBox">
        <!-- Filters box -->
        <div
          class="h-full flex flex-col bg-white rounded-tr-xl rounded-br-xl filters-shadow overflow-y-visible overflow-x-hidden relative"
          style="width: 90%"
        >
          <!-- Header -->
          <div
            class="flex w-full flex-col bg-white md:sticky md:top-0 md:z-10"
            ref="FiltersHeader"
          >
            <div
              class="flex flex-col w-full h-full"
              style="backdrop-filter: blur(1.5px)"
            >
              <!-- Title and info-->
              <div class="flex text-center mt-1">
                <h1
                  class="flex gap-1 items-baseline px-2 text-2xl sm:text-3xl md:text-4xl lg:text-5x"
                >
                  {{ pageInfo.title }}
                  <img
                    src="/images/infoIcon.svg"
                    class="box-border w-6 h-6 cursor-pointer"
                    alt="Page Information Popup"
                    @click="showPageInfo()"
                  />
                  <span class="visually-hidden">Page Information Pop Up</span>
                </h1>
              </div>

              <!-- Display number of active witches. -->
              <div class="ml-3 flex mt-3 items-center pb-1">
                <p class="mr-2 text-lg witchy-text mb-0">Showing</p>
                <span
                  class="h-6 px-1 flex items-center justify-center mr-2 border-2 rounded-md text-white font-semibold bg-slate-500 border-slate-700 mb-0"
                >
                  {{ noWitches }}
                </span>
                <p class="mr-1 text-lg witchy-text mb-0">Accused Witches</p>
              </div>
              <div
                class="ml-3 flex mt-1y items-center pb-2"
                v-if="!iconsConstant"
              >
                <p class="text-base witchy-text mr-2">
                  Colour of map icons indicating
                </p>
                <div
                  class="px-1 flex items-center justify-center mr-2 border-2 rounded-md text-white text-base bg-slate-500 border-slate-700 font-semibold"
                >
                  <span>
                    {{ filterProperties[currentProperty].label }}
                  </span>
                </div>
              </div>
            </div>
            <div class="w-full border mt-1"></div>
          </div>

          <!-- Timeline section -->
          <div class="ml-3 flex flex-col mt-4">
            <div
              class="flex items-center cursor-pointer"
              @click="toggleTimelineShowing()"
            >
              <h4 class="font-medium mr-3 py-0">Timeline</h4>
              <img
                v-if="timelineShowing"
                src="/images/arrow-up.svg"
                class="w-6 h-6"
              />
              <img v-else src="/images/arrow-down.svg" class="w-6 h-6" />
            </div>

            <transition name="slide-in">
              <timeline-range-selector
                v-if="timelineShowing"
                :key="timelineSelectorKey"
                @selectedDateRange="emitDateRange($event)"
                @deactivatedTimeline="deactivateTimeline()"
                @scrollHeaderIntoView="scrollHeaderIntoView()"
              >
              </timeline-range-selector>
            </transition>

            <button
              class="rounded-lg w-24 text-white ml-4 bg-sky-600 py-1 hover:bg-sky-700 text-sm"
              :style="[
                timelineOn
                  ? { 'margin-top': '20px' }
                  : { 'margin-top': '10px' },
              ]"
              @click="emitDateReset()"
              v-if="dateRange != null"
            >
              Reset Dates
            </button>

            <div
              v-if="dateRange !== null && timelineSelectorOn"
              class="flex flex-col ml-4 mt-4"
            >
              <div class="border" style="width: 300px"></div>
              <p class="text-lg witchy-text mt-2 ml-1">
                Showing accused witches between:
              </p>
              <div class="flex items-center mt-3 justify-center">
                <div
                  class="h-6 px-1 flex items-center justify-center mr-2 border-2 rounded-md text-white font-semibold bg-slate-500 border-slate-700"
                >
                  <p class="text-sm mb-0">
                    {{ dateRangeFormatted[0] }}
                  </p>
                </div>
                <p class="mr-2 text-lg witchy-text">and</p>
                <div
                  class="h-6 px-1 flex items-center justify-center mr-2 border-2 rounded-md text-white font-semibold bg-slate-500 border-slate-700"
                >
                  <p class="text-sm mb-0">
                    {{ dateRangeFormatted[1] }}
                  </p>
                </div>
              </div>
            </div>
          </div>

          <!-- Title for "witch filters" -->
          <div
            class="flex ml-3 flex-wrap items-center mt-2 cursor-pointer"
            :style="[
              includeTimeline
                ? { 'margin-top': '17px' }
                : { 'margin-top': '3px' },
            ]"
            @click="toggleFiltersShowing(property)"
          >
            <h4 class="font-medium">
              {{ filtersGeneralInfo.title }}
            </h4>
            <img
              src="/images/arrow-down.svg"
              v-if="!filtersGeneralInfo.filtersShowing"
              class="w-7 h-7 mt-1"
            />
            <img
              src="/images/arrow-up.svg"
              v-if="filtersGeneralInfo.filtersShowing"
              class="w-7 h-7 mt-1"
            />
          </div>

          <!-- Filter dropdowns -->
          <div v-if="filtersGeneralInfo.filtersShowing" class="w-full">
            <!-- Note that <propertyItem> denotes the object and 
              property the key. -->
            <div
              v-for="(propertyItem, property) in filterProperties"
              class="w-full flex flex-col ml-4"
            >
              <!-- Property titles -->
              <div
                class="flex pl-2 py-1 flex-wrap items-center mt-2 cursor-pointer w-full"
                @click="togglePropertyShowing(property)"
              >
                <div class="title-point"></div>
                <!--Descriptions-->
                <div class="tooltip">
                  <span class="label-and-icon">
                    <p style="font-weight: 500; display: inline" class="mr-1">
                      {{ propertyItem.label }}
                    </p>
                    <div
                      class="inline-flex items-center justify-center align-middle w-5 h-5 ml-1 mb-1 cursor-pointer"
                    >
                      <img
                        src="/images/questionIcon.svg"
                        aria-label="Filter Description"
                        class="pt-0.5 h-5 inline"
                      />
                      <span class="visually-hidden">Filter Description</span>
                    </div>
                  </span>
                  <span class="tooltiptext text-xs">
                    <h4 class="font-semibold mb-2">{{ propertyItem.label }}</h4>
                    <div v-html="propertyItem.description"></div>
                  </span>
                </div>
                <img
                  src="/images/arrow-down.svg"
                  v-if="!propertyItem.showing"
                  class="w-6 h-6"
                />
                <img
                  src="/images/arrow-up.svg"
                  v-if="propertyItem.showing"
                  class="w-6 h-6"
                />
              </div>
              <!-- Filters list if property is showing. -->
              <div v-if="propertyItem.showing" class="w-full">
                <div>
                  <button
                    @click="selectAll(property, propertyItem)"
                    class="inline-block rounded hover:bg-gray-300 text-black px-1 pb-1 pt-1 text-xs leading-normal border border-gray-200 hover:shadow-md hover:-translate-y-1 transform transition-all duration-200 focus:outline-none focus:ring focus:ring-blue-300 focus:ring-opacity-50"
                  >
                    Select All
                  </button>
                  <button
                    @click="clearAll(property, propertyItem)"
                    class="inline-block rounded hover:bg-gray-300 text-black px-1 pb-1 pt-1 text-xs leading-normal border border-gray-200 hover:shadow-md hover:-translate-y-1 transform transition-all duration-200 focus:outline-none focus:ring focus:ring-blue-300 focus:ring-opacity-50"
                  >
                    Clear All
                  </button>
                </div>

                <icon-dependent-filters-list
                  v-if="!iconsConstant"
                  :currentProperty="currentProperty"
                  :property="property"
                  :filterTypes="propertyItem.filters"
                  :propertyLabel="propertyItem.label"
                  @filterOff="emitFilterOff($event)"
                  @filterOn="emitFilterOn($event)"
                  @setPropertyToCurrent="setPropertyToCurrent($event)"
                >
                </icon-dependent-filters-list>

                <normal-filters-list
                  v-else
                  :property="property"
                  :filterTypes="propertyItem.filters"
                  @filterOff="emitFilterOff($event)"
                  @filterOn="emitFilterOn($event)"
                >
                </normal-filters-list>
                <NuxtLink
                  v-if="getGlossaryLabel(propertyItem.label)"
                  :to="{
                    path: '/glossary',
                    query: { category: getGlossaryLabel(propertyItem.label) },
                  }"
                  class="block font-sans text-gray-500 text-xs underline hover:text-gray-800 ml-2"
                >
                  {{ getGlossaryLabel(propertyItem.label) }} Glossary
                </NuxtLink>
              </div>
            </div>
          </div>

          <div
            class="self-end flex flex-col mt-3 mr-3 h-full justify-end"
            v-if="!iconsConstant"
          >
            <div class="flex items-center">
              <p class="text-sm mr-0.5">-</p>
              <img
                class="witch-icon mb-1 ml-1"
                src="public/images/witch-single-purple.png"
              />
              <p class="ml-1 text-sm">= Mixed.</p>
            </div>
          </div>

          <div
            class="flex flex-col justify-end"
            :style="[
              iconsConstant
                ? { 'margin-top': '15px', height: '100%' }
                : { 'margin-top': '0px', height: 'auto' },
            ]"
          >
            <div class="w-full border mt-3 mb-3"></div>

            <!-- Map tiles -->
            <div
              class="w-full flex justify-between px-2 sm:px-3 md:px-5 lg:px-10 mb-3"
            >
              <div v-for="tile in tiles">
                <input
                  type="radio"
                  name="tile"
                  :checked="tile.name === currentTileName"
                  @change="filterTiles(tile)"
                />
                {{ tile.name }}
              </div>
            </div>
          </div>
        </div>

        <!-- Left chevron to hide filters. -->
        <div class="w-8 flex flex-col justify-center ml-1 cursor-pointer">
          <div
            class="flex items-center justify-center w-8 h-8 hover:w-10 hover:h-10 rounded-full bg-slate-200 filters-shadow"
            @click="toggleFiltersBox()"
          >
            <img
              class="max-w-full max-h-full"
              src="/images/chevrons-left.svg"
            />
          </div>
        </div>
      </div>
    </transition>

    <!-- Right chevron to show filters. -->
    <div
      class="w-8 flex flex-col justify-center ml-1 h-full cursor-pointer"
      v-if="!filtersBox"
      style="pointer-events: auto"
    >
      <div
        class="flex items-center justify-center w-8 h-8 hover:w-10 hover:h-10 rounded-full bg-slate-200 filters-shadow"
        @click="toggleFiltersBox()"
      >
        <img class="max-w-full max-h-full" src="/images/chevrons-right.svg" />
      </div>
    </div>
  </div>
</template>

<script>
import TimelineRangeSelector from "./TimelineRangeSelector.vue";
import IconDependentFiltersList from "./IconDependentFiltersList.vue";
import NormalFiltersList from "./NormalFiltersList.vue";
import TimelineMethods from "../../assets/js/TimelineMethods";
import Swal from "sweetalert2";

export default {
  components: {
    TimelineRangeSelector,
    IconDependentFiltersList,
    NormalFiltersList,
  },
  props: {
    pageInfo: {
      type: Object,
      required: true,
    },
    startingFiltersGeneralInfo: {
      type: Object,
      required: true,
    },
    startingFilters: {
      type: Object,
      required: true,
    },
    iconBehaviour: {
      type: String,
      required: true,
    },
    includeTimeline: {
      type: Boolean,
      default: false,
    },
    timelineOn: {
      // Recieves from parent when timeline has been
      // activated by selecting a dateRange.
      type: Boolean,
      required: true,
    },
    dateRange: {
      required: false,
    },
    noWitches: {
      type: Number,
      required: true,
    },
  },
  data() {
    return {
      isTooltipVisible: false,
      timelineSelectorOn: false, // Set to true on mounted if includeTimeline.
      timelineSelectorKey: 0,
      filtersBox: true,
      currentTileName: "Modern Map",
      filtersGeneralInfo: JSON.parse(
        JSON.stringify(this.startingFiltersGeneralInfo),
      ),
      filterProperties: JSON.parse(JSON.stringify(this.startingFilters)),
      tiles: [
        {
          name: "Modern Map",
          url: "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
          active: true,
        },
        {
          name: "Historic Map",
          url: "https://mapseries-tilesets.s3.amazonaws.com/mapdata3/125140579/{z}/{x}/{y}.png",
          active: false,
        },
      ],
      currentProperty: "sex", // Determines the property the icons of which are showing.
      timelineShowing: true,
    };
  },
  methods: {
    toggleTooltip: function (propertyItem) {
      if (this.isMobileDevice) {
        propertyItem.descriptionShowing = !propertyItem.descriptionShowing;
      }
    },
    closeTooltip(propertyItem) {
      propertyItem.descriptionShowing = false;
    },
    setFilterInactive: function (property, filterType) {
      this.filterProperties[property].filters[filterType].active = false;
    },
    setFilterActive: function (property, filterType) {
      this.filterProperties[property].filters[filterType].active = true;
    },
    filterEmit: function (property, filterType) {
      let isActive = this.filterProperties[property].filters[filterType].active;

      if (isActive) {
        this.setFilterInactive(property, filterType);
        this.$emit("filterOff", [property, filterType]);
      } else {
        this.setFilterActive(property, filterType);
        this.$emit("filterOn", [property, filterType]);
      }
    },
    emitFilterOff: function (filterInfo) {
      this.$emit("filterOff", filterInfo);
    },
    emitFilterOn: function (filterInfo) {
      this.$emit("filterOn", filterInfo);
    },
    setPropertyToCurrent: function (property) {
      // Sets <property> as the current property, and
      // calls setAllIcons to change the icons accordingly.
      this.currentProperty = property;

      if (this.iconBehaviour !== "constant") {
        this.$emit("changeCurrentProperty", property);
      }
    },
    selectAll: function (property, propertyItem) {
      let filtersList = Object.keys(propertyItem.filters);

      filtersList.forEach((type) => {
        propertyItem.filters[type].active = true;
        let filterInfo = [property, type];
        this.$emit("filterOn", filterInfo);
      });
    },
    clearAll: function (property, propertyItem) {
      let filtersList = Object.keys(propertyItem.filters);

      filtersList.forEach((type) => {
        propertyItem.filters[type].active = false;
        let filterInfo = [property, type];
        this.$emit("filterOff", filterInfo);
      });
    },
    togglePropertyShowing: function (property) {
      // If the property <property> is not showing, sets to showing,
      // and calls setPropertyToCurrent to set the property as the
      // current property, and change the icons accordingly. If it is
      // showing, sets to not showing.

      if (!this.filterProperties[property].showing) {
        if (this.currentProperty !== property) {
          this.setPropertyToCurrent(property);
        }
        this.filterProperties[property].showing = true;
      } else {
        this.filterProperties[property].showing = false;
      }
    },
    toggleTimelineShowing() {
      this.timelineShowing = !this.timelineShowing;
    },
    filterTiles: function (tile) {
      this.currentTileName = tile.name;
      this.$emit("updatedTile", tile.url);
    },
    toggleFiltersShowing: function () {
      this.filtersGeneralInfo.filtersShowing =
        !this.filtersGeneralInfo.filtersShowing;
    },
    toggleFiltersBox: function () {
      this.filtersBox = !this.filtersBox;
    },
    toggleTimelineSelector: function () {
      this.timelineSelectorOn = !this.timelineSelectorOn;

      if (!this.timelineSelectorOn) {
        this.$emit("turnTimelineOff");
      }
    },
    emitDateReset: function () {
      // Re-redner timeline selector with its default values.
      this.timelineSelectorKey = !this.timelineSelectorKey;
      this.$emit("resetDates");
    },
    emitDateRange: function (dateRange) {
      this.$emit("selectedDateRange", dateRange);
    },
    deactivateTimeline: function () {
      this.$emit("deactivatedTimeline");
    },
    showPageInfo: function () {
      Swal.fire(this.pageInfo);
    },
    scrollHeaderIntoView() {
      const el = this.$refs.FiltersHeader;

      if (el) {
        el.scrollIntoView({ behavior: "smooth" });
      }
    },
    getGlossaryLabel: function (label) {
      if (
        label === "Gender" ||
        label === "Shapeshifting" ||
        label === "Wikipedia Page"
      ) {
        return null;
      }
      if (label === "Primary" || label === "Secondary") {
        return "Case Characterisations";
      }
      return label;
    },
  },
  computed: {
    dateRangeFormatted() {
      if (this.dateRange !== null) {
        return [
          TimelineMethods.formatDate(this.dateRange[0]),
          TimelineMethods.formatDate(this.dateRange[1]),
        ];
      }
    },
    iconsConstant() {
      return this.iconBehaviour === "constant";
    },
    isMobileDevice() {
      return window.innerWidth <= 768; // You can adjust the width as needed
    },
  },
  mounted: function () {
    if (this.includeTimeline) {
      this.timelineSelectorOn = true;
    }
  },
};
</script>

<style>
@tailwind base;

.p {
  @apply mb-0;
}

.label-and-icon {
  position: relative;
  display: inline-block;
}

.tooltip .tooltiptext::before {
  content: "";
  position: absolute;
  bottom: 100%;
  left: 10%;
  margin-left: -5px; /* Adjust based on the arrow's size */
  border-width: 5px;
  border-style: solid;
  border-color: transparent transparent rgb(223, 223, 223) transparent; /* Adjust the color as needed */
}

/*Links within the tooltip*/
.tooltip a {
  text-decoration: underline;
  color: rgb(0, 123, 255);
}

.tooltip .tooltiptext {
  display: none;
}

.tooltip .tooltiptext {
  visibility: hidden;
  max-width: 80%;
  background-color: rgb(255, 255, 255);
  color: #070707;
  text-align: center;
  padding: 10px;
  border-radius: 6px;
  box-shadow: 0px 2px 5px rgba(0, 0, 0, 0.2);
  position: absolute;
  z-index: 1;
}

.tooltip:hover .tooltiptext {
  display: block;
}

.label-and-icon:hover + .tooltiptext {
  visibility: visible;
}

/*Tooltip stays when hovering over tooltip"*/
.tooltip:hover .tooltiptext,
.tooltip.active .tooltiptext {
  visibility: visible;
}

.tooltip.active .tooltiptext {
  display: block;
}

.arrow-container {
  border-radius: 50%;
}

.arrow-icon {
  max-width: 100%;
  max-height: 100%;
}

.witch-icon {
  max-width: 20px;
  height: auto;
}

/* The container */
.container {
  cursor: pointer;
  font-size: 22px;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
  width: 15px;
  height: 15px;
  border-radius: 50%;
  background-color: #b5b5b5;
}

/* Hide the browser's default radio button */
.container input {
  position: absolute;
  opacity: 0;
  cursor: pointer;
}

/* Create a custom radio button */
.checkmark {
  width: 4px;
  height: 4px;
  border-radius: 50%;
  background: white;
}

/* On mouse-over, add a grey background color */
.container:hover {
  background-color: #eeb518e1;
}

.v-enter-active {
  animation: slide-in 0.4s ease-out;
}

.v-leave-active {
  animation: slide-in 0.4s reverse ease-out;
}

@keyframes slide-in {
  0% {
    transform: translateX(-100%);
  }
  100% {
    transform: translateX(0%);
  }
}
</style>
