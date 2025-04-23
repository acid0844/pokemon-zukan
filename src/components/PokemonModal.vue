<template>
  <div class="modal-overlay" @click.self="onClose">
    <div class="modal-content">
      <button class="modal-close" @click="onClose">×</button>
      <img :src="pokemon.image" alt="pokemon image" />
      <h2>{{ pokemon.japaneseName }}</h2>
      <p>タイプ: {{ pokemon.types.join(', ') }}</p>
      <p>身長: {{ pokemon.height }}</p>
      <p>体重: {{ pokemon.weight }}</p>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    pokemon: Object,
    onClose: Function
  }
};
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
}

.modal-content {
  background: white;
  padding: 24px;
  border-radius: 10px;
  width: 90%;
  max-width: 500px;
  max-height: 80vh;
  overflow-y: auto;
  text-align: center;

  img {
    width: 100%;
    max-height: 200px;
    object-fit: contain;
    margin-bottom: 16px;
  }

  .modal-close {
    position: absolute;
    top: 10px;
    right: 16px;
    background: none;
    font-size: 24px;
    border: none;
    cursor: pointer;
  }
}
</style>
