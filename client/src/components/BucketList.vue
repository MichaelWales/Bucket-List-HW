<template lang="html">
  <div id="favourite_countries">
    <h2>Bucket List</h2>
    <ul>
      <li v-for="country in bucketList">{{country.name}} <img class="small-flag" :src="country.flag"/> <button v-on:click="updateList(country)">Visit!</button></li>
    </ul>
  </div>
</template>

<script>
import BucketService from '@/services/BucketService.js';
import {eventBus} from '@/main.js';

export default {
  name: 'bucket-list',
  props: ['bucketList'],
  methods: {
    updateList(country){
      const updatedItem = {
        visited: true
      }
      BucketService.updateList(country._id, updatedItem)
      .then(updatedCountry => eventBus.$emit('country-updated', updatedCountry))
    }
  }
}
</script>

<style lang="css" scoped>
</style>
