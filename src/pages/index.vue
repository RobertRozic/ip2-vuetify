<template>
  <div>
    <h1>Home</h1>
    <p>My project is about books</p>
    <p>{{books}}</p>
  </div>
</template>

<script setup>
import { ref, watch, onMounted } from 'vue';
import axios from 'axios';

const books = ref([]);
const page = ref(1);
const totalBooks = ref(0);
const perPage = ref(20);
const search = ref('');
const author = ref('');
const isLoading = ref(false);
const isLoadingAuthor = ref(false);
//const addDialog = ref(false);
//const successAlert = ref(false);

let _searchTimerId;

const getData = () => {
  let api = "https://api.nytimes.com/svc/books/v3/lists/best-sellers/history.json"
  axios.get(api, {
    params: {
      'api-key': 'jGZL1u0s3GnGPUHbWdzefYCYQGDX0zus',
      'offset': perPage.value * (page.value - 1),
      'title': search.value,
      'author': author.value
    }
  }).then((response) => {
    books.value = response.data.results;
    totalBooks.value = response.data.num_results;
    isLoading.value = false;
    isLoadingAuthor.value = false;
  });
};

const fetchEntriesDebounced = () => {
  clearTimeout(_searchTimerId);
  _searchTimerId = setTimeout(() => {
    getData();
  }, 500); /* 500ms throttle */
};

const searchEntries = () => {
  books.value = [];
  page.value = 1;
  fetchEntriesDebounced();
};

watch(page, () => {
  getData();
});

watch(search, (val) => {
  if (!val) {
    return;
  }
  isLoading.value = true;
  searchEntries();
});

watch(author, (val) => {
  if (!val) {
    return;
  }
  isLoadingAuthor.value = true;
  searchEntries();
});

onMounted(() => {
  console.log('created');
  getData();
});
</script>

<style scoped>
  h1 {
    color: blue;
  }
</style>
