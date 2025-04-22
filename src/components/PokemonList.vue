<template>
  <div class="container">
    <div class="left-section">
      <!-- 検索バー -->
      <input type="text" v-model="searchQuery" placeholder="ポケモンを検索" class="search-input"/>

    <div class="right-section">
      <!-- 詳細表示エリア -->
      <div v-if="selectedPokemon" class="pokemon-details">
        <h2>{{ selectedPokemon.japaneseName }}の詳細</h2>
        <img :src="selectedPokemon.image" alt="pokemon image" class="pokemon-detail-image"/>
        <p><strong>タイプ:</strong> {{ selectedPokemon.types.join(', ') }}</p>
        <p><strong>高さ:</strong> {{ selectedPokemon.height / 10 }} m</p>
        <p><strong>重さ:</strong> {{ selectedPokemon.weight / 10 }} kg</p>
      </div>
    </div>
      
      <!-- ポケモンリスト -->
      <div class="pokemon-list">
        <div
          v-for="poke in filteredPokemons"
          :key="poke.id"
          class="pokemon-card"
          :class="{ selected: selectedPokemon && selectedPokemon.id === poke.id }"
          @click="showDetails(poke)"
        >
          <img :src="poke.image" alt="pokemon image" class="pokemon-image"/>
          <div class="pokemon-name">{{ poke.japaneseName }}</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      pokemons: [],
      selectedPokemon: null,
      searchQuery: '',
      typeMap: {
        normal: 'ノーマル',
        fighting: 'かくとう',
        flying: 'ひこう',
        poison: 'どく',
        ground: 'じめん',
        rock: 'いわ',
        bug: 'むし',
        ghost: 'ゴースト',
        steel: 'はがね',
        fire: 'ほのお',
        water: 'みず',
        grass: 'くさ',
        electric: 'でんき',
        psychic: 'エスパー',
        ice: 'こおり',
        dragon: 'ドラゴン',
        dark: 'あく',
        fairy: 'フェアリー'
      }
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
    const res = await fetch('https://pokeapi.co/api/v2/pokemon?limit=151')
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
          types: detailsData.types.map(type => this.typeMap[type.type.name] || type.type.name), // 日本語に変換
          height: detailsData.height,
          weight: detailsData.weight
        }
      })
    )

    this.pokemons = fetchedPokemons

    // ⭐ ここで最初のポケモンを選択状態にする
    if (fetchedPokemons.length > 0) {
      this.selectedPokemon = fetchedPokemons[0]
    }
  },
  methods: {
    showDetails(pokemon) {
      this.selectedPokemon = pokemon
    }
  }
}
</script>

<style scoped>
.container {
  /* display: flex;
  justify-content: space-between; */
  margin: 0 auto;
  padding: 20px;
  font-family: Arial, sans-serif;
  max-width: 1200px; /* コンテンツ全体の幅 */
}

.left-section {
  flex: 1;
  padding-right: 20px;
}

.right-section {
  width: 350px; /* 詳細エリアの幅 */
  max-width: 100%;
  margin-bottom: 20px;
}

.search-input {
  padding: 10px;
  margin-bottom: 20px;
  width: 100%;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.pokemon-list {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(150px, 1fr));
  gap: 20px;
  padding: 20px;
  max-height: 500px; /* 高さを固定（リストが151匹以上ある場合にスクロールするように） */
  overflow-y: scroll; /* 縦スクロールを有効に */
}

.pokemon-card {
  padding: 20px;
  text-align: center;
  background-color: #f9f9f9;
  border: 1px solid #ddd;
  border-radius: 10px;
  cursor: pointer;
  transition: transform 0.2s;
}

.pokemon-card:hover {
  transform: scale(1.05);
}

.pokemon-card.selected {
  background-color: #e0e0e0; /* グレー系の色 */
  border: 2px solid #888;
}

.pokemon-image {
  width: 100px;
  height: 100px;
}

.pokemon-name {
  font-weight: bold;
  margin-top: 10px;
}

.pokemon-details {
  padding: 20px;
  background-color: #f9f9f9;
  border: 1px solid #ddd;
  border-radius: 10px;
  text-align: center;
}

.pokemon-detail-image {
  width: 150px;
  height: 150px;
  margin-bottom: 20px;
}
</style>
