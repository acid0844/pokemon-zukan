<template>
  <div class="container">
    <div class="left-section">
      <!-- 検索バー -->
      <input type="text" v-model="searchQuery" placeholder="ポケモンを検索" class="search-input"/>
      <select v-model="selectedType" class="search-type-select">
        <option value="すべて">すべて</option>
        <option value="ノーマル">ノーマル</option>
        <option value="かくとう">かくとう</option>
        <option value="ひこう">ひこう</option>
        <option value="どく">どく</option>
        <option value="じめん">じめん</option>
        <option value="いわ">いわ</option>
        <option value="むし">むし</option>
        <option value="ゴースト">ゴースト</option>
        <option value="はがね">はがね</option>
        <option value="ほのお">ほのお</option>
        <option value="みず">みず</option>
        <option value="くさ">くさ</option>
        <option value="でんき">でんき</option>
        <option value="エスパー">エスパー</option>
        <option value="こおり">こおり</option>
        <option value="ドラゴン">ドラゴン</option>
        <option value="あく">あく</option>
        <option value="フェアリー">フェアリー</option>
      </select>

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
      pokemons: [],                // 取得したポケモン一覧を格納
      selectedPokemon: null,       // 選択中のポケモン（詳細表示用）
      searchQuery: '',             // 名前検索用の文字列
      selectedType: 'すべて',       // タイプ検索の選択状態（デフォルトは「すべて」）
      typeMap: {                   // 英語のタイプ名 → 日本語の変換マップ
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
    // 名前検索とタイプ選択に応じて、表示するポケモンを絞り込む
    filteredPokemons() {
      return this.pokemons.filter(poke => {
        const matchesName = poke.japaneseName.includes(this.searchQuery) // 名前に検索文字列が含まれているか
        const matchesType = this.selectedType === 'すべて' || poke.types.includes(this.selectedType) // 選択タイプと一致しているか
        return matchesName && matchesType
      })
    }
  },
  async mounted() {
    // 初期表示時にポケモン151匹を取得する

    // まず全151匹の基本情報を取得
    const res = await fetch('https://pokeapi.co/api/v2/pokemon?limit=151')
    const data = await res.json()

    // 各ポケモンに対して追加の詳細情報を取得して整形
    const fetchedPokemons = await Promise.all(
      data.results.map(async (pokemon, index) => {
        const id = index + 1 // ポケモンID（1から始まる）

        // 日本語名の取得
        const speciesRes = await fetch(`https://pokeapi.co/api/v2/pokemon-species/${id}/`)
        const speciesData = await speciesRes.json()
        const japaneseNameObj = speciesData.names.find(n => n.language.name === 'ja')

        // 詳細情報（タイプ、サイズなど）の取得
        const detailsRes = await fetch(`https://pokeapi.co/api/v2/pokemon/${id}/`)
        const detailsData = await detailsRes.json()

        // ポケモンオブジェクトを作成して返す
        return {
          id,
          japaneseName: japaneseNameObj ? japaneseNameObj.name : pokemon.name, // 日本語名があれば使う
          image: `https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${id}.png`, // 画像URL
          types: detailsData.types.map(type => this.typeMap[type.type.name] || type.type.name), // 日本語タイプ名に変換
          height: detailsData.height, // 高さ（デシメートル単位）
          weight: detailsData.weight  // 重さ（ヘクトグラム単位）
        }
      })
    )

    // ポケモン一覧を格納
    this.pokemons = fetchedPokemons

    // ⭐ 最初の1匹を選択状態にしておく（詳細表示エリア用）
    if (fetchedPokemons.length > 0) {
      this.selectedPokemon = fetchedPokemons[0]
    }
  },
  methods: {
    // ポケモンをクリックしたときに詳細情報を表示する
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
  /* padding-right: 20px; */
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

.search-type-select {
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
