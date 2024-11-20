<template>
  <div>
    <div class="home-bg-div">
      <img class="home-bg" src="./assets/img/home_bg.jpg" />
      <div class="gradient"></div>
    </div>
    <div class="main-div">
      <div class="glossy-container"
        :class="{ 'h-400': showMakeDropdown, 'h-560': showModelDropdown, 'h-650': showResult }">
        <p class="home-title">Find any car here</p>
        <div class="first-dropdown-div">
          <p class="dropdown-inst">Select the year :</p>
          <Dropdown
            :url="'https://new.api.nexusautotransport.com/api/vehicles/years'"
            :year-select="true"
            :reset-selection="false"
            :dropdown-placeholder="'Select the year'"
            @year-selected="handleYearSelection"
          />
        </div>
        <transition name="dropdown-fade">
          <div v-if="showMakeDropdown" class="dropdown-div">
            <p class="dropdown-inst">Select the make :</p>
            <Dropdown
              :url="makeUrl"
              :make-select="true"
              v-if="showMakeDropdown"
              :reset-selection="resetMakeDropdown"
              :dropdown-placeholder="'Select the make'"
              @make-selected="handleMakeSelection"
            />
          </div>
        </transition>
        <transition name="dropdown-fade">
          <div v-if="showModelDropdown" class="dropdown-div">
            <p class="dropdown-inst">Select the model :</p>
            <Dropdown
              :url="modelUrl"
              :model-select="true"
              v-if="showModelDropdown"
              :reset-selection="resetModelDropdown"
              :dropdown-placeholder="'Select the model'"
              @model-selected="handleModelSelection"
            />
          </div>
        </transition>
        <transition name="dropdown-fade">
          <div v-if="showResult">
            <p class="result-title">
              Your selected car is :
            </p>
            <p class="result-text">
              {{ result }}
            </p>
          </div>
        </transition>
      </div>
    </div>
  </div>
</template>

<script>
import Dropdown from "./components/Dropdown.vue";

export default {
  components: { Dropdown },

  data() {
    return {
      showMakeDropdown: false,
      showModelDropdown: false,
      makeUrl: "",
      modelUrl: "",
      selectedYear: null,
      selectedMake: null,
      selectedModel: null,
      resetMakeDropdown: false,
      resetModelDropdown: false,
      showResult: false,
      resulr:''
    };
  },

  methods: {
    handleYearSelection(url, year) {
      // Update the make URL and reset other dropdowns
      this.makeUrl = url;
      this.selectedYear = year;
      this.showMakeDropdown = true;
      this.showModelDropdown = false;
      this.resetMakeDropdown = true;
      this.resetModelDropdown = true;
      this.showResult = false;

      // Allow re-rendering with reset flags
      this.$nextTick(() => {
        this.resetMakeDropdown = false;
        this.resetModelDropdown = false;
      });
    },

    handleMakeSelection(make) {
      this.selectedMake = make;
      this.modelUrl = `https://new.api.nexusautotransport.com/api/vehicles/models?year=${this.selectedYear}&make=${make}`;
      this.showModelDropdown = true;
      this.resetModelDropdown = true;
      this.showResult = false;

      this.$nextTick(() => {
        this.resetModelDropdown = false;
      });
    },

    handleModelSelection(model) {
      this.selectedModel = model;
      this.showResult = true;
      this.result = `${this.selectedYear} ${this.selectedMake} ${this.selectedModel}`;
    },
  },
};
</script>
