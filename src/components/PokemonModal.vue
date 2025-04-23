<template>
  <div class="modal-overlay" @click.self="$emit('close')">
    <div class="modal-content">
      <button class="modal-close" @click="$emit('close')">×</button>
      <img :src="pokemon.image" alt="pokemon image" />
      <h2>{{ pokemon.japaneseName }}</h2>
      <p>タイプ: {{ pokemon.types.join(', ') }}</p>
      <p>身長: {{ pokemon.height / 10 }} m</p>
      <p>体重: {{ pokemon.weight / 10 }} kg</p>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    pokemon: Object
  }
}
</script>

<style scoped lang="scss">
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.6);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
  backdrop-filter: blur(2px);
  padding: 0 10px;
}

.modal-content {
  background: #fff;
  padding: 24px;
  width: 90%;
  max-width: 500px;
  border-radius: 10px;
  position: relative;
  max-height: 80vh;
  overflow-y: auto;
  text-align: center;
  
  @media screen and (max-width: 600px) {
    width: 75%; // ✅ ← スマホ時は90%の幅にする
    padding: 16px;
  }

  img {
    width: 100%;
    max-height: 200px;
    object-fit: contain;
    margin-bottom: 16px;
    
    @media screen and (max-width: 600px) {
      max-height: 150px;
      margin-bottom: 0;
    }
  }
}

.modal-close {
  position: absolute;
  top: 8px;
  right: 12px;
  font-size: 28px;
  font-weight: bold;
  line-height: 1;
  color: #333;
  background: #f2f2f2;
  border: none;
  border-radius: 50%;
  width: 36px;
  height: 36px;
  cursor: pointer;
  transition: background 0.3s ease;

  &:hover {
    background: #ddd;
  }
}
</style>
