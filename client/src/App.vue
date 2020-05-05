<template>
  <div id="app">
    <h2>Travel Bucket List</h2>

    <country-select :countries="countries"/>

    <country-detail v-if="selectedCountry" :selectedCountry="selectedCountry">
    </country-detail>

    <button v-if="selectedCountry && !bucketList.some(country => country.name === selectedCountry.name) " v-on:click="addToBucketList">Add Country</button>

    <bucket-list :bucketList="bucketList"></bucket-list>
</div>

</template>

<script>
import CountryDetail from '@/components/CountryDetail.vue';
import BucketList from '@/components/BucketList.vue';
import CountrySelect from '@/components/CountrySelect';
import BucketService from '@/services/BucketService';
import {eventBus} from '@/main.js';

export default {
  name: 'App',
  data() {
    return {
      countries: [],
      selectedCountry: null,
      bucketList: []
    }
  },
  components: {
    'country-detail': CountryDetail,
    'bucket-list': BucketList,
    'country-select': CountrySelect
  },
    mounted(){
      this.getCountries();
      this.getBucketList();

      eventBus.$on('country-selected', (country) => {
        this.selectedCountry = country;
      })
      eventBus.$on('country-updated', (country) => {
        const index = this.bucketList.findIndex(bucketItem => bucketItem._id === country._id)
        this.bucketList.splice(index, 1, country)
      })
    },
    methods: {
      getCountries(){
        fetch("https://restcountries.eu/rest/v2/all")
        .then(res => res.json())
        .then(countries => this.countries = countries)
      },
      getBucketList(){
        BucketService.getBucketList()
        .then(bucketList => this.bucketList = bucketList)
      },
      addToBucketList(){
        const newListItem = {
          name: this.selectedCountry.name,
          flag:  this.selectedCountry.flag,
          visited: false
        }
        BucketService.postBucketListItem(newListItem)
        .then(newItem => this.bucketList.push(newItem))
      }
    }
}
</script>

<style>
.small-flag {
  height: 20px
}



</style>
