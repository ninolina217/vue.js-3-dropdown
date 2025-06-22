<script setup>
import { ref, computed, watch, defineProps, onMounted } from 'vue';

// Define props:
// - modelValue: the selected value, used with v-model and synced with the parent via emit
// - options: an array of categories passed from the parent page component

const { modelValue, options } = defineProps({
  modelValue: [String, Number, Object],
  options: {
    type: Array,
    default: () => [],
  },
});

const emit = defineEmits(['update:modelValue']);

const isOpen = ref(false);
const dropdownRef = ref(null);

// Handles opening and closing of the dropdown menu

function toggleDropdown() {
  isOpen.value = !isOpen.value;
}

// Emits the selected option ID to the parent component and closes the dropdown

function selectOption(option) {
  emit('update:modelValue', option.id);
  isOpen.value = false;
}

// Finds and returns the name of the selected subcategory to display as the button label

const selectedLabel = computed(() => {
  const found = options
    ?.flatMap((opt) => opt.children || [])
    .find((sub) => sub.id === modelValue);
  return found?.name || 'All categories';
});
</script>
<template>
  <div class="dropdown">
    <button
      @click="toggleDropdown"
      class="dropdown-button"
      :class="{ 'dropdown-button--active': isOpen }"
    >
      {{ selectedLabel }}
      <span class="arrow">{{ isOpen ? '▲' : '▼' }}</span>
    </button>

    <ul v-if="isOpen" class="dropdown-menu">
      <li v-for="option in options" :key="option.id">
        <div class="category-label">{{ option.name }}</div>
        <ul class="subcategory-list">
          <li
            v-for="subcategory in option.children"
            :key="subcategory.id"
            class="subcategory-item"
            @click="selectOption(subcategory)"
          >
            {{ subcategory.name }}
          </li>
        </ul>
      </li>
    </ul>
  </div>
</template>

<style scoped>
.arrow {
  float: right;
}
ul {
  list-style: none;
  padding: 0;
  margin: 0;
}

.align-row {
  display: flex;
  flex-direction: row;
  gap: 10px;
}

.dropdown {
  position: relative;
  display: inline-block;
  width: 300px;
  text-align: left;
}

.dropdown-button {
  width: 100%;
  padding: 8px 12px;
  text-align: left;
  border: 1px solid #ccc;
  background-color: white;
  cursor: pointer;
  color: black;
}

.dropdown-button--active {
  border-color: #007bff; /* Replace with your primary color */
}

.dropdown-menu {
  position: absolute;
  width: 100%;
  margin-top: 4px;
  border: 1px solid #ccc;
  background: white;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  max-height: 300px;
  overflow-y: auto;
  z-index: 10;
}

.category-label {
  font-weight: bold;
  padding: 8px 12px;
  background-color: #f9f9f9;
  cursor: default;
}

.subcategory-list {
  list-style: none;
  padding: 0;
  margin: 0;
}

.subcategory-item {
  padding: 8px 23px;
  cursor: pointer;
}

.subcategory-item:hover {
  background-color: #f0f0f0;
}
</style>
