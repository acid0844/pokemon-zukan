<template>
  <div>
    <h1>データ一覧</h1>
    <ul>
      <li v-for="item in items" :key="item.name">{{ item.name }}</li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      items: []
    }
  },
  mounted() {
    fetch('https://pokeapi.co/api/v2/pokemon?limit=10')
      .then(response => response.json())
      .then(data => {
        this.items = data.results
      })
  }
}
</script>
