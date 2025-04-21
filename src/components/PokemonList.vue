<template>
  <div>
    <h1>ポケモン図鑑（日本語）</h1>
    <ul>
      <li v-for="poke in pokemons" :key="poke.id">
        <img :src="poke.image" alt="pokemon image" width="100" height="100"/>
        {{ poke.japaneseName }}
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      pokemons: []
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

        return {
          id,
          japaneseName: japaneseNameObj ? japaneseNameObj.name : pokemon.name,
          image: `https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${id}.png`
        }
      })
    )

    this.pokemons = fetchedPokemons
  }
}
</script>
