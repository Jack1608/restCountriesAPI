<!-- HelloWorld.vue -->
<template>
  <div class="greetings">
    <FuzzySearch :countries="countries" @update-results="updateResults" @show-country="showCountryModal" />
    <table>
      <thead style="width: 100%;">
        <tr>
          <td colspan="13"><h2>List of All the Countries</h2></td>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td colspan="1" class="w-10"><h3>Flag</h3></td>
          <td colspan="2" class="w-10"><h3>Official Name</h3></td>
          <td colspan="2" class="w-10"><h3>2 Countries Code</h3></td>
          <td colspan="2" class="w-10"><h3>3 Countries Code</h3></td>
          <td colspan="3" class="w-30"><h3>Native Country Name</h3></td>
          <td colspan="2" class="w-30"><h3>Alternative Country Names</h3></td>
          <td colspan="1" class="w-10"><h3>Country Calling Code</h3></td>
        </tr>
        <tr v-for="country in paginatedCountries" :key="country.item.name.common">
          <td colspan="1">
            <img :src="country.item.flags.png" alt="Flag">
          </td>
          <td colspan="2">
            <a href="#" @click.prevent="showCountryModal(country.item)">{{ country.item.name.official }}</a>
          </td>
          <td colspan="2">
            {{ country.item.cca2 }}
          </td>
          <td colspan="2">
            {{ country.item.cca3 }}
          </td>
          <td colspan="3">
            <ul>
              <li v-for="(cCode, key) in country.item.name.nativeName" :key="key">
                <span class="capitalized">+ {{ key }}:</span>
                <b>Official</b>: {{ cCode.official }},
                <b>Common</b>: {{ cCode.common }}
              </li>
            </ul>
          </td>
          <td colspan="2">
            <ul>
              <li v-for="altSpelling in country.item.altSpellings" :key="altSpelling">
                {{ altSpelling }}
              </li>
            </ul>
          </td>
          <td colspan="1">
            {{ country.item.idd.root }}
          </td>
        </tr>
      </tbody>
    </table>
    <CountryModal v-if="selectedCountry" :show="showModal" :country="selectedCountry" @close="closeCountryModal" />
  </div>
</template>

<script>
import FuzzySearch from './FuzzySearch.vue';
import CountryModal from '../modal/CountryModal.vue';

export default {
  name: 'HelloWorld',
  components: {
    FuzzySearch,
    CountryModal
  },
  data() {
    return {
      countries: [],
      paginatedCountries: [],
      selectedCountry: null,
      showModal: false
    };
  },
  async created() {
    await this.getCountries();
  },
  methods: {
    async getCountries() {
      try {
        const getCountries = await fetch('https://restcountries.com/v3.1/all');
        const allCountries = await getCountries.json();
        this.countries = allCountries;
        console.log(this.countries.map(code => code.idd));
      } catch (error) {
        console.error(error);
      }
    },
    updateResults(results) {
      this.paginatedCountries = results;
    },
    showCountryModal(country) {
      this.selectedCountry = country;
      this.showModal = true;
    },
    closeCountryModal() {
      this.selectedCountry = null;
      this.showModal = false;
    }
  }
};
</script>

<style scoped>
h1 {
  font-weight: 500;
  font-size: 2.6rem;
  position: relative;
  top: -10px;
}

h3 {
  font-size: 1.2rem;
}

.greetings h1,
.greetings h3 {
  text-align: center;
}
thead{
  text-align: center;
}
tbody{
  text-align: center;
}

td{
  border: 1px solid;
  img{
    width: 30px;
  }
  ul{
    li{
      list-style-type: none;
    }
  }
}
.w-10{
  width: 10%;
}
.w-20{
  width: 20%;
}
.w-30{
  width: 30%;
}
b{
  font-weight: bold;
}
.capitalized{
  text-transform: uppercase;
  font-weight: bold;
}
@media (min-width: 1024px) {
  .greetings h1,
  .greetings h3 {
    text-align: center;
  }
  .greetings{
    width: 100%;
  }
}
</style>