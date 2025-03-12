<template>
  <div class="table-container">
    <table>
      <thead>
        <tr>
          <th @click="sortBy('id')">Ид</th>
          <th @click="sortBy('albumId')">Альбом</th>
          <th @click="sortBy('title')">Название</th>
          <th>Ссылка</th>
          <th>Миниатюра</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="photo in displayedPhotos" :key="photo.id">
          <td>{{ photo.id }}</td>
          <td>{{ photo.albumId }}</td>
          <td :title="photo.title">{{ truncatedTitle(photo.title) }}</td>
          <td><a :href="photo.url" target="_blank">Ссылка</a></td>
          <td><img :src="photo.thumbnailUrl" alt="Миниатюра"></td>
        </tr>
      </tbody>
    </table>

    <!-- Состояние загрузки -->
    <div v-if="loading" class="loading">Загрузка...</div>

    <!-- Сообщение, если нет данных -->
    <div v-if="!loading && displayedPhotos.length === 0">Нет данных для отображения.</div>

    <!-- Кнопка для подгрузки дополнительных данных -->
    <button v-if="hasMoreData" @click="loadMore">Загрузить еще</button>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';

const props = defineProps({
  photos: {
    type: Array,
    required: true
  },
  loading: {
    type: Boolean,
    default: false
  }
});

const sortByField = ref(null);
const sortAscending = ref(true);
const initialRows = ref(30);
const rowsToLoad = ref(20);
const displayedPhotos = ref([]);

const loadMore = () => {
  const currentLength = displayedPhotos.value.length;
  const nextPhotos = props.photos.slice(currentLength, currentLength + rowsToLoad.value);
  displayedPhotos.value.push(...nextPhotos);
};

const hasMoreData = computed(() => {
  return displayedPhotos.value.length < props.photos.length;
});

// Сортировка
const sortBy = (field) => {
  if (sortByField.value === field) {
    sortAscending.value = !sortAscending.value;
  } else {
    sortByField.value = field;
    sortAscending.value = true;
  }
};

// Транзакция заголовка
const truncatedTitle = (title) => {
  if (title.length > 20) {
    return title.substring(0, 20) + '...';
  }
  return title;
};

// Инициализация отображаемых фотографий
displayedPhotos.value.push(...props.photos.slice(0, initialRows.value));
</script>

<style scoped>
.table-container {
  width: 600px;
  height: 600px;
  overflow: auto;
  margin: 0 auto;
}

table {
  width: 100%;
  border-collapse: collapse;
}

th,
td {
  border: 1px solid #ddd;
  padding: 8px;
}

th {
  background-color: #f2f2f2;
  cursor: pointer;
}

td:nth-child(3) {
  max-width: 200px; /* Adjust as needed */
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.loading {
  text-align: center;
}
img {
  max-width: 50px;
}
</style>
