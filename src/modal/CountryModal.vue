<!-- CountryModal.vue -->
<template>
    <div v-if="show" class="modal-overlay" @click.self="close">
      <div class="modal-content">
        <h2>{{ country.name.official }}</h2>
        <img :src="country.flags.png" alt="Flag" />
        <p><strong>2 Country Code:</strong> {{ country.cca2 }}</p>
        <p><strong>3 Country Code:</strong> {{ country.cca3 }}</p>
        <p><strong>Country Calling Code:</strong> {{ country.idd.root }}</p>
        <h3>Native Country Names:</h3>
        <ul>
          <li v-for="(cCode, key) in country.name.nativeName" :key="key">
            <span class="capitalized">+ {{ key }}:</span>
            <b>Official</b>: {{ cCode.official }},
            <b>Common</b>: {{ cCode.common }}
          </li>
        </ul>
        <h3>Alternative Country Names:</h3>
        <ul>
          <li v-for="altSpelling in country.altSpellings" :key="altSpelling">
            {{ altSpelling }}
          </li>
        </ul>
        <button @click="close">Close</button>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    props: {
      show: {
        type: Boolean,
        required: true
      },
      country: {
        type: Object,
        required: true
      }
    },
    methods: {
      close() {
        this.$emit('close');
      }
    }
  };
  </script>
  
  <style scoped>
  .modal-overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .modal-content {
    background-color: white;
    padding: 20px;
    border-radius: 8px;
    width: 50%;
    max-width: 600px;
  }
  </style>
  