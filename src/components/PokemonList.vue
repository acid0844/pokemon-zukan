<template>
  <div>
    <h1>ポケモン図鑑（日本語）</h1>
    <input type="text" v-model="searchQuery" placeholder="ポケモンを検索" />

    <ul>
      <li v-for="poke in filteredPokemons" :key="poke.id" @click="showDetails(poke)">
        <img :src="poke.image" alt="pokemon image" width="100" height="100"/>
        {{ poke.japaneseName }}
      </li>
    </ul>

    <div v-if="selectedPokemon">
      <h2>{{ selectedPokemon.japaneseName }}の詳細</h2>
      <img :src="selectedPokemon.image" alt="pokemon image" width="150" height="150"/>
      <p>タイプ: {{ selectedPokemon.types.join(', ') }}</p>
      <p>高さ: {{ selectedPokemon.height / 10 }} m</p>
      <p>重さ: {{ selectedPokemon.weight / 10 }} kg</p>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      pokemons: [],
      selectedPokemon: null,
      searchQuery: ''
    }
  },
  computed: {
    filteredPokemons() {
      return this.pokemons.filter(poke =>
        poke.japaneseName.toLowerCase().includes(this.searchQuery.toLowerCase())
      )
    }
  },
  async mounted() {
    const res = await fetch('https://pokeapi.co/api/v2/pokemon?limit=10')
    const data = await res.json()

    const fetchedPokemons = await Promise.all(
      data.results.map(async (pokemon, index) => {
        const id = index + 1 // IDは1から始まる
        const speciesRes = await fetch(`https://pokeapi.co/api/v2/pokemon-species/${id}/`)
        const speciesData = await speciesRes.json()

        const japaneseNameObj = speciesData.names.find(n => n.language.name === 'ja')

        const detailsRes = await fetch(`https://pokeapi.co/api/v2/pokemon/${id}/`)
        const detailsData = await detailsRes.json()

        return {
          id,
          japaneseName: japaneseNameObj ? japaneseNameObj.name : pokemon.name,
          image: `https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${id}.png`,
          types: detailsData.types.map(type => type.type.name),
          height: detailsData.height,
          weight: detailsData.weight
        }
      })
    )

    this.pokemons = fetchedPokemons
  },
  methods: {
    showDetails(pokemon) {
      this.selectedPokemon = pokemon
    }
  }
}
</script>
