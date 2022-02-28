<script setup>
import { ref, computed} from 'vue'

const cityWithDistrict = ref([])
const searchText = ref('')
const modal = ref(false)
const suggestedInputs = computed(() => {
  return searchText.value !== ''
  ?  cityWithDistrict.value.filter(data => data.includes(searchText.value))
  :  ''
})
const setInput = (e) => {
  searchText.value = e
  modal.value = false
}

fetch('https://raw.githubusercontent.com/kurotanshi/mask-map/master/raw/area-location.json')
  .then(res => res.json())
  .then(data => {
    for (const {name: cityName, districts} of data) {
      for (const {name: districtName} of districts) {
        cityWithDistrict.value.push(`${cityName} ${districtName}`)
      }
    }
  })
</script>

<template>
  <div class="container">
    <div class="shadow" @click="modal = false"></div>
    <div class="map">
      <h1>臺灣各縣市行政區查詢</h1>
      <img src="https://i2.wp.com/taslifamily.org/wp-content/uploads/2019/10/taiwan.png?w=600" alt="">
    </div>
    <div class="search-info">
      <form class="search-form">
        <input type="text" class="search" placeholder="請輸入縣市名稱" v-model="searchText" @input="filterCity" @focus="modal = true">
        <div v-if="suggestedInputs && modal">
          <ul class="suggestions">
            <li v-for="suggestedInput in suggestedInputs" :key="suggestedInput" @click="setInput(suggestedInput)"> {{suggestedInput}} </li>
          </ul>
        </div>
      </form>
    </div>
  </div>
</template>

<style>
  html {
    box-sizing: border-box;
    background: #ffc600;
  }

  .container {
    display: flex;
    font-family: 'helvetica neue';
    font-size: 20px;
    font-weight: 200;
    justify-content: space-around;
  }

  .map{
    width: 40%;
  }

  h1{
    font-size: 50px;
    font-weight: bolder;
    margin: 40px auto;
    text-align: center;
  }

  img{
    width: 90%;
    margin-left: 30px;
  }

  .search-info{
    width: max-content;
    height: max-content;
    z-index: 1;
  }

  .search-form {
    margin: 40px auto;
  }

  input {
    width: 500px;
    text-align: center;
    border: 10px solid #F7F7F7;
    border-radius: 5px;
    font-size: 30px;
  }

  .suggestions {
    width: 520px;
    background-color: #eee;
    z-index: 1;
  }

  .suggestions li {
    padding: 5px 20px;
    cursor: pointer;
    border-top: 1px solid #aaa;
    margin: 2px 0;
  }

  .shadow {
    position: absolute;
    width: 100%;
    height: 100vh;
    z-index: 0;
  }
</style>