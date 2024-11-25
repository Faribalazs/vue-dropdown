<template>
  <div class="dropdown">
    <div :class="['select-menu', { active: isActive }]">
      <div class="select-btn" @click="toggleMenu">
        <span class="sBtn-text">
          {{ selectedOption || dropdownPlaceholder }}
        </span>
        <i class="ri-arrow-down-s-line"></i>
      </div>

      <ul v-if="isActive" class="options">
        <li class="search-option">
          <input
            v-model="searchQuery"
            class="search-input"
            type="text"
            placeholder="Search..."
            @input="filterOptions"
          />
        </li>
        <li
          v-for="(option, index) in options"
          :key="index"
          class="option"
          v-show="matchesSearch(option)"
          @click="selectOption(option)"
        >
          <span class="option-text">
            {{ optionText(option) }}
          </span>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  props: {
    url: {
      type: String,
      required: true,
    },
    yearSelect: {
      type: Boolean,
      default: false,
    },
    makeSelect: {
      type: Boolean,
      default: false,
    },
    modelSelect: {
      type: Boolean,
      default: false,
    },
    resetSelection: {
      type: Boolean,
      default: false,
    },
    dropdownPlaceholder: {
      type: String,
      required: true,
    },
    dropdownName: {
      type: String,
      required: true,
    },
  },

  data() {
    return {
      isActive: false,
      selectedOption: null,
      options: [],
      searchQuery: "",
    };
  },

  created() {
    this.fetchOptions();
    document.addEventListener("click", this.handleClickOutside);
  },

  beforeUnmount() {
    document.removeEventListener("click", this.handleClickOutside);
  },

  watch: {
    resetSelection(newValue) {
      if (newValue) {
        this.resetDropdown();
      }
    },

    isActive(newValue) {
      this.$emit("dropdown-status", newValue, this.dropdownName);
    }
  },

  methods: {
    //Fetch data from API
    async fetchOptions() {
      try {
        const response = await axios.get(this.url, {
          headers: {
            Accept: "application/json",
            "Content-Type": "application/json",
          },
        });
        this.options = response.data.data || [];
      } catch (error) {
        console.error("Failed to fetch options:", error);
      }
    },

    toggleMenu() {
      this.isActive = !this.isActive;
      if (this.isActive) {
        this.searchQuery = "";
      }
    },

    matchesSearch(option) {
      // Safely handle cases where optionText might not return a valid string
      const query = this.searchQuery.toLowerCase();
      const text = this.optionText(option).toLowerCase();
      return text.includes(query);
    },

    selectOption(option) {
      // Year select handle
      if (this.yearSelect) {
        this.$emit("year-selected", option);
        this.selectedOption = option;

        // Make select handle
      } else if (this.makeSelect) {
        this.$emit("make-selected", option.name);
        this.selectedOption = option.name;

        // Moodel select handle
      } else if (this.modelSelect) {
        this.$emit("model-selected", option.model);
        this.selectedOption = option.model;
      }
      this.isActive = false;
    },

    optionText(option) {
      // Dynamically handle display text based on dropdown type
      if (this.yearSelect) return String(option);
      if (this.makeSelect) return String(option.name || "");
      if (this.modelSelect) return String(option.model || "");
      return String(option);
    },

    handleClickOutside(event) {
      //Close the dropdown when clicked outside the dropdown
      if (!this.$el.contains(event.target)) {
        this.isActive = false;
      }
    },

    resetDropdown() {
      this.selectedOption = null;
      this.options = [];
      this.isActive = false;
      this.fetchOptions(); // Reload options if necessary
    },
  },
};
</script>
