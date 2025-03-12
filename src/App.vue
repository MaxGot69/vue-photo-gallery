<script setup>
import { ref, onMounted } from 'vue';
import PhotoTable from './components/PhotoTable.vue';

const photos = ref([]);
const albumIds = ref('');
const loading = ref(false);

const searchPhotos = async () => {
  loading.value = true;
  let url = 'https://jsonplaceholder.typicode.com/photos';

  if (albumIds.value.trim() !== '') {
    const ids = albumIds.value.split(' ').filter(id => id.trim() !== '');
    if (ids.length > 0) {
      url += '?' + ids.map(id => `albumId=${id}`).join('&');
    }
  }

  try {
    const response = await fetch(url);
    photos.value = await response.json();
  } catch (error) {
    console.error('Ошибка при загрузке данных:', error);
  } finally {
    loading.value = false;
  }
};

onMounted(searchPhotos);
</script>

<template>
  <div class="app-container">
    <h1>Photo Gallery</h1>
    <div class="filter-container">
      <input type="text" v-model="albumIds" placeholder="Введите ID альбомов через пробел">
      <button @click="searchPhotos">Поиск</button>
    </div>
    <div v-if="loading">Загрузка...</div>
    <PhotoTable v-else :photos="photos" :loading="loading" />
  </div>
</template>

<style scoped>
.app-container {
  text-align: center;
  font-family: sans-serif;
}

.filter-container {
  margin-bottom: 20px;
}
</style>
